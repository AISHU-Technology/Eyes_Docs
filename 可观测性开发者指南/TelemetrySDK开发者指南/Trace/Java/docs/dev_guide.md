# 开发指南

## 导入TelemetrySDK-Trace(Java)
**第1步** 检查版本兼容性
Trace
- 查看SDK[兼容列表](../../../docs/compatibility.md)，检查待埋点业务代码的Java版本是否符合要求。
- 在pom.xml中引入相应版本的sdk包
-
-  opentelemetry-exporter-ar-trace组件 需要依赖 opentelemetry-exporter-common组件
-  我们一共提供了有四个包：
```
  opentelemetry-exporter-common-1.0.0.jar //小包，需要有maven仓库下载其它第三方包，如io.opentelemetry
  opentelemetry-exporter-common-1.0.0-jar-with-dependencies.jar //大包，包含了所有依赖包
  opentelemetry-exporter-ar-trace-1.0.0.jar //小包，需要有maven仓库下载其它第三方包，如io.opentelemetry
  opentelemetry-exporter-ar-trace-1.0.0-jar-with-dependencies.jar   //大包，包含了所有依赖包，包括opentelemetry-exporter-common
```

#### 1.1 最加单的使用方法：对于没有maven本地仓库的情况，把trace大包（opentelemetry-exporter-ar-trace-1.0.0-jar-with-dependencies.jar）放在与项目src同级目录，用以下方法引用。使用导入本地jar文件的方式引入包，这样可以在离线环境下使用
```
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-trace</artifactId>
    <version>1.0.0</version>
    <type>jar</type>
    <scope>system</scope>
    <systemPath>${project.basedir}/opentelemetry-exporter-ar-trace-1.0.0-jar-with-dependencies.jar</systemPath>
</dependency>
```

#### 1.2  对于有maven仓库,并且可以连外网拉第三方库的情况：
- 把 opentelemetry-exporter-common-1.0.0.jar 和 opentelemetry-exporter-ar-trace-1.0.0.jar 上传的maven仓库， 然后在pom.xml里引用：
```
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-common</artifactId>
    <version>1.0.0</version>
</dependency>

<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-trace</artifactId>
    <version>1.0.0</version>
</dependency>
```

#### 1.3  对于有maven仓库,但不可以连外网拉第三方库的情况：
- 把 opentelemetry-exporter-common-1.0.0-jar-with-dependencies.jar 和 opentelemetry-exporter-ar-trace-1.0.0.jar 上传的maven仓库， 然后在pom.xml里引用：
```
<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-common</artifactId>
    <version>1.0.0</version>
    <classifier>jar-with-dependencies</classifier>
</dependency>

<dependency>
    <groupId>cn.aishu</groupId>
    <artifactId>opentelemetry-exporter-ar-trace</artifactId>
    <version>1.0.0</version>
</dependency>
```

## 使用TelemetrySDK-Trace(Java) 
以实际使用为准。
```
import cn.aishu.exporter.ar_trace.ArExporter;
import cn.aishu.exporter.common.output.HttpSender;
import cn.aishu.exporter.common.output.HttpsSender;
import cn.aishu.exporter.common.output.Retry;
import io.opentelemetry.api.trace.Span;
import io.opentelemetry.api.trace.Tracer;
import io.opentelemetry.sdk.OpenTelemetrySdk;
import io.opentelemetry.sdk.resources.Resource;
import io.opentelemetry.sdk.trace.SdkTracerProvider;
import io.opentelemetry.sdk.trace.export.SimpleSpanProcessor;
import io.opentelemetry.semconv.resource.attributes.ResourceAttributes;

public class TraceExporterTest {
    public static void main(String[] args) {
        Resource serviceNameResource =
                Resource.create(io.opentelemetry.api.common.Attributes.of(ResourceAttributes.SERVICE_NAME, "otel-jaeger-example2"));

        // Set to process the spans by the Jaeger Exporter
        SdkTracerProvider tracerProvider =
                SdkTracerProvider.builder()
                          //1. 导出到标准输出
                        .addSpanProcessor(SimpleSpanProcessor.create(new ArExporter()))
                        
                          //2. 导出到AnyRobot:注意切换到对应地址：
//                        .addSpanProcessor(SimpleSpanProcessor.create(ArExporter.create(
//                                HttpSender.create("http://10.4.15.62/api/feed_ingester/v1/jobs/job-0988e01371fd21c9/events"))))

                            //3. 导出到AnyRobot:（可配置发送相关参数）
//                        .addSpanProcessor(SimpleSpanProcessor.create(ArExporter.create(
//                                HttpSender.create("http://10.4.68.236:13048/api/feed_ingester/v1/jobs/job-4f1931764308121e/events",
//                                        Retry.create(true,5,15,30),
//                                        true, 4096))))
//
                        .setResource(Resource.getDefault().merge(serviceNameResource))
                        .build();
        OpenTelemetrySdk openTelemetry =
                OpenTelemetrySdk.builder().setTracerProvider(tracerProvider).build();

        // it's always a good idea to shut down the SDK cleanly at JVM exit.
        Runtime.getRuntime().addShutdownHook(new Thread(tracerProvider::close));
        final Tracer tracer = openTelemetry.getTracer("io.opentelemetry.example.JaegerExample2");

        Span span = tracer.spanBuilder("Start my wonderful use case2").startSpan();
        span.addEvent("Event 02");

        span.end();
    }
}
```

