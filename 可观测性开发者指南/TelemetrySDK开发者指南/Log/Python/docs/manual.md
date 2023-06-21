# 使用手册

#### SamplerLogger

SamplerLogger 异步模式上报数据的日志器。

```
SamplerLogger(log_resource(), ConsoleExporter(), ARLogExporter())
```

#### SyncLogger

SyncLogger 同步模式上报数据的日志器。

```
SamplerLogger(log_resource(), ARLogExporter())
```

#### ConsoleExporter

ConsoleExporter 新建Exporter，日志在控制台实时打印。

```
ConsoleExporter()
```

#### ARLogExporter

ARLogExporter 新建Exporter，需要传入指定的数据发送客户端 Client 。

```
ARLogExporter(StdoutClient("./AnyRobotLog.txt"))
```

#### FileClient

FileClient 创建 Exporter 需要的数据写入文件发送客户端。

```
FileClient("./AnyRobotTrace.txt")
```

#### ConsoleClient

ConsoleClient 创建 Exporter 需要的数据写入控制台发送客户端。

```
ConsoleClient()
```

#### StdoutClient

StdoutClient 创建 Exporter 需要的数据写入文件+控制台发送客户端。

```
StdoutClient("./AnyRobotTrace.txt")
```

#### HTTPClient

HTTPClient 创建 Exporter 需要的HTTP数据发送客户端。

```
HTTPClient(WithAnyRobotURL(),WithCompression(Compression.GzipCompression))
```

#### WithAnyRobotURL

WithAnyRobotURL 设置 HTTPClient 数据上报地址。

```
WithAnyRobotURL(url: str)
```

#### WithSyncMode

WithSyncMode 设置发送方式为同步发送。

```
WithSyncMode()
```

#### WithCompression

WithCompression 设置Log压缩方式：0代表无压缩，1代表GZIP压缩。

```
WithCompression
```

#### WithHeader

WithHeader 设置 HTTPClient 用户自定义请求头。

```
WithHeader(headers: dict[str, str])
```

#### WithRetry

WithRetry 设置 HTTPClient 重发机制，如果显著干扰到业务运行了，减少最大重发时间maxElapsedTime。

```
WithRetry(max_elapsed_time: float)
```

#### WithTimeout

WithTimeout 设置 HTTPClient 连接超时时间。

```
WithTimeout(timeout: int)
```

#### set_service_info

set_service_info 设置服务信息，包括服务名、版本号、实例ID。

```
def set_service_info(name: str, version: str, instance_id: str):
```

#### log_resource

log_resource 传入 Log 的默认resource。

```
def log_resource() -> Resources:
```
