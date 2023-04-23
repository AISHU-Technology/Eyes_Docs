# 开发指南

## 导入TelemetrySDK-Log(Python)

**第1步**检查版本兼容性

- 查看SDK[兼容列表](../../../docs/compatibility.md)，检查待埋点业务代码的Python版本是否符合要求。

**第2步**配置项目下载权限

- 从[ONE-Architecture](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python)
  拉取代码需要配置代码仓库[下载权限](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/Eyes_Docs?path=/可观测性开发者指南/TelemetrySDK开发者指南/Log/README.md&version=GBdevelop&_a=preview&anchor=sdk2.0-使用参考)

**第3步**执行以下命令引入TelemetrySDK-Log(Python)

```
git clone ssh://devops.aishu.cn:22/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python -b 2.3.0
cd TelemetrySDK-Python
pip install .
```

**第4步**(可选)更新TelemetrySDK-Log(Python)

- 查看Telemetry[版本列表](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python/branches?_a=all)
  ，选择希望引入的版本，例如v2.3.0，替换末尾的版本号重新执行命令。

```
cd TelemetrySDK-Python
git fetch
git checkout 2.3.0
pip install .
```

## 使用TelemetrySDK-Log(Python)进行代码埋点生产Log数据

**第1步**新增依赖：以下为新增汇总，以实际使用为准。

```
from tlogging import SamplerLogger, Attributes

from exporter.ar_log.log_exporter import ARLogExporter
from exporter.public.client import HTTPClient, StdoutClient
from exporter.public.public import WithAnyRobotURL, WithSyncMode
from exporter.resource.resource import log_resource, set_service_info
from tlogging.exporter import ConsoleExporter
from tlogging.tlogger import SyncLogger
```

**第2步**修改业务代码

- 修改前

```
def add_before(x: int, y: int) -> int:
    return x + y
```

- 修改后

```
def add(x: int, y: int) -> int:
    func_attr = Attributes({"param_x": x, "param_y": y}, atype="pair")
    system_logger.warn(message="this is a info message", attributes=func_attr)
    return x + y
```

### 上报Log数据到AnyRobot

**第1步**获取上报地址

- 在AnyRobot管理端创建Log采集任务并生成上报地址供数据源端使用，如`http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events` 。

**第2步**上报到AnyRobot

- 将获取的上报地址作为参数传入

```
if __name__ == "__main__":
    # 设置服务名、服务版本号、服务运行实例ID
    set_service_info("YourServiceName", "2.3.0", "983d7e1d5e8cda64")
    # 初始化系统日志器，系统日志在控制台输出，并且异步模式上报数据到数据接收器。
    system_logger = SamplerLogger(log_resource(), ConsoleExporter(), ARLogExporter(
        HTTPClient(WithAnyRobotURL("http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events"))))

    # 初始化业务日志器，业务日志同步模式上报数据到数据接收器。
    # ！注意配置这个参数WithSyncMode()
    service_logger = SyncLogger(log_resource(), ARLogExporter(
        HTTPClient(WithAnyRobotURL("http://127.0.0.1/api/feed_ingester/v1/jobs/job-c9a577c302505576/events"),
                   WithSyncMode())))

    # 业务代码
    add(1, 2)
    multiply(3, 4)
```

## [更多示例](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Python?path=%2Fexporter%2Far_log%2Fexamples%2Fone_service.py&version=GB2.3.0&_a=contents)
