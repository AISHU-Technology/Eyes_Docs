# 开发指南

## 导入TelemetrySDK-Event(Go)

**第1步**检查版本兼容性

- 查看SDK[兼容列表](../../../docs/compatibility.md)，检查待埋点业务代码的Go版本是否符合要求。

**第2步**配置项目下载权限

- 从[ONE-Architecture](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go)
  配置代码仓库下载权限，以便后续从ONE-Architecture拉取代码[参考文档](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/Eyes_Docs?path=/可观测性开发者指南/TelemetrySDK开发者指南/Log/README.md&version=GBdevelop&_a=preview&anchor=sdk2.0-使用参考)

**第3步**执行以下命令引入TelemetrySDK-Event(Go)

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/event@2.6.1
```

**第4步**(可选)更新TelemetrySDK-Event(Go)

- 查看Telemetry[版本列表](../../../docs/compatibility.md)，选择希望引入的版本，例如v2.6.1，替换末尾的版本号重新执行命令。

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/event@2.6.1
```

## 导入Event Exporter

**第1步**执行以下命令引入Event Exporter

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter@2.6.1
go mod tidy
```

**第2步**(可选)更新Event Exporter

- 查看SDK[兼容列表](../../../docs/compatibility.md)，选择希望引入的版本，例如2.6.1，替换末尾的版本号重新执行命令。

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter@2.6.1
go mod tidy
```

## 使用TelemetrySDK-Event(Go)进行代码埋点生产事件数据

**第1步**新增依赖：以下为新增汇总，以实际使用为准。

```
import (
	"context"
	"log"
	"time"

	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/event/eventsdk"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/ar_event"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/ar_trace"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/public"
	"go.opentelemetry.io/otel"
	sdktrace "go.opentelemetry.io/otel/sdk/trace"
	"go.opentelemetry.io/otel/trace"
)
```

**第2步**修改业务代码

- 修改前

```
func multiply(ctx context.Context, x, y int64) (context.Context, int64) {
	// 业务代码
	return ctx, x * y
}
```

- 修改后

```
func multiply(ctx context.Context, x, y int64) (context.Context, int64) {
	eventsdk.Info("eventData")

	// 业务代码
	return ctx, x * y
}
```

### 上报事件数据到AnyRobot

**第1步**本地调试

```
func main() {
	ctx := context.Background()
	eventClient := public.NewStdoutClient("./AnyRobotEvent.txt")
	eventExporter := ar_event.NewExporter(eventClient)
	public.SetServiceInfo("YourServiceName", "1.0.0", "983d7e1d5e8cda64")
	eventProvider := eventsdk.NewEventProvider(eventsdk.Exporters(eventExporter), ar_event.EventResource())
	eventsdk.SetEventProvider(eventProvider)

	defer func() {
		if err := eventProvider.Shutdown(); err != nil {
			log.Println(err)
		}
	}()

	// 业务代码
	ctx, num := multiply(ctx, 1, 7)
	ctx, num = add(ctx, num, 8)
	log.Println(result, num)
}
```

- 在同级目录找到本地文件AnyRobotEvent.txt，查看是否正常生产事件数据。

**第2步**获取上报地址

- 在AnyRobot管理端创建Event采集任务并生成上报地址供数据源端使用，如`http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events` 。

**第3步**上报到AnyRobot

- 将获取的上报地址作为参数传入

```
func main() {
	ctx := context.Background()
	// eventClient := public.NewStdoutClient("./AnyRobotEvent.txt")
	eventClient := public.NewHTTPClient(public.WithAnyRobotURL("http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events"),
		public.WithCompression(0), public.WithTimeout(10*time.Second), public.WithRetry(true, 5*time.Second, 30*time.Second, 1*time.Minute))
	eventExporter := ar_event.NewExporter(eventClient)
	public.SetServiceInfo("YourServiceName", "1.0.0", "983d7e1d5e8cda64")
	eventProvider := eventsdk.NewEventProvider(eventsdk.Exporters(eventExporter, eventsdk.GetDefaultExporter()), ar_event.EventResource())
	eventsdk.SetEventProvider(eventProvider)

	defer func() {
		if err := eventProvider.Shutdown(); err != nil {
			log.Println(err)
		}
	}()

	// 业务代码
	ctx, num := add(ctx, 2, 3)
	for i := 0; i < 6; i++ {
		ctx, num = multiply(ctx, 2, 3)
		ctx, num = add(ctx, 2, 3)
	}
	log.Println(result, num)
}
```

## [更多示例](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go?version=GB2.6.1&path=%2Fexporter%2Far_event%2Fexamples%2Fsingle_service_with_event.go)
