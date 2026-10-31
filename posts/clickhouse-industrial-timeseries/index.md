# ClickHouse 在工业高频时序数据中的选型与落地实践


## 背景

工业场景对数据的挑战与互联网场景很不一样。一条大型产线上,千级到万级的测点(CPU 压力、辊缝、速度、温度……)以毫秒级采样周期持续写入,一天产生的数据行数在 **10 亿级**。如果再加上「质量溯源要回查任意时间窗的原始波形」这类需求,传统关系型数据库很快就撑不住了。

在之前的项目里,我就遇到了典型情况:全厂工艺参数毫秒级高频写入,查询侧既要支持数月跨度的趋势分析,又要支持秒级精度的细节回溯。用 MySQL 单表分片方案在测试阶段就已明显瓶颈:写入吞吐不足、历史表膨胀严重、大时间窗查询直接把库打满。

这篇文章记录当时做**采集与存储架构选型**的思路,以及落地 ClickHouse 后的经验和踩坑。

---

## 选型:为什么不是 MySQL、InfluxDB,而是 ClickHouse

选型主要对比三类方案,关注四个硬指标:写入吞吐、压缩比、查询延迟、运维成本。

| 方案 | 写入吞吐 | 压缩比 | 大时间窗聚合 | 主要顾虑 |
|------|----------|--------|--------------|----------|
| MySQL / 分表分库 | ❌ 低 | 差 | 慢,索引退化 | 亿级表迁移、DDL 困难 |
| InfluxDB 等专用时序库 | ✅ 高 | 好 | ✅ 快 | 多维度标签模型限制,SQL 生态弱 |
| **ClickHouse** | ✅ 极高(百万行/s 级) | ✅ 10~40x | ✅ 优秀 | 弱事务、不适合单点更新 |

**决定性因素**是工业数据的访问模式:

1. **写多改少** — 采集数据只追加、几乎不更新,天然契合 LSM/列存追加式引擎
2. **宽表稀疏** — 一行数据带几百个测点列,列式存储下未查询列几乎零开销
3. **聚合为主,点查为辅** — 分析场景是「某工序一个月内的均值/极值」,列存向量化执行优势巨大
4. **SQL 求生欲** — 工程团队和上层 BI 工具都会写 SQL,ClickHouse 的 SQL 兼容度和学习成本明显优于多数专用时序库

ClickHouse 的短板也必须正视:不支持高频单行 UPDATE/DELETE、**不保证强事务**、对高并发小查询(几毫秒级的仪表盘点查)并不擅长。这些缺口要靠架构设计来补,而不是寄望于数据库本身。

---

## 表结构设计

### 主表:按天分区 &#43; 排序键按测点聚类

```SQL
CREATE TABLE plant_process_data
(
    `ts`          DateTime64(3),                     -- 毫秒时间戳
    `line_id`     LowCardinality(String),           -- 产线,低基数
    `device_id`   LowCardinality(String),           -- 设备/单元
    `tag_code`    LowCardinality(String),           -- 测点编码
    `value`       Float64,
    `quality`     UInt8                              -- 质量戳:1 正常 / 2 替代 / 0 无效
)
ENGINE = MergeTree
PARTITION BY toDate(ts)
ORDER BY (line_id, device_id, tag_code, ts)
TTL toDateTime(ts) &#43; INTERVAL 12 MONTH
SETTINGS index_granularity = 8192;
```

几个关键设计:

- **LowCardinality 收窄** — 字符串列是列存的天然敌人,产线/测点这类枚举字段用 `LowCardinality` 后,压缩比和查询性能都有数量级改善
- **排序键把同类测点放在一起** — 业务查询几乎总是「某设备某测点的时间序列」,`ORDER BY (line, device, tag, ts)` 让一个设备的数据物理连续,磁盘顺序读
- **按天分区 &#43; TTL** — 查询天然带时间窗,分区裁剪直接跳过无关日期;冷热分层用 TTL 自动清理或滚动降精度
- **quality 质量戳** — 工业数据不是&#34;有就是好的&#34;,断裂、跳变、默认值都要带质量标记入库,这是后续数据清洗的根基

### 轻量物化视图做分钟级降采样

原始数据保留原始精度,聚合需求交给物化视图:

```SQL
CREATE MATERIALIZED VIEW plant_1min_mv TO plant_1min AS
SELECT
    ts - (toSecond(ts) % 60) SECOND AS ts,           -- 对齐整分
    line_id, device_id, tag_code,
    min(value) AS min_v, max(value) AS max_v,
    avg(value) AS avg_v,
    count()     AS cnt
FROM plant_process_data
GROUP BY ts, line_id, device_id, tag_code;
```

趋势/看板类查询全部走分钟表,原始表只服务「波形回溯」这类高精度需求。**数据密度差两个数量级,查询耗时也差两个数量级**。

---

## 写入链路优化

ClickHouse 的写入模式是「攒大批量」,这一点必须刻在写入侧的设计里:

1. **采集服务本地缓冲** — 采集程序(Python/C&#43;&#43;)先把数据写入 Kafka 或内存队列,按 `≥ 1 万行 / 2 秒` 的批量提交,严禁逐条 INSERT
2. **控制分区** — 只保留计算节点数 × 1~2 的写入分区数,每个分区内的并发写入过多会撑爆后台 Merge
3. **幂等与去重** — 采集断线重连导致的重复数据,用 `ReplacingMergeTree` &#43; 唯一排序键(设备&#43;测点&#43;纳秒级时间戳)在 Merge 阶段自动收敛,查询侧配 `FINAL` 或聚合近似
4. **回落策略** — ClickHouse 不可用时段数据不能丢,队列重放;**宁可延迟入库,不可丢测点**

落地后的实测结果:单节点常态写入延迟稳定在 **50ms 以内**,持续支撑全厂 7×24 高频写入,压力峰值时段也没有出现过 backlog 雪崩。

---

## 踩坑记录

- **`FINAL` 不能滥用** — 它会强制读时合并,拖垮查询性能。业务上能容忍短暂重复就先不加,靠后台 Merge 收敛
- **测试环境像真的一样测** — 一定要用真实密度和分布的数据压测,自造的均匀数据会把磁盘 IO 的真实表现掩盖掉
- **物化视图是同步计算** — 主表写入有多少行,MV 就实时聚合多少行,不要把重度计算塞进 MV,要做就「入库 → MV 轻聚合 → 异步重算」分层
- **备份策略单独做** — MergeTree 不像 MySQL 有成熟的主从备份体系,`clickhouse-backup` 工具 &#43; 对象存储要一开始就规划好
- **跳数索引精用** — `minmax`、`set` 这类二级索引只对真正高频的点查场景加,盲目全加反而拖慢写入

---

## 小结

| 关注点 | 实践做法 |
|--------|----------|
| 存储 | MergeTree &#43; 按天分区 &#43; 排序键按设备聚类 |
| 性能 | LowCardinality、批量写入、分钟级物化视图分流 |
| 可靠 | 质量戳、幂等去重、队列回落、独立备份 |
| 生态 | 纯 SQL 接 Layer BI/自研可视化,学习成本低 |

工业数据和互联网数据是两种形态:前者「写多改少、聚合为主、数据严格带语义」,后者更看重租户隔离和灵活访问。把数据库选型建立在**访问模式**而非流行度上,ClickHouse 在工业高频时序场景里几乎是现阶段的最佳答案。

如果你的场景中写入是实打实的百万行秒级、查询以时间窗聚合为主,不妨从 ClickHouse 开始。

---

**参考链接**

- [ClickHouse 官方文档](https://clickhouse.com/docs)
- [Altinity.ClickHouse-Backup(成熟备份方案)](https://github.com/Altinity/clickhouse-backup)


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/clickhouse-industrial-timeseries/  

