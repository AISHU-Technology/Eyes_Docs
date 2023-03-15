# 使用手册

#### NewDefaultSamplerLogger

NewDefaultSamplerLogger 生成默认的日志器。

```
func NewDefaultSamplerLogger() *SamplerLogger
```

#### GetStdoutExporter

GetStdoutExporter 生成一个标准输出的 Exporter ，可打印日志数据至控制台。

```
func GetStdoutExporter() LogExporter
```

#### NewExporter

NewExporter 生成 Exporter 对象，需要传入指定的数据发送客户端 public.Client 。

```
func NewExporter(c public.Client) *public.Exporter
```

#### NewHTTPClient

NewHTTPClient 创建 Exporter 需要的HTTP数据发送客户端。

```
func NewHTTPClient(opts ...config.HTTPOption) public.Client
```

#### WithAnyRobotURL

WithAnyRobotURL 设置 public.HTTPClient 数据上报地址。

```
func WithAnyRobotURL(URL string) config.Option
```

#### WithCompression

WithCompression 设置 Trace 压缩方式：0代表无压缩，1代表GZIP压缩。

```
func WithCompression(compression int) config.Option
```

#### WithHeader

WithHeader 设置 public.HTTPClient 用户自定义请求头。

```
func WithHeader(headers map[string]string) config.Option
```

#### WithRetry

WithRetry 设置 public.HTTPClient 重发机制，如果显著干扰到业务运行了，请增加重发间隔 maxInterval，减少最大重发时间 maxElapsedTime，甚至关闭重发enabled=false。

```
func WithRetry(enabled bool, internal time.Duration, maxInterval time.Duration, ...) config.Option
```

#### WithTimeout

WithTimeout 设置 public.HTTPClient 连接超时时间。

```
func WithTimeout(duration time.Duration) config.Option
```

#### NewJsonEncoder

NewJsonEncoder 返回一个 Encoder 对象，传入实现 io.Writer 的对象，兼容老版本。

```
func NewJsonEncoder(w io.Writer) Encoder
```

#### NewJsonEncoderWithExporters

NewJsonEncoderWithExporters 返回一个 Encoder 对象，可传入多个创建的 Exporter 对象。

```
func NewJsonEncoderWithExporters(exporters ...exporter.LogExporter) Encoder
```

#### WithServiceInfo

WithServiceInfo 生成一个带有 Service 信息的 Field，需要依次传入微服务名称、微服务版本、微服务实例ID。

```
func WithServiceInfo(ServiceName string, ServiceVersion string, ServiceInstanceID string) Field
```

#### WithAttribute

WithAttribute 在记日志时传入 Attributes 信息，生成一个 Log 选项。

```
func WithAttribute(attr *attribute) LogOptionFunc
```

#### WithContext

WithContext 在记日志时传入包含 SpanContext 的 context 信息，生成一个 Log 选项。

```
func WithContext(ctx context.Context) LogOptionFunc
```

#### SetDefaultResources

SetDefaultResources 设置默认的 Resource 信息。

```
func (o *OpenTelemetry) SetDefaultResources()
```

#### SetResourcesWithServiceInfo

SetResourcesWithServiceInfo 设置带有微服务信息的 Resource 信息，需要依次传入微服务名称、微服务版本、微服务实例ID。

```
func (o *OpenTelemetry) SetResourcesWithServiceInfo(ServiceName string, ServiceVersion string, ServiceInstanceID string)
```

#### SetUploadInternalAndMaxLog

SetUploadInternalAndMaxLog 设置间隔多少秒发送一次 Log 数据以及达到多少 Log 数量发送一次，需要依次传入间隔时间以及达到发送的 Log 数量。

```
func (r *Runtime) SetUploadInternalAndMaxLog(Internal time.Duration, MaxLog int)
```

#### SetLevel

SetLevel 设置输出的日志级别，日志级别在 log 包中进行选择。

```
func (s *SamplerLogger) SetLevel(level int)
```

#### Trace

Trace 设置 Trace 级别的非结构化日志数据，可以传入 Body 中的 message 以及设置 Log 选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) Trace(message string, options ...field.LogOptionFunc)
```

#### Debug

Debug 设置 Debug 级别的非结构化日志数据，可以传入 Body 中的 message 以及设置 Log 选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) Debug(message string, options ...field.LogOptionFunc)
```

#### Info

Info 设置 Info 级别的非结构化日志数据，可以传入 Body 中的 message 以及设置 Log 选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) Info(message string, options ...field.LogOptionFunc)
```

#### Warn

Warn 设置 Warn 级别的非结构化日志数据，可以传入 Body 中的 message 以及设置 Log选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) Warn(message string, options ...field.LogOptionFunc)
```

#### Error

Error 设置 Error 级别的非结构化日志数据，可以传入 Body 中的 message 以及设置 Log选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) Error(message string, options ...field.LogOptionFunc)
```

#### Fatal

Fatal 设置 Fatal 级别的非结构化日志数据，可以传入 Body 中的 message 以及设置 Log选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) Fatal(message string, options ...field.LogOptionFunc)
```

#### TraceField

TraceField 设置 Trace 级别的结构化日志数据，可传入 Body 中的 message 和 type ，也可设置 Log 选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) TraceField(message field.Field, typ string, options ...field.LogOptionFunc)
```

#### DebugField

DebugField 设置 Debug 级别的结构化日志数据，可传入 Body 中的 message 和 type ，也可设置 Log 选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) DebugField(message field.Field, typ string, options ...field.LogOptionFunc)
```

#### InfoField

InfoField 设置 Info 级别的结构化日志数据，可传入 Body 中的 message 和 type ，也可设置 Log 选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) InfoField(message field.Field, typ string, options ...field.LogOptionFunc)
```

#### WarnField

WarnField 设置 Warn 级别的结构化日志数据，可传入 Body 中的 message 和 type ，也可设置 Log 选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) WarnField(message field.Field, typ string, options ...field.LogOptionFunc)
```

#### ErrorField

ErrorField 设置 Error 级别的结构化日志数据，可传入 Body 中的 message 和 type ，也可设置 Log 选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) ErrorField(message field.Field, typ string, options ...field.LogOptionFunc)
```

#### FatalField

FatalField 设置 Fatal 级别的结构化日志数据，可传入 Body 中的 message 和 type ，也可设置 Log 选项，包含 SpanContext 的 context 和 Attributes。

```
func (s *SamplerLogger) FatalField(message field.Field, typ string, options ...field.LogOptionFunc)
```