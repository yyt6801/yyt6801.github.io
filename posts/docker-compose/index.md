# Docker Compose与容器编排


## Docker Compose基础
- docker-compose.yml 结构解析（version、services、networks、volumes）
- 常用指令：build、ports、volumes、environment、depends_on

## 典型场景模板
### Web &#43; 数据库
- Nginx &#43; 后端 &#43; MySQL/PostgreSQL

### 微服务多容器
- 服务注册发现组合

### 开发环境
- 热重载挂载卷配置

## 生产环境考虑
- 健康检查（healthcheck）
- 资源限制（mem_limit、cpus）
- 日志配置（logging driver）
- .env 文件与环境管理

## Compose vs K8s
- 适用场景对比
- 从Compose迁移到K8s的路径


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/docker-compose/  

