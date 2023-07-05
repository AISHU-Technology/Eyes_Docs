# 使用手册

#### NewEventProvider

NewEventProvider 根据配置项，新建 EventProvider 。

```
func NewEventProvider(opts ...EventProviderOption) EventProvider 
```

#### NewExporter

NewExporter 新建Exporter，需要传入指定的数据发送客户端 public.Client 。

```
func NewExporter(c public.Client) *EventExporter
```

#### NewHTTPClient

NewHTTPClient 创建 ar_event.Exporter 需要的HTTP数据发送客户端。

```
func NewHTTPClient(opts ...config.HTTPOption) public.Client
```

#### NewStdoutClient

NewStdoutClient 创建 ar_trace.Exporter 需要的控制台+本地文件发送客户端。

```
func NewStdoutClient(stdoutPath string) public.Client
```

#### NewFileClient

NewFileClient 创建 ar_trace.Exporter 需要的本地文件发送客户端。

```
func NewFileClient(stdoutPath string) public.Client
```

#### NewConsoleClient

NewConsoleClient 创建 ar_trace.Exporter 需要的控制台发送客户端。

```
func NewConsoleClient() public.Client
```

#### WithAnyRobotURL

WithAnyRobotURL 设置 public.HTTPClient 数据上报地址。

```
func WithAnyRobotURL(URL string) config.Option
```

#### WithCompression

WithCompression 设置Event压缩方式：0代表无压缩，1代表GZIP压缩。

```
func WithCompression(compression int) config.Option
```

#### WithHeader

WithHeader 设置 public.HTTPClient 用户自定义请求头。

```
func WithHeader(headers map[string]string) config.Option
```

#### WithRetry

WithRetry 设置 public.HTTPClient 重发机制，如果显著干扰到业务运行了，请增加重发间隔maxInterval，减少最大重发时间maxElapsedTime，甚至关闭重发enabled=false。

```
func WithRetry(enabled bool, internal time.Duration, maxInterval time.Duration, ...) config.Option
```

#### WithTimeout

WithTimeout 设置 public.HTTPClient 连接超时时间。

```
func WithTimeout(duration time.Duration) config.Option
```

#### ForceFlush

ForceFlush 立即发送之前生产的 Event 数据。

```
func (ep *eventProvider) Shutdown() error
```

#### Shutdown

Shutdown 关闭 Event 生产和发送。

```
func (ep *eventProvider) Shutdown() error
```

#### SetEventProvider

SetEventProvider 设置全局唯一 EventProvider 。

```
func SetEventProvider(ep EventProvider)
```

#### GetEventProvider

GetEventProvider 获取全局唯一 EventProvider 。

```
func GetEventProvider() EventProvider
```

#### Info

Info 设置 Info 级别的事件并立即发送。

```
func Info(data interface{}, opts ...EventStartOption)
```

#### Warn

Warn 设置 Warn 级别的事件并立即发送。

```
func Warn(data interface{}, opts ...EventStartOption)
```

#### Error

Error 设置 Error 级别的事件并立即发送。

```
func Error(data interface{}, opts ...EventStartOption)
```

#### WithEventType

WithEventType 设置事件级别。

```
func WithEventType(eventType string) EventStartOption
```

#### WithTime

WithTime 设置事件发生时间。

```
func WithTime(t time.Time) EventStartOption
```

#### WithAttributes

WithAttributes 设置资源信息。

```
func WithAttributes(attrs ...Attribute) EventStartOption
```

#### WithSubject

WithSubject 设置事件操作对象。

```
func WithSubject(subject string) EventStartOption
```

#### WithSpanContext

WithSpanContext 设置关联的Trace信息。

```
func WithSpanContext(spanContext trace.SpanContext) EventStartOption
```

#### Exporters

Exporters 批量设置 EventExporter 。

```
func Exporters(exporters ...EventExporter) EventProviderOption
```

#### ServiceInfo

ServiceInfo 记录服务信息。

```
func ServiceInfo(ServiceName string, ServiceVersion string, ServiceInstance string) EventProviderOption
```

#### FlushInternal

FlushInternal 设置发送间隔。

```
func FlushInternal(flushInternal time.Duration) EventProviderOption
```

#### MaxEvent

MaxEvent 设置Event发送上限。

```
func MaxEvent(maxEvent int) EventProviderOption
```

#### SetServiceInfo

SetServiceInfo 设置服务信息，包括服务名、版本号、实例ID。

```
func SetServiceInfo(name string, version string, instance string)
```

#### EventResource

EventResource 传入 Event 的默认resource。

```
func EventResource() eventsdk.EventProviderOption
```
