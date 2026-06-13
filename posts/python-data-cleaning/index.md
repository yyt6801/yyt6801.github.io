# Python工业数据清洗与处理


## 工业数据特点
- 噪声大：传感器漂移、通信丢失
- 时序相关：时间戳对齐、采样频率不一
- 数据量大：高频采集

## 数据读取
- CSV、Excel、Parquet格式
- 数据库读取（SQLAlchemy）
- 时序数据库读取（InfluxDB/TDengine客户端）

## 常见数据质量问题
### 缺失值处理
- 删除 vs 插值（前向填充、线性插值）
- 工业场景建议策略

### 异常值检测
- 统计方法（3σ、IQR）
- 基于阈值的工业经验方法

### 重复值与时间戳对齐
- 去重策略
- 重采样与对齐

## 数据变换
- 标准化与归一化
- 特征工程基础
- 滑动窗口特征构建

## 实战示例
- 从原始CSV到可用数据集的完整pipeline


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/python-data-cleaning/  

