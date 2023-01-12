# 使用手册

#### Tracer

Tracer 是一个全局变量，用于在业务代码中生产Span。

```
ar_trace.Tracer.Start(ctx context.Context, spanName string, opts ...SpanStartOption) (context.Context, Span)
```

#### SetAttributes

SetAttributes 用于在Span中添加和业务相关的有语义的一系列键值对。

```
func SetAttributes(kv ...attribute.KeyValue)
```

#### AddEvent

AddEvent 用于在Span中添加某一时刻发生的有意义的事件。

```
func AddEvent(name string, options ...EventOption)
```

#### SetServiceInfo

SetServiceInfo 设置服务信息，包括服务名、版本号、实例ID。

```
func SetServiceInfo(name string, version string, instance string)
```

#### TraceResource

TraceResource 传入 Trace 的默认Resource。

```
func TraceResource() *sdkresource.Resource
```

#### NewExporter

NewExporter 新建Exporter，需要传入指定的数据发送客户端 public.Client 。

```
func NewExporter(c public.Client) *public.Exporter
```

#### NewHTTPClient

NewHTTPClient 创建 ar_trace.Exporter 需要的HTTP数据发送客户端。

```
func NewHTTPClient(opts ...config.HTTPOption) public.Client
```

#### NewStdoutClient

NewStdoutClient 创建 ar_trace.Exporter 需要的Local数据发送客户端。

```
func NewStdoutClient(stdoutPath string) public.Client
```

#### WithAnyRobotURL

WithAnyRobotURL 设置 public.HTTPClient 数据上报地址。

```
func WithAnyRobotURL(URL string) config.Option
```

#### WithCompression

WithCompression 设置Trace压缩方式：0代表无压缩，1代表GZIP压缩。

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

ForceFlush 立即发送之前生产的链路数据。

```
func (p *TracerProvider) ForceFlush(ctx context.Context) error
```

#### RegisterSpanProcessor

RegisterSpanProcessor 注册 SpanProcessor 来生产发送链路数据。

```
func (p *TracerProvider) RegisterSpanProcessor(s SpanProcessor)
```

#### UnregisterSpanProcessor

UnregisterSpanProcessor 解绑 SpanProcessor 来停止生产发送链路数据。

```
func (p *TracerProvider) UnregisterSpanProcessor(s SpanProcessor)
```
