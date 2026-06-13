# Redis缓存基础


## 数据结构与使用场景
- String：计数器、缓存
- Hash：对象存储
- List：消息队列、最新列表
- Set：标签、去重
- ZSet：排行榜、优先级队列
- HyperLogLog、Bitmap、GeoSpatial

## 持久化
- RDB vs AOF 对比
- 混合持久化
- 备份与恢复

## 高可用
- 主从复制
- 哨兵模式
- Redis Cluster 分片

## 常见问题
- 缓存穿透、缓存击穿、缓存雪崩
- 解决方案：布隆过滤器、互斥锁、热点数据永不过期

## 客户端使用
- redis-cli 常用命令
- Python连接（redis-py）
- 连接池配置


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/redis-cache/  

