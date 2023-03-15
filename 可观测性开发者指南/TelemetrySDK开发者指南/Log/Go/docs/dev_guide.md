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
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/ar_span"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/ar_trace"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/public"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/encoder"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/exporter"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/field"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/log"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/open_standard"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/span/runtime"
	"go.opentelemetry.io/otel"
	sdktrace "go.opentelemetry.io/otel/sdk/trace"
)
```

**第2步**修改业务代码

- 修改前

```
func multiply(ctx context.Context, x, y int64) (context.Context, int64) {
	//your code here
	return ctx, x * y
}
```

- 修改后

```
func multiply(ctx context.Context, x, y int64) (context.Context, int64) {
    sampleLog.Info("this is a test")
	//your code here
	return ctx, x * y
}
```

### 上报Log数据到AnyRobot

**第1步**本地标准输出

```
var sampleLog *log.SamplerLogger

func main() {
	sampleLog = log.NewDefaultSamplerLogger()
	//logClient := public.NewHTTPClient(public.WithAnyRobotURL("http://10.4.130.68:13048/api/feed_ingester/v1/jobs/Kitty1/events"),
	//public.WithCompression(0), public.WithTimeout(10*time.Second), public.WithRetry(true, 5*time.Second, 30*time.Second, 1*time.Minute))
	//logExporter := ar_span.NewExporter(logClient)
	defaultExporter := exporter.GetStdoutExporter()
	//	output := os.Stdout
	writer := &open_standard.OpenTelemetry{
		Encoder:  encoder.NewJsonEncoderWithExporters(defaultExporter),
		Resource: field.WithServiceInfo("testServiceName", "testServiceVersion", "testServiceInstanceID"),
	}
	//writer.SetDefaultResources()
	writer.SetResourcesWithServiceInfo("testServiceName", "testServiceVersion", "testServiceInstanceID")
	run := runtime.NewRuntime(writer, field.NewSpanFromPool)

	run.SetUploadInternalAndMaxLog(3*time.Second, 10)

	sampleLog.SetRuntime(run)

	// start runtime
	go run.Run()
	sampleLog.SetLevel(log.AllLevel)

	//your code here
	ctx := context.Background()
	otel.SetTracerProvider(sdktrace.NewTracerProvider())
	sampleLog.Info("this is a test")
	ctx, num := multiply(ctx, 1, 7)
	_, _ = add(ctx, num, 8)
	sampleLog.Close()
}
```

- 在标准输出的控制台查看是否正常生产Log数据。

**第2步**获取上报地址

- 在AnyRobot管理端创建Log采集任务并生成上报地址供数据源端使用，如`https://a.b.c.d/api/feed_ingester/v1/jobs/abcd4f634e80d530/events` 。

**第3步**上报到AnyRobot

- 将获取的上报地址作为参数传入

```
var sampleLog *log.SamplerLogger

func main() {
	sampleLog = log.NewDefaultSamplerLogger()
	logClient := public.NewHTTPClient(public.WithAnyRobotURL("https://a.b.c.d/api/feed_ingester/v1/jobs/abcd4f634e80d530/events"),
	public.WithCompression(0), public.WithTimeout(10*time.Second), public.WithRetry(true, 5*time.Second, 30*time.Second, 1*time.Minute))
	logExporter := ar_span.NewExporter(logClient)
	defaultExporter := exporter.GetStdoutExporter()
	//	output := os.Stdout
	writer := &open_standard.OpenTelemetry{
		Encoder:  encoder.NewJsonEncoderWithExporters(defaultExporter, logExporter),
		Resource: field.WithServiceInfo("testServiceName", "testServiceVersion", "testServiceInstanceID"),
	}
	//writer.SetDefaultResources()
	writer.SetResourcesWithServiceInfo("testServiceName", "testServiceVersion", "testServiceInstanceID")
	run := runtime.NewRuntime(writer, field.NewSpanFromPool)

	run.SetUploadInternalAndMaxLog(3*time.Second, 10)

	sampleLog.SetRuntime(run)

	// start runtime
	go run.Run()
	sampleLog.SetLevel(log.AllLevel)

	//your code here
	ctx := context.Background()
	otel.SetTracerProvider(sdktrace.NewTracerProvider())
	sampleLog.Info("this is a test")
	ctx, num := multiply(ctx, 1, 7)
	_, _ = add(ctx, num, 8)
	sampleLog.Close()
}
```

## [更多示例](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go?version=GB2.6.0&path=/exporter/ar_span/examples/oneservice.go)
