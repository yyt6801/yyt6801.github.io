# 时序数据库入门：InfluxDB与TDengine


## 时序数据特点
- 写入量大、按时间排序、很少更新
- 与关系型数据库设计差异

## InfluxDB
### 核心概念
- Measurement、Tag、Field、Timestamp
- Retention Policy（保留策略）

### 常用操作
- 数据库创建与管理
- 数据写入与查询（InfluxQL/Flux）
- 连续查询与降采样

## TDengine
### 核心概念
- 超级表（STable）、子表
- 标签 vs 列

### 常用操作
- 建库建表
- 数据写入与SQL查询
- 窗口聚合与降采样

## 选型对比
- 性能、功能、社区、部署复杂度
- 工业场景推荐方案


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/influxdb-tdengine/  

