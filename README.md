# UDP Split Tool

UDP分发工具，实现UDP数据的高效传输和分发。支持client和server两种模式，通过多会话代理和stream管理实现数据分发和转发。

## 功能特性

- 支持client和server模式
- 多会话代理：server监听多个端口范围，client轮询发送到代理会话
- 目标地址转发：server收到数据后转发到指定目标地址
- Stream管理：基于客户端IP:Port识别stream，确保同一stream使用固定端口
- 端口管理：自动分配和管理本地端口，避免冲突
- 源端口区分：server为不同stream使用不同本地端口转发
- 命令行参数配置

## 构建

### 构建要求

- Go 1.21 或更高版本
- 仅支持 amd64 架构的 Linux 系统

### 构建步骤

1. 确保已安装 Go 环境：

   ```bash
   go version
   ```

2. 克隆或进入项目目录：

   ```bash
   cd /path/to/udpsplit
   ```

3. 构建程序：

   ```bash
   go build ./cmd/udpsplit
   ```

4. 构建成功后，会在当前目录生成 `udpsplit` 二进制文件。

### 验证构建

运行构建后的程序查看帮助信息：

```bash
./udpsplit --help
```

### 交叉编译（可选）

如果需要在其他平台构建，请设置 GOOS 和 GOARCH 环境变量：

```bash
GOOS=linux GOARCH=amd64 go build ./cmd/udpsplit
```

## 使用方法

### Server模式

启动server，监听多个端口并转发数据到目标地址：

```bash
./udpsplit -mode=server -server-ports=8000-8005 -target=127.0.0.1:9000
```

参数说明：

- `-mode=server`: 运行模式
- `-server-ports=8000-8005`: server监听的端口范围
- `-target=127.0.0.1:9000`: 转发目标地址

### Client模式

启动client，监听端口并将收到的数据分发到server的代理会话：

```bash
./udpsplit -mode=client -client-listen=7000 -server-addr=127.0.0.1 -server-ports=8000-8005
```

参数说明：

- `-mode=client`: 运行模式
- `-client-listen=7000`: client监听的端口
- `-server-addr=127.0.0.1`: server地址
- `-server-ports=8000-8005`: server端口范围

### 测试示例

1. 启动server：

   ```bash
   ./udpsplit -mode=server &
   ```

2. 启动client：

   ```bash
   ./udpsplit -mode=client &
   ```

3. 发送数据到client：

   ```bash
   echo "hello world" | nc -u 127.0.0.1 7000
   ```

4. 启动目标接收器（可选）：

   ```bash
   nc -u -l 9000
   ```

数据流：外部数据 → client(7000) → stream管理 → 轮询到server代理端口 → server stream识别 → 转发到target(9000)

## 参数列表

| 参数 | 默认值 | 说明 |
|------|--------|------|
| -mode | client | 运行模式：client 或 server |
| -server-addr | 127.0.0.1 | server地址 |
| -server-ports | 8000-8005 | server端口范围 |
| -target | 127.0.0.1:9000 | 目标地址 |
| -client-listen | 7000 | client监听端口 |

## 架构说明

### Stream管理

- **Stream定义**：每个外部客户端（IP:Port）对应一个stream。
- **Client端**：收到数据时，根据发送者地址创建/获取stream，为每个stream分配固定本地发送端口，确保同一stream数据从相同源端口发出。
- **Server端**：收到数据时，根据client地址识别stream，为每个stream分配固定本地转发端口，使用独立UDP socket转发。

### 多会话代理

- Server监听端口范围内的多个端口，每个端口作为代理会话通道。
- Client使用轮询方式选择代理会话发送数据，实现负载均衡。

### 端口管理

- 使用PortManager自动分配可用端口（server: 9000-65535, client: 10000-65535）。
- 支持大量stream动态端口分配，避免冲突。

### 数据流

1. 外部应用发送UDP数据到client监听端口。
2. Client根据发送者创建stream，使用固定源端口轮询发送到server代理端口。
3. Server根据client地址识别stream，使用固定本地端口转发到目标地址。
4. 目标应用收到来自不同源端口的数据，可区分不同stream。

## 平台支持

仅支持amd64架构的Linux系统。
