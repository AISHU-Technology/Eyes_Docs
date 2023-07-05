# 使用手册

#### NewMeterProvider

NewMeterProvider 根据配置项，新建 NewMeterProvider 。

```
func NewMeterProvider(options ...Option) *MeterProvider 
```

#### NewExporter

NewExporter 新建Exporter，需要传入指定的数据发送客户端 public.Client 。

```
func NewExporter(c public.Client) *MetricExporter
```

#### NewHTTPClient

NewHTTPClient 创建 ar_metric.Exporter 需要的HTTP数据发送客户端。

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

WithCompression 设置Metric压缩方式：0代表无压缩，1代表GZIP压缩。

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

#### WithReader

WithReader 设置 Reader。

```
func WithReader(r Reader) Option
```

#### NewPeriodicReader

NewPeriodicReader 设置 Exporter。

```
func NewPeriodicReader(exporter Exporter, options ...PeriodicReaderOption) Reader
```

#### SetServiceInfo

SetServiceInfo 设置服务信息，包括服务名、版本号、实例ID。

```
func SetServiceInfo(name string, version string, instance string)
```

#### MetricResource

MetricResource 传入 Metric 的默认resource。

```
func MetricResource() *sdkresource.Resource
```
