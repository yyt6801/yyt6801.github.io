# DataX 完全使用指南：从入门到生产环境部署


DataX 是阿里巴巴开源的离线数据同步工具，广泛应用于阿里内部各种异构数据源的离线同步场景，每天处理 TB 级别的数据。它支持关系型数据库（MySQL、Oracle、PostgreSQL、SQL Server）、NoSQL（MongoDB、Redis）、大数据平台（HDFS、Hive、MaxCompute、ClickHouse）等数十种数据源，是国内数据仓库和数据湖建设中常用的数据迁移与同步工具。

**核心优势**：
- **开源免费**：基于 Apache 2.0 协议，无商业授权限制
- **异构支持**：内置数十种 Reader/Writer 插件，支持主流数据库和大数据组件
- **高性能**：支持多通道并行同步、批量读写、流量控制
- **运维友好**：提供命令行运行、Web 监控（DataX-Web）、错误数据重试机制
- **扩展性强**：基于插件架构，可自定义 Reader/Writer

---

## 快速上手

### 1. 下载与安装

从 [DataX GitHub 仓库](https://github.com/alibaba/DataX) 下载最新稳定版，或通过源码编译。

```bash
# 克隆仓库
git clone https://github.com/alibaba/DataX.git

# 进入目录并编译（需要 Maven 和 JDK 1.8&#43;）
cd DataX
mvn -U -X package -DskipTests
```

编译完成后，`target/datax/datax` 目录即为可运行版本，目录结构如下：

```
datax/
├── bin/                  # 启动脚本（datax.py / datax.bat）
├── job/                  # 任务 JSON 配置文件示例
├── libs/                 # 第三方依赖 JAR 包
├── plugin/               # Reader/Writer 插件
├── tmp/                  # 运行时临时目录
└── conf/                 # 配置文件
```

### 2. 运行第一个同步任务

DataX 任务以 JSON 格式编写，包含 `job` 级配置和 `content` 级 Reader/Writer 配置。以下是一个从 MySQL 同步到 ClickHouse 的最小示例：

**文件：`mysql2clickhouse.json`**

```json
{
  &#34;job&#34;: {
    &#34;setting&#34;: {
      &#34;speed&#34;: {
        &#34;channel&#34;: 3,
        &#34;byte&#34;: 10485760
      },
      &#34;errorLimit&#34;: {
        &#34;record&#34;: 100,
        &#34;percentage&#34;: 0.02
      }
    },
    &#34;content&#34;: [
      {
        &#34;reader&#34;: {
          &#34;name&#34;: &#34;mysqlreader&#34;,
          &#34;parameter&#34;: {
            &#34;username&#34;: &#34;root&#34;,
            &#34;password&#34;: &#34;123456&#34;,
            &#34;column&#34;: [&#34;id&#34;, &#34;name&#34;, &#34;create_time&#34;],
            &#34;splitPk&#34;: &#34;id&#34;,
            &#34;connection&#34;: [
              {
                &#34;table&#34;: [&#34;user_info&#34;],
                &#34;jdbcUrl&#34;: [&#34;jdbc:mysql://192.168.1.100:3306/test_db?useSSL=false&amp;serverTimezone=UTC&#34;]
              }
            ]
          }
        },
        &#34;writer&#34;: {
          &#34;name&#34;: &#34;clickhousewriter&#34;,
          &#34;parameter&#34;: {
            &#34;jdbcUrl&#34;: &#34;jdbc:clickhouse://192.168.1.101:8123/default&#34;,
            &#34;username&#34;: &#34;default&#34;,
            &#34;password&#34;: &#34;&#34;,
            &#34;table&#34;: &#34;user_info&#34;,
            &#34;column&#34;: [&#34;id&#34;, &#34;name&#34;, &#34;create_time&#34;],
            &#34;preSql&#34;: [&#34;TRUNCATE TABLE user_info&#34;],
            &#34;postSql&#34;: []
          }
        }
      }
    ]
  }
}
```

**执行同步**：

```bash
# Linux/macOS
python bin/datax.py mysql2clickhouse.json

# Windows（需安装 Python，或使用编译好的 datax.bat）
python bin\datax.py mysql2clickhouse.json
```

运行结束后，终端会输出任务统计信息：读取记录数、写入记录数、脏数据数、任务耗时等。

---

## 任务 JSON 配置详解

一个标准的 DataX 作业 JSON 分为 `job.setting` 和 `job.content` 两部分。

### job.setting —— 作业级配置

| 配置项 | 说明 | 默认值 | 必填 |
|--------|------|--------|------|
| `speed.channel` | 并行通道数（线程数） | 1 | 否 |
| `speed.byte` | 每秒最大字节数（流量控制） | 0（不限制） | 否 |
| `speed.record` | 每秒最大记录数（流量控制） | 0（不限制） | 否 |
| `errorLimit.record` | 最大允许脏数据记录数 | 0（不允许） | 否 |
| `errorLimit.percentage` | 最大允许脏数据占比（0~1） | 0.0 | 否 |

**示例**：

```json
&#34;setting&#34;: {
  &#34;speed&#34;: {
    &#34;channel&#34;: 5,
    &#34;byte&#34;: 52428800,
    &#34;record&#34;: 100000
  },
  &#34;errorLimit&#34;: {
    &#34;record&#34;: 1000,
    &#34;percentage&#34;: 0.01
  }
}
```

### job.content —— 数据内容配置

`content` 是一个数组，每个元素代表一个子任务，包含 `reader` 和 `writer`。

| 配置项 | 说明 |
|--------|------|
| `reader.name` | Reader 插件名称，如 `mysqlreader`、`oraclereader` |
| `reader.parameter` | Reader 连接与读取参数 |
| `writer.name` | Writer 插件名称，如 `clickhousewriter`、`hdfswriter` |
| `writer.parameter` | Writer 连接与写入参数 |

---

## 常用 Reader 与 Writer 参数模板

### 1. MySQL 读取（mysqlreader）

```json
{
  &#34;reader&#34;: {
    &#34;name&#34;: &#34;mysqlreader&#34;,
    &#34;parameter&#34;: {
      &#34;username&#34;: &#34;root&#34;,
      &#34;password&#34;: &#34;123456&#34;,
      &#34;column&#34;: [&#34;id&#34;, &#34;name&#34;, &#34;create_time&#34;, &#34;update_time&#34;],
      &#34;splitPk&#34;: &#34;id&#34;,
      &#34;where&#34;: &#34;create_time &gt;= &#39;2026-01-01&#39; AND is_deleted = 0&#34;,
      &#34;querySql&#34;: &#34;SELECT id, name, create_time FROM user_info WHERE create_time &gt;= &#39;${start}&#39;&#34;,
      &#34;connection&#34;: [
        {
          &#34;jdbcUrl&#34;: [&#34;jdbc:mysql://192.168.1.100:3306/test_db?useSSL=false&amp;serverTimezone=UTC&#34;],
          &#34;table&#34;: [&#34;user_info&#34;, &#34;order_info&#34;]
        }
      ]
    }
  }
}
```

| 参数 | 说明 |
|------|------|
| `username` / `password` | 数据库账号密码 |
| `column` | 读取字段列表，`*` 表示全部字段 |
| `splitPk` | 切分主键，用于多通道并行读取，建议为主键或有索引的列 |
| `where` | 附加过滤条件（与 querySql 二选一） |
| `querySql` | 自定义 SQL，适合复杂查询，与 `table` / `where` 互斥 |
| `connection.jdbcUrl` | JDBC 连接串 |
| `connection.table` | 表名列表 |

### 2. ClickHouse 写入（clickhousewriter）

```json
{
  &#34;writer&#34;: {
    &#34;name&#34;: &#34;clickhousewriter&#34;,
    &#34;parameter&#34;: {
      &#34;jdbcUrl&#34;: &#34;jdbc:clickhouse://192.168.1.101:8123/default&#34;,
      &#34;username&#34;: &#34;default&#34;,
      &#34;password&#34;: &#34;&#34;,
      &#34;table&#34;: &#34;user_info&#34;,
      &#34;column&#34;: [&#34;id&#34;, &#34;name&#34;, &#34;create_time&#34;],
      &#34;preSql&#34;: [&#34;TRUNCATE TABLE user_info&#34;],
      &#34;postSql&#34;: [&#34;OPTIMIZE TABLE user_info FINAL&#34;],
      &#34;batchSize&#34;: 1024
    }
  }
}
```

| 参数 | 说明 |
|------|------|
| `jdbcUrl` | ClickHouse JDBC 连接串 |
| `username` / `password` | 连接账号密码 |
| `table` | 目标表名 |
| `column` | 目标字段列表，顺序需与 Reader 输出一致 |
| `preSql` | 写入前执行的 SQL（如清空表） |
| `postSql` | 写入后执行的 SQL |
| `batchSize` | 批量写入大小，默认 1024 |

### 3. 其他常用插件参考

| 数据源 | Reader 插件名 | Writer 插件名 | 适用场景 |
|--------|--------------|--------------|----------|
| Oracle | `oraclereader` | `oraclewriter` | 关系型数据库迁移 |
| PostgreSQL | `postgresqlreader` | `postgresqlwriter` | 开源关系型数据库 |
| SQL Server | `sqlserverreader` | `sqlserverwriter` | 微软关系型数据库 |
| HDFS | `hdfswriter` / `hdfsreader` | `hdfswriter` | Hadoop 生态离线存储 |
| Hive | `hivereader` | `hivewriter` | 数据仓库 ETL |
| MaxCompute | `odpsreader` | `odpswriter` | 阿里云大数据平台 |
| MongoDB | `mongodbreader` | `mongodbwriter` | 文档型数据库 |
| Redis | `redisreader` | `rediswriter` | 缓存与键值存储 |
| Elasticsearch | `elasticsearchreader` | `elasticsearchwriter` | 全文检索 |

&gt; **提示**：各插件的完整参数说明可在 DataX 仓库的 `plugin/` 目录下各插件的 `README.md` 中查阅，或执行 `python bin/datax.py -r &lt;插件名&gt;` 查看参数帮助。

---

## 生产环境实战示例

### 场景：每日全量同步 MySQL 业务库到 ClickHouse 做 OLAP 分析

**任务配置文件：`daily_sync_mysql_to_ch.json`**

```json
{
  &#34;job&#34;: {
    &#34;setting&#34;: {
      &#34;speed&#34;: {
        &#34;channel&#34;: 5,
        &#34;byte&#34;: 0
      },
      &#34;errorLimit&#34;: {
        &#34;record&#34;: 5000,
        &#34;percentage&#34;: 0.005
      }
    },
    &#34;content&#34;: [
      {
        &#34;reader&#34;: {
          &#34;name&#34;: &#34;mysqlreader&#34;,
          &#34;parameter&#34;: {
            &#34;username&#34;: &#34;etl_user&#34;,
            &#34;password&#34;: &#34;xxx&#34;,
            &#34;column&#34;: [&#34;id&#34;, &#34;order_id&#34;, &#34;user_id&#34;, &#34;amount&#34;, &#34;create_time&#34;],
            &#34;splitPk&#34;: &#34;id&#34;,
            &#34;where&#34;: &#34;DATE(create_time) = DATE_SUB(CURDATE(), INTERVAL 1 DAY)&#34;,
            &#34;connection&#34;: [
              {
                &#34;table&#34;: [&#34;orders&#34;],
                &#34;jdbcUrl&#34;: [&#34;jdbc:mysql://mysql-prod:3306/biz_db?useSSL=false&amp;serverTimezone=UTC&#34;]
              }
            ]
          }
        },
        &#34;writer&#34;: {
          &#34;name&#34;: &#34;clickhousewriter&#34;,
          &#34;parameter&#34;: {
            &#34;jdbcUrl&#34;: &#34;jdbc:clickhouse://ch-cluster:8123/ods&#34;,
            &#34;username&#34;: &#34;default&#34;,
            &#34;password&#34;: &#34;&#34;,
            &#34;table&#34;: &#34;ods_orders&#34;,
            &#34;column&#34;: [&#34;id&#34;, &#34;order_id&#34;, &#34;user_id&#34;, &#34;amount&#34;, &#34;create_time&#34;],
            &#34;preSql&#34;: [&#34;ALTER TABLE ods_orders DELETE WHERE toDate(create_time) = toDate(yesterday())&#34;],
            &#34;postSql&#34;: [],
            &#34;batchSize&#34;: 5000
          }
        }
      }
    ]
  }
}
```

**定时调度**：可将上述 JSON 配合系统定时任务（Linux crontab）或调度平台（Airflow、Azkaban、XXL-JOB）每日凌晨执行：

```bash
# crontab 示例：每天凌晨 2 点执行
0 2 * * * /usr/bin/python /opt/datax/bin/datax.py /opt/datax/job/daily_sync_mysql_to_ch.json &gt;&gt; /var/log/datax/daily_sync_$(date &#43;\%Y\%m\%d).log 2&gt;&amp;1
```

---

## 性能优化与调优

### 1. 并行度调优

| 优化项 | 建议 | 说明 |
|--------|------|------|
| `speed.channel` | 设置为 CPU 核数的 1~2 倍 | 过高会增加数据库压力，过低浪费资源 |
| MySQL `splitPk` | 必须指定有索引的主键或唯一列 | 未指定时 DataX 会退化为单通道串行读取 |
| ClickHouse `batchSize` | 2000~10000 | 过小增加交互次数，过大增加内存占用 |

### 2. 流量控制

为避免同步任务占满生产数据库带宽，建议限制写入速率：

```json
&#34;speed&#34;: {
  &#34;channel&#34;: 3,
  &#34;byte&#34;: 10485760,
  &#34;record&#34;: 20000
}
```

### 3. 错误数据处理

开启脏数据容错，避免单条异常数据导致整个任务失败：

```json
&#34;errorLimit&#34;: {
  &#34;record&#34;: 1000,
  &#34;percentage&#34;: 0.01
}
```

DataX 会将脏数据写入 `tmp/` 目录下的错误文件，供后续人工核对和重试。

### 4. 字段类型映射注意事项

不同数据库的字段类型可能不完全对应，常见问题如下：

| 源数据库 | 目标数据库 | 常见问题 | 解决方案 |
|----------|------------|----------|----------|
| MySQL `DATETIME` | ClickHouse `DateTime` | 时区不一致 | JDBC URL 添加 `serverTimezone=UTC`，ClickHouse 表使用 `DateTime(&#39;UTC&#39;)` |
| MySQL `DECIMAL` | ClickHouse `Decimal` | 精度丢失 | 确保目标表精度与源一致 |
| Oracle `NUMBER` | MySQL `BIGINT` | 溢出 | 转换前评估数值范围，必要时拆分字段 |

---

## 离线环境部署与运维

在无法访问互联网的内网或离线环境中部署 DataX，需准备以下内容：

### 1. 离线安装包准备

在有网机器编译打包后，将整个 `target/datax` 目录拷贝到目标机器：

```bash
# 有网机器
mvn -U -X package -DskipTests
tar -czf datax-offline.tar.gz target/datax/

# 拷贝到离线机器并解压
tar -xzf datax-offline.tar.gz
```

### 2. 依赖 JAR 包离线放置

部分 Reader/Writer 依赖的第三方 JDBC 驱动不在默认 `libs/` 中，需手动放置：

| 数据源 | 驱动 JAR 示例 | 放置目录 |
|--------|--------------|----------|
| MySQL | `mysql-connector-java-8.0.x.jar` | `plugin/mysqlreader/libs/` |
| Oracle | `ojdbc8.jar` | `plugin/oraclereader/libs/` |
| ClickHouse | `clickhouse-jdbc-x.x.x.jar` | `plugin/clickhousewriter/libs/` |

### 3. 日志与监控

- **运行日志**：默认输出到控制台，可重定向到文件，或通过 `-log` 参数指定日志级别
- **DataX-Web**：社区提供的 Web 管理界面，支持可视化配置任务、查看运行状态、重试脏数据，适合团队共用
- **错误文件**：脏数据写入 `${jobName}.error.log`，位于 `tmp/` 目录

### 4. 常见问题排查

| 现象 | 可能原因 | 解决方案 |
|------|----------|----------|
| `ClassNotFoundException` | 缺少 JDBC 驱动 JAR | 将对应驱动放入插件 `libs/` 目录，重启 DataX |
| `Access denied for user` | 数据库账号密码错误或权限不足 | 核对账号密码，确认源库授权了远程连接 |
| `Communications link failure` | 网络不通或防火墙拦截 | 检查端口（3306/8123 等）、安全组、防火墙规则 |
| 任务运行极慢 | 通道数过低或未指定 `splitPk` | 提高 `channel`，为 MySQL Reader 指定有索引的 `splitPk` |
| 脏数据过多 | 字段类型不匹配或数据非法 | 检查源表与目标表的字段类型映射，清洗源数据 |

---

## 最佳实践建议

1. **全量与增量分离**：全量同步使用 `table` &#43; `where` 时间范围；增量同步建议使用 `querySql` 结合水位字段（如 `update_time &gt; last_sync_time`）。
2. **生产环境限流**：同步生产库时务必设置 `speed.byte` 或 `speed.record`，避免影响在线业务。
3. **目标表预处理**：ClickHouse 等列式存储建议在 `preSql` 中执行分区清理，避免重复数据。
4. **任务拆分**：大表建议按时间或主键范围拆分为多个 DataX 任务并行执行，降低单任务风险。
5. **配置版本化**：将所有任务 JSON 文件纳入 Git 版本管理，便于回滚和审计。
6. **敏感信息脱敏**：生产环境中数据库密码建议通过环境变量或加密配置注入，避免明文写在 JSON 中。

---

## 参考链接

- [DataX 官方 GitHub 仓库](https://github.com/alibaba/DataX)
- [DataX 官方文档（阿里云）](https://help.aliyun.com/document_detail/149509.html)
- [DataX-Web 项目](https://github.com/DataX-Web/DataX-Web)
- [ClickHouse JDBC 驱动](https://github.com/ClickHouse/clickhouse-jdbc)

---

**编者注**：本文基于 DataX 开源项目整理，实际使用中建议结合具体数据源版本和生产环境进行压力测试后再上线。

---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/datax-complete-guide/  

