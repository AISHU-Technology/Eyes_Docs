# 使用手册

#### set_tracer_provider

set_tracer_provider 设置全局唯一的 TraceProvider 。

```
set_tracer_provider((tracer_provider=trace_provider))
```

#### TraceProvider

TraceProvider 根据配置项，新建 TraceProvider 。

```
TracerProvider(resource=trace_resource(), active_span_processor=trace_processor)
```

#### active_span_processor

active_span_processor Union[SynchronousMultiSpanProcessor, ConcurrentMultiSpanProcessor] 。

```
SynchronousMultiSpanProcessor()
ConcurrentMultiSpanProcessor(num_threads = 2)
```

#### add_span_processor

add_span_processor 同一份数据源添加多个上报地址。

```
trace_processor.add_span_processor(
    span_processor=BatchSpanProcessor(span_exporter=trace_exporter_1, schedule_delay_millis=2000,
                                      max_queue_size=10000, max_export_batch_size=400
                                      ))
                                      
trace_processor.add_span_processor(
    span_processor=BatchSpanProcessor(span_exporter=trace_exporter_2, schedule_delay_millis=2000,
                                      max_queue_size=10000, max_export_batch_size=400
                                      ))
```

#### SpanProcessor

SpanProcessor 链路数据处理器。

```
SimpleSpanProcessor(span_exporter = trace_exporter)
BatchSpanProcessor(span_exporter = trace_exporter, schedule_delay_millis=2000, max_queue_size=10000, max_export_batch_size=400)
```

#### ARTraceExporter

ARTraceExporter 新建Exporter，需要传入指定的数据发送客户端 Client 。

```
ARTraceExporter(StdoutClient("./AnyRobotTrace.txt"))
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

WithCompression 设置Trace压缩方式：0代表无压缩，1代表GZIP压缩。

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

#### trace_resource

trace_resource 传入 Trace 的默认resource。

```
def trace_resource() -> Resource:
```
