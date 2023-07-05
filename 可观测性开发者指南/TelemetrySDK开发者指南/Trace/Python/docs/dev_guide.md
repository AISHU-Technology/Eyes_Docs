# 开发指南

## 导入TelemetrySDK-Trace(Python)

**第1步**检查版本兼容性

- 查看SDK[兼容列表](../../../docs/compatibility.md)，检查待埋点业务代码的Python版本是否符合要求。

**第2步**配置项目下载权限

- 从[ONE-Architecture](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python)
  配置代码仓库下载权限，以便后续从ONE-Architecture拉取代码[参考文档](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/Eyes_Docs?path=/可观测性开发者指南/TelemetrySDK开发者指南/Log/README.md&version=GBdevelop&_a=preview&anchor=sdk2.0-使用参考)

## 导入Trace Exporter

**第1步**执行以下命令引入Trace Exporter

```
git clone ssh://devops.aishu.cn:22/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python -b 2.4.2
cd TelemetrySDK-Python
pip install -r requirements.txt
pip install .
```

**第2步**(可选)更新Trace Exporter

- 查看SDK[兼容列表](../../../docs/compatibility.md)，选择希望引入的版本，例如2.4.2。

```
cd TelemetrySDK-Python
git fetch
git checkout 2.4.2
pip install -r requirements.txt
pip install .
```

## 使用TelemetrySDK-Trace(Python)进行代码埋点生产链路数据

**第1步**新增依赖：以下为新增汇总，以实际使用为准。

```
from opentelemetry.context import Context
from opentelemetry.sdk.trace import TracerProvider, SynchronousMultiSpanProcessor
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.trace import set_tracer_provider
from opentelemetry.trace.propagation.tracecontext import TraceContextTextMapPropagator

from exporter.ar_trace.trace_exporter import ARTraceExporter, tracer
from exporter.config.config import Compression
from exporter.public.client import StdoutClient, HTTPClient, FileClient, ConsoleClient
from exporter.public.public import WithAnyRobotURL, WithCompression
from exporter.resource.resource import set_service_info, trace_resource

import flask
from opentelemetry.instrumentation.flask import FlaskInstrumentor
import requests
from opentelemetry.instrumentation.requests import RequestsInstrumentor
```

**第2步**修改业务代码

- 修改前

```
def add(x: int, y: int) -> int:
    return x + y
```

- 修改后

```
def add(x: int, y: int) -> int:
    with tracer.start_as_current_span("add", context=ctx) as span:
        # 设置属性的key中特殊字符.禁止使用
        span.set_attribute("add_value", x + y)
    return x + y
```

### 上报链路数据到AnyRobot

**第1步**本地调试

- 在同级目录找到本地文件AnyRobotTrace.json，查看是否正常生产链路数据。

**第2步**获取上报地址

- 在AnyRobot管理端创建Trace采集任务并生成上报地址供数据源端使用，如`http://127.0.0.1/api/feed_ingester/v1/jobs/job-864ab9d78f6a1843/events` 。

**第3步**上报到AnyRobot

- 将获取的上报地址作为参数传入

```
def trace_init():
    set_service_info("YourServiceName", "2.4.2", "983d7e1d5e8cda64")
    trace_exporter = ARTraceExporter(
        FileClient("AnyRobotTrace.json")
    )
    trace_processor = SynchronousMultiSpanProcessor()
    trace_processor.add_span_processor(
        span_processor=BatchSpanProcessor(span_exporter=trace_exporter, schedule_delay_millis=2000,
                                          max_queue_size=10000, max_export_batch_size=400
                                          ))
    trace_provider = TracerProvider(resource=trace_resource(), active_span_processor=trace_processor)
    set_tracer_provider(tracer_provider=trace_provider)
```

## [更多示例](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python?path=%2Fexporter%2Far_trace%2Fexamples%2FREADME.md&version=GB2.4.2&_a=preview)
