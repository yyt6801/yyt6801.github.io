# Modbus协议简介与实践


## Modbus协议概述
- 历史与行业地位
- 主从架构模型

## 协议变种
### Modbus RTU
- 二进制编码、CRC校验
- RS-232/485物理层

### Modbus ASCII
- ASCII编码、LRC校验

### Modbus TCP
- 以太网传输、报文格式
- 端口502

## 报文结构详解
- 地址码、功能码、数据区、校验码
- 常用功能码速查（01~23）

## 寻址与数据模型
- Coil、Discrete Input、Input Register、Holding Register
- 地址映射规则

## 实践：Python与Modbus通信
- pymodbus库使用
- 读取/写入寄存器示例
- 异常处理

## 调试工具
- Modbus Poll/Slave
- 串口调试工具


---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/modbus-protocol/  

