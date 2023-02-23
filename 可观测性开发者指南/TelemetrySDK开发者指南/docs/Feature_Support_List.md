# TelemetrySDK特性支持列表

|  语言  | sdk类别 |sdk版本  | exporter to AR版本 |数据格式规范性（link, resource等字段名统一） | 是否支持多provider |是否支持实时打印  | 编码格式兼容性 |是否支持实时打印  | 编码格式兼容性 |
|:-----:|:----------:|-----:|:----------:|:-----:|:----------:|-----:|:----------:|-----:|:----------:|
| go |  log   |  |      |  |     否 |  |      |  |   是   |
| go |  metric   |  |      |  |    否  |  |      |  |      |
| go |  trace   |  |      |  |   否   |  |      |  |      |
| java |  log   |<groupId>com.eisoo</groupId><artifactId>TelemetrySDK-Logger</artifactId><version>2.2.3</version>|2.2.3版本的sdk内包含了可以通过http、https方式发送到AR的能力| 规范统一 |  否    | 支持实现打印到标准输出 |     |  | 否    |
| java |  metric   |  |      |  |    否  |  |      |  |      |
| java |  trace   |使用官方sdk：<groupId>io.opentelemetry</groupId><artifactId>opentelemetry-sdk-trace</artifactId><version>1.22.0</version>  |<groupId>com.eisoo</groupId><artifactId>TraceExporter</artifactId><version>1.0.0</version>| 规范统一 | 否     | 支持实现打印到标准输出 |      |  |   否   |
| python |  log   |  |      |  |    否  |  |      |  |      |
| python |  metric   |  |      |  |   否   |  |      |  |      |
| python |  trace   |  |      |  |    否  |  |      |  |      |
| cpp |  log   |  |      |  |    否  |  |      |  |      |
| cpp |  metric   |  |      |  |     否 |  |      |  |      |
| cpp |  trace   |  |      |  |   否   |  |      |  |      |
