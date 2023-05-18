# 开发指南

## 导入TelemetrySDK-Trace(Go)

**第1步**检查版本兼容性

- 查看SDK[兼容列表](../../../docs/compatibility.md)，检查待埋点业务代码的Go版本是否符合要求。

**第2步**配置项目下载权限

- 从[ONE-Architecture](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go)
  拉取代码需要配置代码仓库[下载权限](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/Eyes_Docs?path=/可观测性开发者指南/TelemetrySDK开发者指南/Log/README.md&version=GBdevelop&_a=preview&anchor=sdk2.0-使用参考)

**第3步**执行以下命令引入TelemetrySDK-Trace(Go)

```
go get go.opentelemetry.io/otel@v1.11.2
go get go.opentelemetry.io/otel/sdk/trace@v1.11.2
```

**第4步**(可选)更新TelemetrySDK-Trace(Go)

- 查看Telemetry[版本列表](https://pkg.go.dev/go.opentelemetry.io/otel?tab=versions)，选择希望引入的版本，例如v1.11.0，替换末尾的版本号重新执行命令。

```
go get go.opentelemetry.io/otel@v1.11.0
go get go.opentelemetry.io/otel/sdk/trace@v1.11.0
```

## 导入Trace Exporter

**第1步**执行以下命令引入Trace Exporter

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter@2.6.0
```

**第2步**(可选)更新Trace Exporter

- 查看SDK[兼容列表](../../../docs/compatibility.md)，选择希望引入的版本，例如2.6.0，替换末尾的版本号重新执行命令。

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter@2.6.0
```

## 使用TelemetrySDK-Trace(Go)进行代码埋点生产链路数据

**第1步**新增依赖：以下为新增汇总，以实际使用为准。

```
import (
	"context"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/ar_trace"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/public"
	"go.opentelemetry.io/otel"
	"go.opentelemetry.io/otel/attribute"
	sdktrace "go.opentelemetry.io/otel/sdk/trace"
	"go.opentelemetry.io/otel/trace"
	"log"
	"time"
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
	ctx, span := ar_trace.Tracer.Start(ctx, "乘法")
	defer span.End()

	// 业务代码
	return ctx, x * y
}
```

### 上报链路数据到AnyRobot

**第1步**本地调试

```
func main() {
	ctx := context.Background()
	traceClient := public.NewStdoutClient("./AnyRobotTrace.txt")
	traceExporter := ar_trace.NewExporter(traceClient)
	public.SetServiceInfo("YourServiceName", "1.0.0", "983d7e1d5e8cda64")
	tracerProvider := sdktrace.NewTracerProvider(
		sdktrace.WithBatcher(traceExporter,
			sdktrace.WithBlocking(),
			sdktrace.WithMaxExportBatchSize(1000)), 
		sdktrace.WithResource(ar_trace.TraceResource()))

	otel.SetTracerProvider(tracerProvider)
	defer func() {
		if err := tracerProvider.Shutdown(ctx); err != nil {
			log.Println(err)
		}
	}()

	// 业务代码
	ctx, num := multiply(ctx, 2, 3)
	ctx, num = multiply(ctx, num, 7)
	ctx, num = add(ctx, num, 8)
	log.Println(result, num)
}
```

- 在同级目录找到本地文件AnyRobotTrace.txt，查看是否正常生产链路数据。

**第2步**获取上报地址

- 在AnyRobot管理端创建Trace采集任务并生成上报地址供数据源端使用，如`http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events` 。

**第3步**上报到AnyRobot

- 将获取的上报地址作为参数传入

```
func main() {
	ctx := context.Background()
	// traceClient := public.NewStdoutClient("./AnyRobotTrace.txt")
	traceClient := public.NewHTTPClient(public.WithAnyRobotURL("http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events"))
	traceExporter := ar_trace.NewExporter(traceClient)
	public.SetServiceInfo("YourServiceName", "1.0.0", "983d7e1d5e8cda64")
	tracerProvider := sdktrace.NewTracerProvider(
		sdktrace.WithBatcher(traceExporter,
			sdktrace.WithBlocking(),
			sdktrace.WithMaxExportBatchSize(1000)), 
		sdktrace.WithResource(ar_trace.TraceResource()))
	otel.SetTracerProvider(tracerProvider)

	defer func() {
		if err := tracerProvider.Shutdown(ctx); err != nil {
			log.Println(err)
		}
	}()

	// 业务代码
	ctx, num := multiply(ctx, 2, 3)
	ctx, num = multiply(ctx, num, 7)
	ctx, num = add(ctx, num, 8)
	log.Println(result, num)
}
```

### 使用开关示例

- 解绑/恢复SpanProcessor

**第1步**执行以下命令与SpanProcessor解绑，停止生产发送链路数据

```
func main() {
	ctx := context.Background()
	traceClient := public.NewStdoutClient("./AnyRobotTrace.txt")
	traceExporter := ar_trace.NewExporter(traceClient)
	public.SetServiceInfo("YourServiceName", "1.0.0", "")
	tracerProvider := sdktrace.NewTracerProvider(
		sdktrace.WithBatcher(traceExporter,
			sdktrace.WithBlocking(),
			sdktrace.WithMaxExportBatchSize(1000)), 
		sdktrace.WithResource(ar_trace.TraceResource()))
	otel.SetTracerProvider(tracerProvider)
	defer func() {
		if err := tracerProvider.Shutdown(ctx); err != nil {
			log.Println(err)
		}
	}()

    // 业务代码
	ctx, num := multiply(ctx, 2, 3)
	ctx, num = multiply(ctx, num, 7)
	// 调用ForceFlush之后会立即发送之前生产的2次乘法链路。
	_ = tracerProvider.ForceFlush(ctx)
	// 关闭Trace的发送，这3次加法产生的链路不会发送。
	tracerProvider.UnregisterSpanProcessor(sdktrace.NewBatchSpanProcessor(traceExporter))
	ctx, num = add(ctx, num, 8)
	ctx, num = add(ctx, num, 9)
	ctx, num = add(ctx, num, 10)
}
```

**第2步**执行以下命令恢复SpanProcessor来生产并发送链路数据

```
func main() {
	ctx := context.Background()
	traceClient := public.NewStdoutClient("./AnyRobotTrace.txt")
	traceExporter := ar_trace.NewExporter(traceClient)
	public.SetServiceInfo("YourServiceName", "1.0.0", "")
	tracerProvider := sdktrace.NewTracerProvider(
		sdktrace.WithBatcher(traceExporter,
			sdktrace.WithBlocking(),
			sdktrace.WithMaxExportBatchSize(1000)), 
		sdktrace.WithResource(ar_trace.TraceResource()))
	otel.SetTracerProvider(tracerProvider)
	defer func() {
		if err := tracerProvider.Shutdown(ctx); err != nil {
			log.Println(err)
		}
	}()

    // 业务代码
	ctx, num := multiply(ctx, 2, 3)
	ctx, num = multiply(ctx, num, 7)
	// 调用ForceFlush之后会立即发送之前生产的2次乘法链路。
	_ = tracerProvider.ForceFlush(ctx)
	// 关闭Trace的发送，这3次加法产生的链路不会发送。
	tracerProvider.UnregisterSpanProcessor(sdktrace.NewBatchSpanProcessor(traceExporter))
	ctx, num = add(ctx, num, 8)
	ctx, num = add(ctx, num, 9)
	ctx, num = add(ctx, num, 10)
	// 开启Trace的发送，这1次乘法产生的链路会发送。
	tracerProvider.RegisterSpanProcessor(sdktrace.NewBatchSpanProcessor(traceExporter))
	ctx, num = multiply(ctx, num, 9)
	log.Println(result, num)
}
```

## [更多示例](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go?path=%2Fexporter%2Far_trace%2Fexamples%2Fone_service.go&version=GBmaster&_a=contents)
