# 开发指南

## 导入TelemetrySDK-Metric(Go)

**第1步**检查版本兼容性

- 查看SDK[兼容列表](../../../docs/compatibility.md)，检查待埋点业务代码的Go版本是否符合要求。

**第2步**配置项目下载权限

- 从[ONE-Architecture](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go)
  配置代码仓库下载权限，以便后续从ONE-Architecture拉取代码[参考文档](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/Eyes_Docs?path=/可观测性开发者指南/TelemetrySDK开发者指南/Log/README.md&version=GBdevelop&_a=preview&anchor=sdk2.0-使用参考)

**第3步**执行以下命令引入TelemetrySDK-Metric(Go)

```
go get go.opentelemetry.io/otel/sdk/metric@v0.34.0
```

**第4步**(可选)更新TelemetrySDK-Metric(Go)

- 查看Telemetry[版本列表](https://pkg.go.dev/go.opentelemetry.io/otel/sdk/metric?tab=versions)，选择希望引入的版本，例如v0.34.0，替换末尾的版本号重新执行命令。

```
go get go.opentelemetry.io/otel/sdk/metric@v0.34.0
```

## 导入Metric Exporter

**第1步**执行以下命令引入Metric Exporter

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter@2.6.1
go mod tidy
```

**第2步**(可选)更新Metric Exporter

- 查看SDK[兼容列表](../../../docs/compatibility.md)，选择希望引入的版本，例如2.6.1，替换末尾的版本号重新执行命令。

```
go get devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter@2.6.1
go mod tidy
```

## 使用TelemetrySDK-Metric(Go)进行代码埋点生产指标数据

**第1步**新增依赖：以下为新增汇总，以实际使用为准。

```
import (
	"context"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/ar_metric"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/public"
	"devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go.git/exporter/version"
	"go.opentelemetry.io/otel/attribute"
	"go.opentelemetry.io/otel/metric"
	"go.opentelemetry.io/otel/metric/instrument"
	"go.opentelemetry.io/otel/metric/unit"
	sdkmetric "go.opentelemetry.io/otel/sdk/metric"
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
	attrs := []attribute.KeyValue{
		attribute.Key("用户信息").String("在线用户数"),
	}
	gauge, _ := ar_metric.Meter.AsyncInt64().Gauge("gauge：用户数峰值", instrument.WithUnit(unit.Dimensionless), instrument.WithDescription("a simple gauge"))
	gaugeTest := func(ctx context.Context) {
		gauge.Observe(ctx, 12, attrs...)
	}
	_ = ar_metric.Meter.RegisterCallback([]instrument.Asynchronous{gauge}, gaugeTest)

	// 业务代码
	return ctx, x * y
}
```

### 上报指标数据到AnyRobot

**第1步**本地调试

```
func main() {
	ctx := context.Background()
	metricClient := public.NewStdoutClient("./AnyRobotMetric.txt")
	metricExporter := ar_metric.NewExporter(metricClient)
	public.SetServiceInfo("YourServiceName", "1.0.0", "983d7e1d5e8cda64")
	metricProvider := sdkmetric.NewMeterProvider(
		sdkmetric.WithReader(sdkmetric.NewPeriodicReader(metricExporter, sdkmetric.WithInterval(10*time.Second), sdkmetric.WithTimeout(10*time.Second))),
		sdkmetric.WithResource(ar_metric.MetricResource()),
	)
	defer func() {
		if err := metricProvider.Shutdown(ctx); err != nil {
			log.Println(err)
		}
	}()
	ar_metric.Meter = metricProvider.Meter(version.MetricInstrumentationName, metric.WithSchemaURL(version.MetricInstrumentationURL), metric.WithInstrumentationVersion(version.TelemetrySDKVersion))

	// 业务代码
	ctx, num := add(ctx, 2, 8)
	ctx, num = multiply(ctx, num, 7)
	log.Println(result, num)
}
```

- 在同级目录找到本地文件AnyRobotMetric.txt，查看是否正常生产指标数据。

**第2步**获取上报地址

- 在AnyRobot管理端创建Metric采集任务并生成上报地址供数据源端使用，如`http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events` 。

**第3步**上报到AnyRobot

- 将获取的上报地址作为参数传入

```
func main() {
	ctx := context.Background()
	// metricClient := public.NewStdoutClient("./AnyRobotMetric.txt")
	metricClient := public.NewHTTPClient(public.WithAnyRobotURL("http://127.0.0.1/api/feed_ingester/v1/jobs/job-983d7e1d5e8cda64/events"),
		public.WithCompression(1), public.WithTimeout(10*time.Second), public.WithRetry(true, 5*time.Second, 30*time.Second, 1*time.Minute))
	metricExporter := ar_metric.NewExporter(metricClient)
	public.SetServiceInfo("YourServiceName", "1.0.0", "983d7e1d5e8cda64")
	metricProvider := sdkmetric.NewMeterProvider(
		sdkmetric.WithReader(sdkmetric.NewPeriodicReader(metricExporter, sdkmetric.WithInterval(10*time.Second), sdkmetric.WithTimeout(10*time.Second))),
		sdkmetric.WithResource(ar_metric.MetricResource()),
	)
	defer func() {
		if err := metricProvider.Shutdown(ctx); err != nil {
			log.Println(err)
		}
	}()
	ar_metric.Meter = metricProvider.Meter(version.MetricInstrumentationName, metric.WithSchemaURL(version.MetricInstrumentationURL), metric.WithInstrumentationVersion(version.TelemetrySDKVersion))

	// 业务代码
	ctx, num := add(ctx, 2, 8)
	ctx, num = multiply(ctx, num, 7)
	log.Println(result, num)
}
```

## [更多示例](https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go?path=%2Fexporter%2Far_metric%2Fexamples%2Fsingle_service_with_metric.go&version=GB2.6.1&_a=contents)
