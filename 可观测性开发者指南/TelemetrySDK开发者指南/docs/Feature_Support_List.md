# TelemetrySDK特性支持列表

|  语言  | sdk类别 |sdk版本  | exporter to AR版本 |数据格式规范性（link, resource等字段名统一） | 是否支持多provider |是否支持实时打印  | 编码格式兼容性 |SuperAgent数据接收器是否支持  | 在devops上是否有开发指南 |
|:-----:|:----------:|-----:|:----------:|:-----:|:----------:|-----:|:----------:|-----:|:----------:|
| go |  log   | go.opentelemetry.io/otel/sdk v1.11.2 |   v2.5.0   | 规范统一  |     否 | 支持打印到标准输出（非实时） |   json,支持gzip压缩   | 支持 |  否  |
| go |  metric   |go.opentelemetry.io/otel/sdk/metric v0.34.0  |  v2.5.0    | 规范统一 |    否  | 支持打印到标准输出（非实时） |  json,支持gzip压缩    | 支持 |    是  |
| go |  trace   | go.opentelemetry.io/otel/sdk v1.11.2 | v2.5.0     | 规范统一 |   否   | 支持打印到标准输出（非实时） |  json,支持gzip压缩    | 支持 | 是     |
| java |  log   |<groupId>com.eisoo</groupId><artifactId>TelemetrySDK-Logger</artifactId><version>2.2.3</version>|2.2.3版本的sdk内包含了可以通过http、https方式发送到AR的能力| 规范统一 |  否    | 支持打印到标准输出 |json     |  | 否    |
| java |  metric   | 	<groupId>io.opentelemetry</groupId><artifactId>opentelemetry-sdk-trace</artifactId><version>1.22.0</version> |      | 规范统一 |    否  | 支持打印到标准输出 |      |  |      |
| java |  trace   |使用官方sdk：<groupId>io.opentelemetry</groupId><artifactId>opentelemetry-sdk-trace</artifactId><version>1.22.0</version>  |<groupId>com.eisoo</groupId><artifactId>TraceExporter</artifactId><version>1.0.0</version>| 规范统一 | 否     | 支持实现打印到标准输出 |  json    |  |   否   |
| python |  log   | TelemetrySDK-Python 2.1.0 |  无    | 不规范 |    否  | 无 |   json   | 支持 |  否   |
| python |  metric   | TelemetrySDK-Python 2.2.0 |   无   | 不规范 |   否   | 无 |  json    | 支持 |   有   |
| python |  trace   | 无 |  无    | 无 |    否  | 无 |   无   | 无 |   无   |
| cpp |  log   | TelemetrySDK-Cpp 2.1.0 |   2.1.0   | 不规范 |    否  |无  |   json   | 支持 |   无   |
| cpp |  metric   | 无 |   无   | 无 |     否 | 无 |    无  | 无 |    无  |
| cpp |  trace   | 无 |    无  |  无|   否   | 无 |  无    | 无 |  无    |
