---
applyTo: "**"
description: 记录和跟踪项目开发计划和进度的说明文件，每次对任务进度的更新都必须记录在这里，包括新增任务、删除任务和任务完成情况。
---

# 进度追踪

## 2025-11-05
- 初始化Go项目结构：创建go.mod、main.go，编译通过。
- 实现基础client/server模式：添加命令行参数选择模式，UDP连接。
- 添加多会话代理支持：server监听端口范围，client发送到所有端口，server收到数据。
- 实现目标地址转发：server转发数据到target，使用不同源端口。
- 添加流轮询机制：client监听端口，收到数据后发送到所有server代理会话。
- 实现源端口区分：已在转发中实现。
- 支持命令行参数配置：添加所有必要flags。
- 测试和验证功能：基本功能测试通过，数据从client流经server转发到target。
- 使用log库记录日志：添加log-level flag，支持debug, info, warn, error级别，默认info，使用slog包。
- 初始化Go规范目录结构：创建cmd/udpsplit/目录，移动main.go，更新project.instructions.md。
