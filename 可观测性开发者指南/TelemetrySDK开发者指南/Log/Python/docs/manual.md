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

#### ARLogExporter

ARLogExporter 新建Exporter，需要传入指定的数据发送客户端 Client 。

```
ARLogExporter(StdoutClient("./AnyRobotLog.txt"))
```

#### ARLogExporter

ARLogExporter 新建Exporter，需要传入指定的数据发送客户端 Client 。

```
ARLogExporter(StdoutClient("./AnyRobotLog.txt"))
```

#### HTTPClient

HTTPClient 创建 Exporter 需要的HTTP数据发送客户端。

```
HTTPClient(WithAnyRobotURL(),WithCompression(Compression.GzipCompression))
```

#### StdoutClient

StdoutClient 创建 Exporter 需要的Local数据发送客户端。

```
StdoutClient("./AnyRobotLog.txt")
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

#### Log_resource

Log_resource 传入 Log 的默认resource。

```
def log_resource() -> Resources:
```
