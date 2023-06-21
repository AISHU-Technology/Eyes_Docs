# 使用手册

#### MeterProvider

MeterProvider 根据配置项，新建 MeterProvider 。

```
MeterProvider(resource=metric_resource(), metric_readers=[reader])
```

#### PeriodicExportingMetricReader

PeriodicExportingMetricReader 新建Reader，需要传入MetricExporter() 。

```
PeriodicExportingMetricReader(MetricExporter())
```

#### ARMetricExporter

ARMetricExporter 新建Exporter，需要传入指定的数据发送客户端 Client 。

```
ARMetricExporter(StdoutClient("./AnyRobotMetric.txt"))
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

#### WithCompression

WithCompression 设置Metric压缩方式：0代表无压缩，1代表GZIP压缩。

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

#### metric_resource

metric_resource 传入 Metric 的默认resource。

```
def metric_resource() -> Resource:
```
