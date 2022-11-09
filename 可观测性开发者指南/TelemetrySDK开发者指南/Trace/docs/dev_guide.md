# 开发指南

## 安装导入TelemetrySDK

1. 检查版本[兼容性](compatibility.md)
2. 配置项目[下载权限](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/Eyes_Docs?path=/可观测性开发者指南/TelemetrySDK开发者指南/Log/README.md&version=GBdevelop&_a=preview&anchor=sdk2.0-使用参考)
3. 引入TelemetrySDK：

```
go get go.opentelemetry.io/otel@v1.10.0
go get go.opentelemetry.io/otel/sdk/trace@v1.10.0
```

4. (可选)更新TelemetrySDK：步骤等同于引入TelemetrySDK。

## 安装导入Trace Exporter

1. 引入Trace Exporter：
   ```go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporters/artrace@2.2.0```
2. (可选)更新Trace Exporter：步骤等同于引入Trace Exporter。

## 使用TelemetrySDK进行代码埋点生产链路数据

1. 新增依赖：以下为新增汇总，以实际使用为准。

```
import (
"context"
"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporters/artrace"
"go.opentelemetry.io/otel"
"go.opentelemetry.io/otel/attribute"
"go.opentelemetry.io/otel/sdk/resource"
sdktrace "go.opentelemetry.io/otel/sdk/trace"
"go.opentelemetry.io/otel/trace"
"log")
```

2. 修改业务：`logics.go`

```
func multiply(ctx context.Context, x, y int64) (context.Context, int64) {
	ctx, span := artrace.Tracer.Start(ctx, "乘法")
	defer span.End()

	//your code here
	return ctx, x * y
}
```

3. 修改入口：`main.go`

```
func main() {
	ctx := context.Background()
	client := artrace.NewStdoutClient("")
	//client := artrace.NewHTTPClient(artrace.WithAnyRobotURL("http://a.b.c.d/api/feed_ingester/v1/jobs/abcd4f634e80d530/events"))
	exporter := artrace.NewExporter(client)
	tracerProvider := sdktrace.NewTracerProvider(sdktrace.WithBatcher(exporter), sdktrace.WithResource(artrace.GetResource("YourServiceName", "1.0.0", "")))
	otel.SetTracerProvider(tracerProvider)
	defer func() {
		if err := tracerProvider.Shutdown(ctx); err != nil {
			log.Println(err)
		}
	}()

	//your code here
	ctx, num := multiply(ctx, 2, 3)
	ctx, num = multiply(ctx, num, 7)
}
```

## [最佳实践]()

### 上报链路数据到AnyRobot

正确填写上报地址：`NewHTTPClient("http://a.b.c.d/")`，参数从AnyRobot网页端获取。

修改入口：`main.go`

```
func main() {
	ctx := context.Background()
	//client := artrace.NewStdoutClient("")
	client := artrace.NewHTTPClient(artrace.WithAnyRobotURL("http://a.b.c.d/api/feed_ingester/v1/jobs/abcd4f634e80d530/events"))
	exporter := artrace.NewExporter(client)
	tracerProvider := sdktrace.NewTracerProvider(sdktrace.WithBatcher(exporter), sdktrace.WithResource(artrace.GetResource("YourServiceName", "1.0.0", "")))
	otel.SetTracerProvider(tracerProvider)
	defer func() {
		if err := tracerProvider.Shutdown(ctx); err != nil {
			log.Println(err)
		}
	}()

	//your code here
	ctx, num := multiply(ctx, 2, 3)
	ctx, num = multiply(ctx, num, 7)
}
```

### 开启/关闭 生产和发送链路数据

注册/解绑Trace Provider来开启/关闭链路数据的生产和发送：

```
func main() {
	ctx := context.Background()
	client := artrace.NewStdoutClient("./AnyRobotTrace.txt")
	exporter := artrace.NewExporter(client)
	tracerProvider := sdktrace.NewTracerProvider(sdktrace.WithBatcher(exporter), sdktrace.WithResource(artrace.GetResource("YourServiceName", "1.0.0", "")))
	otel.SetTracerProvider(tracerProvider)
	defer func() {
		if err := tracerProvider.Shutdown(ctx); err != nil {
			log.Println(err)
		}
	}()

	ctx, num := multiply(ctx, 2, 3)
	ctx, num = multiply(ctx, num, 7)
	//调用ForceFlush之后会立即发送之前生产的2次乘法链路。
	_ = tracerProvider.ForceFlush(ctx)
	//关闭Trace的发送，这3次加法产生的链路不会发送。
	tracerProvider.UnregisterSpanProcessor(sdktrace.NewBatchSpanProcessor(exporter))
	ctx, num = add(ctx, num, 8)
	ctx, num = add(ctx, num, 9)
	ctx, num = add(ctx, num, 10)
	//开启Trace的发送，这1次乘法产生的链路会发送。
	tracerProvider.RegisterSpanProcessor(sdktrace.NewBatchSpanProcessor(exporter))
	ctx, num = multiply(ctx, num, 9)
	log.Println(result, num)
}
```

## [更多示例](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go?version=GB2.2.0&path=/exporters/artrace/examples/oneservice.go)