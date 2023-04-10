# 开发指南

## 导入TelemetrySDK-Log(Go)

**第1步**检查版本兼容性

- 查看SDK[兼容列表](../../../docs/compatibility.md)，检查待埋点业务代码的Go版本是否符合要求。

**第2步**配置项目下载权限

- 从[ONE-Architecture](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go)
  拉取代码需要配置代码仓库[下载权限](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/Eyes_Docs?path=/可观测性开发者指南/TelemetrySDK开发者指南/Log/README.md&version=GBdevelop&_a=preview&anchor=sdk2.0-使用参考)

**第3步**执行以下命令引入TelemetrySDK-Log(Go)

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span@2.6.0
```

**第4步**(可选)更新TelemetrySDK-Log(Go)

- 查看Telemetry[版本列表](https://pkg.go.dev/go.opentelemetry.io/otel?tab=versions)，选择希望引入的版本，例如v2.7.0，替换末尾的版本号重新执行命令。

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span@2.7.0
```

## 导入Log Exporter

**第1步**执行以下命令引入Log Exporter

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter@2.6.0
```

**第2步**(可选)更新Log Exporter

- 查看SDK[兼容列表](../../../docs/compatibility.md)，选择希望引入的版本，例如2.7.0，替换末尾的版本号重新执行命令。

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter@2.7.0
```

## 使用TelemetrySDK-Log(Go)进行代码埋点生产Log数据

**第1步**新增依赖：以下为新增汇总，以实际使用为准。

```
import (
	"context"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/ar_log"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/ar_trace"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/public"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/resource"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/encoder"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/exporter"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/field"
	spanLog "devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/log"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/open_standard"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/runtime"
	"fmt"
	"go.opentelemetry.io/otel"
	sdktrace "go.opentelemetry.io/otel/sdk/trace"
	"time"
)
```

**第2步**修改业务代码

- 修改前

```
func multiplyBefore(ctx context.Context, x, y int64) (context.Context, int64) {
	//业务代码
	time.Sleep(100 * time.Millisecond)
	return ctx, x * y
}
```

- 修改后

```
// 定义一组全局变量
// SystemLogger 程序日志记录器，使用异步发送模式，无返回值。
var SystemLogger = spanLog.NewSamplerLogger(spanLog.WithSample(1.0), spanLog.WithLevel(spanLog.InfoLevel))
// ServiceLogger 业务日志记录器，使用同步发送模式，有返回值，返回error=nil代表发送成功，返回error!=nil代表发送失败。
var ServiceLogger = spanLog.NewSyncLogger(spanLog.WithLevel(spanLog.AllLevel))
```

```
// multiply 增加了 Log 的计算两数之积。
func multiply(ctx context.Context, x, y int64) (context.Context, int64) {
	ctx, _ = ar_trace.Tracer.Start(ctx, "乘法")

	numbers := []int64{x, y}
	attr := field.NewAttribute("INTARRAY", field.MallocJsonField(numbers))
	// ServiceLogger 记录业务日志，并且记录自定义的业务属性信息。
	if err := ServiceLogger.Info("multiply two numbers", field.WithAttribute(attr), field.WithContext(ctx)); err != nil {
		fmt.Println(err)
	}
	// SystemLogger 记录系统日志，同时关联Trace。
	SystemLogger.Fatal("This is an fatal message", field.WithContext(ctx))

	//业务代码
	time.Sleep(100 * time.Millisecond)
	return ctx, x * y
}
```

### 上报Log数据到AnyRobot

**第1步**本地标准输出

```
func StdoutExporterExample() {
	public.SetServiceInfo("YourServiceName", "2.6.5", "983d7e1d5e8cda64")

	// 1.初始化系统日志器，系统日志在控制台输出。
	systemLogExporter := exporter.GetStdoutExporter()
	systemLogWriter := open_standard.OpenTelemetryWriter(
		encoder.NewJsonEncoderWithExporters(systemLogExporter),
		resource.LogResource())
	systemLogRunner := runtime.NewRuntime(systemLogWriter, field.NewSpanFromPool)
	systemLogRunner.SetUploadInternalAndMaxLog(3*time.Second, 10)
	// 运行SystemLogger日志器。
	go systemLogRunner.Run()
	//defer SystemLogger.Close()
	SystemLogger.SetLevel(spanLog.InfoLevel)
	SystemLogger.SetRuntime(systemLogRunner)

	// 2.初始化业务日志器，业务日志仅在控制台输出。
	serviceLogExporter := exporter.SyncStdoutExporter()
	serviceLogWriter := open_standard.NewSyncWriter(
		encoder.NewSyncEncoder(serviceLogExporter),
		resource.LogResource())
	// 运行ServiceLogger日志器。
	//defer ServiceLogger.Close()
	ServiceLogger.SetLevel(spanLog.AllLevel)
	ServiceLogger.SetWriter(serviceLogWriter)

	// 3.运行业务代码
	ctx := context.Background()
	otel.SetTracerProvider(sdktrace.NewTracerProvider())
	ctx, num := multiply(ctx, 1, 7)
	ctx, _ = add(ctx, num, 8)
	for i := 0; i < 10; i++ {
		ctx, num = multiply(ctx, 1, num)
	}
}
```

- 在标准输出的控制台查看是否正常生产Log数据。

**第2步**获取上报地址

- 在AnyRobot管理端创建Log采集任务并生成上报地址供数据源端使用，如`http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events` 。

**第3步**上报到AnyRobot

- 将获取的上报地址作为参数传入

```
func HTTPExample() {
	public.SetServiceInfo("YourServiceName", "2.6.5", "c9a577c302505576")
	stdoutExporter := exporter.GetStdoutExporter()

	// 1.初始化系统日志器，系统日志在控制台输出，同时上报到AnyRobot。
	systemLogClient := public.NewHTTPClient(public.WithAnyRobotURL("http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events"),
		public.WithCompression(0), public.WithTimeout(10*time.Second), public.WithRetry(true, 5*time.Second, 30*time.Second, 1*time.Minute))
	systemLogExporter := ar_log.NewExporter(systemLogClient)
	systemLogWriter := open_standard.OpenTelemetryWriter(
		encoder.NewJsonEncoderWithExporters(systemLogExporter, stdoutExporter),
		resource.LogResource())
	systemLogRunner := runtime.NewRuntime(systemLogWriter, field.NewSpanFromPool)
	systemLogRunner.SetUploadInternalAndMaxLog(3*time.Second, 10)
	// 运行SystemLogger日志器。
	go systemLogRunner.Run()
	//defer SystemLogger.Close()
	SystemLogger.SetLevel(spanLog.InfoLevel)
	SystemLogger.SetRuntime(systemLogRunner)

	// 2.初始化业务日志器，业务日志仅上报到AnyRobot，上报地址不同。
	serviceLogClient := public.NewSyncHTTPClient(public.WithAnyRobotURL("http://127.0.0.1/api/feed_ingester/v1/jobs/job-c9a577c302505576/events"),
		public.WithCompression(0), public.WithTimeout(10*time.Second), public.WithRetry(true, 5*time.Second, 30*time.Second, 1*time.Minute))
	serviceLogExporter := ar_log.NewSyncExporter(serviceLogClient)
	serviceLogWriter := open_standard.NewSyncWriter(
		encoder.NewSyncEncoder(serviceLogExporter),
		resource.LogResource())
	// 运行ServiceLogger日志器。
	//defer ServiceLogger.Close()
	ServiceLogger.SetLevel(spanLog.AllLevel)
	ServiceLogger.SetWriter(serviceLogWriter)

	// 3.运行业务代码
	ctx := context.Background()
	otel.SetTracerProvider(sdktrace.NewTracerProvider())
	ctx, num := multiply(ctx, 1, 7)
	_, _ = add(ctx, num, 8)
}
```

### 补充说明

使用异步模式上报Log数据可以配置多个上报地址，例如encoder.NewJsonEncoderWithExporters(systemLogExporter, stdoutExporter)。

使用同步模式上报Log数据只能配置一个地址，例如encoder.NewSyncEncoder(serviceLogExporter)。

如果同步模式需要上报到多个目的地，需要创建并初始化多个SyncLogger，例如：

var ServiceLogger1 = spanLog.NewSyncLogger()

var ServiceLogger2 = spanLog.NewSyncLogger()

var ServiceLogger3 = spanLog.NewSyncLogger()

## [更多示例](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go?version=GB2.6.0&path=/exporter/ar_span/examples/oneservice.go)
