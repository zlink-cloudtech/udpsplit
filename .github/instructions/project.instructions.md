---
applyTo: '**'
description: 项目概要说明，描述项目需求，项目相关方，规范和其他重要信息
---

本项目是一个UDP分发工具，旨在实现UDP数据的高效传输和分发。项目包含client和server两个主要组件，支持多会话代理和目标地址转发功能。

## 需求

1. 支持client和server模式
2. client和server用udp传输数据
3. client和server建立的udp session存在多个代理会话，即 server可以配置端口范围，client需要与它一致
4. server会有个target配置，表示server收到数据后需要转发的目标地址
5. 当client收到数据时，会创建stream，并把数据轮询到建立的多个代理会话中发送到server
6. 当server从代理会话中收到数据时，会根据target将数据转发到目标地址
7. server转发数据时，如果是不同的stream会用不同的源端口发送到target
8. 支持简单的命令行参数配置client和server的相关参数
9. 仅需要支持amd64架构的linux系统

## 技术栈

- 编程语言：Go

## 项目规范

### 目录结构

项目采用标准的Go项目布局：

```
.
├── cmd/
│   └── udpsplit/
│       └── main.go          # 主程序入口
├── go.mod                   # Go模块定义
├── go.sum                   # Go依赖校验文件
├── README.md                # 项目说明文档
├── udpsplit                 # 编译后的二进制文件
```