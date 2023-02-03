# 开发指南

## 导入TelemetrySDK-Metric(Python)

**第1步**检查版本兼容性

- 查看SDK[兼容列表](../../../docs/compatibility.md)，检查待埋点业务代码的Python版本是否符合要求。

**第2步**配置项目下载权限

- 从[ONE-Architecture](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python)
  拉取代码需要配置代码仓库[下载权限](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/Eyes_Docs?path=/可观测性开发者指南/TelemetrySDK开发者指南/Log/README.md&version=GBdevelop&_a=preview&anchor=sdk2.0-使用参考)

## 导入Metric Exporter

**第1步**执行以下命令引入Metric Exporter

```
git clone ssh://devops.aishu.cn:22/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python -b 2.2.0
cd TelemetrySDK-Python
pip install .
```

**第2步**(可选)更新Metric Exporter

- 查看SDK[兼容列表](../../../docs/compatibility.md)，选择希望引入的版本，例如2.3.0。

```
git clone ssh://devops.aishu.cn:22/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python -b 2.2.0
cd TelemetrySDK-Python
pip install .
```

## 导入依赖

**第1步**执行以下命令引入TelemetrySDK-Metric(Python)

```
pip3 install -r requirements.txt
```

**第2步**(可选)Mark directory as namespace package

## 使用TelemetrySDK-Metric(Python)进行代码埋点生产指标数据

**第1步**新增依赖：以下为新增汇总，以实际使用为准。

```
from opentelemetry import metrics
from opentelemetry.metrics import Observation, CallbackOptions
from opentelemetry.sdk.metrics import MeterProvider
from opentelemetry.sdk.metrics.export import (
    PeriodicExportingMetricReader,
)
from exporter.config.config import Compression
from exporter.public.client import StdoutClient, HTTPClient
from exporter.ar_metric.metric_exporter import ARMetricExporter, meter
from exporter.public.public import WithAnyRobotURL, WithCompression
from exporter.resource.resource import metric_resource
```

**第2步**修改业务代码

- 修改前

```
if __name__ == "__main__":
    num = 4
    num = add(num, 7)
    num = add(num, 8)
    num = multiply(num, 9)
    num = multiply(num, 22)
```

- 修改后

```
reader = PeriodicExportingMetricReader(
    ARMetricExporter(StdoutClient("./AnyRobotMetric.txt"))
)
provider = MeterProvider(resource=metric_resource(), metric_readers=[reader])
metrics.set_meter_provider(provider)

if __name__ == "__main__":
    num = 4

    num = add(num, 7)
    meter.create_observable_gauge(
        "gauge", [observable_gauge_func], "dimension", " a simple gauge"
    )

    num = add(num, 7)
    attributes = {"用户信息": "当前用户数"}
    histogram = meter.create_histogram(
        "histogram", "dimension", "a histogram with custom buckets and name"
    )
    histogram.record(num, attributes)
    num = multiply(num, 7)
    histogram.record(num, attributes)

    num = multiply(num, 2)
    attributes = {"用户信息": "用户数日活"}
    counter = meter.create_counter("sum", "dimension", "a simple counter")
    counter.add(num, attributes)
    counter.add(num, attributes)
```

### 上报指标数据到AnyRobot

**第1步**本地调试

- 在同级目录找到本地文件AnyRobotMetric.txt，查看是否正常生产指标数据。

**第2步**获取上报地址

- 在AnyRobot管理端创建Metric采集任务并生成上报地址供数据源端使用，如`http://a.b.c.d/api/feed_ingester/v1/jobs/abcd4f634e80d530/metrics` 。

**第3步**上报到AnyRobot

- 将获取的上报地址作为参数传入

```
reader = PeriodicExportingMetricReader(
    ARMetricExporter(
        HTTPClient(
            WithAnyRobotURL(
                http://a.b.c.d/api/feed_ingester/v1/jobs/abcd4f634e80d530/metrics"
            ),
            WithCompression(Compression.GzipCompression),
        )
    )
)
```

## [更多示例](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python?path=%2Fexporter%2Far_metric%2Fexamples%2Fone_service.py)
