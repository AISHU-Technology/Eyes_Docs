# Java SDK使用指南

## opentelemetry metric基本术语

### opentelemetry metric层级结构
```
ResourceMetric
│
├── Resource(kvArray) //resource数据，包含该指标数据的来源细节等数据
│
└── ScopeMetrics(struct)
    │
    ├── Scope(struct)   //Scope数据，包含检测库(instrumentation)的具体信息
    │      ├── Name(string)      //检测库的名称，没有使用检测库则为sdk的名称
    │      ├── Version(string)   //检测库的版本，
    │      └── SchemaURL(string) //检测库的URL
    │
    └── Metrics(Array) //具体指标数据的数组
        │
        └── Metric(struct)
                ├── Name(string)         //指标名
                ├── Description(string)  //指标的描述
                ├── Unit(string)         //指标的单位
                └── Data(struct)         //指标的具体格式，
                                         //分别可以是Gauge(度量),Sum(求和),Histogram(直方图)


Gauge
│
└── DataPoints(Array) //数据点数组

Sum
│
├── DataPoints(Array)   //数据点数组
├── Temporality(string) //累计性质，可以为unset(未设置),cumulative(累计的),delta(增量的)
└── IsMonotonic(bool)   //是否存在单调性

Histogram
│
├── DataPoints(Array)   //数据点数组,具有特殊的HistogramDatapoint格式
└── Temporality(string) //累计性质，可以为unset(未设置),cumulative(累计的),delta(增量的)


```

- 检测库(instrumentation)，用于对于一些特定场景的数据采集的集成，通过检测库可省略部分埋点工作的编码
- 累计性质(temporality),cumulative(累计的)指当开始采集后所有的累计总量，而delta(增量的)指当前时间段内的累计总量。



## opentelemetry metric SDK基本使用

### Resource的设置
- Resource即为层级结构中最上层的Resource，用于设置一些关于所需采集的系统，以及用于采集的SDK的基础信息

```
//自定义所需的attributes示例
//构建所需的自定义attributes
AttributesBuilder attributesBuilder = Attributes.builder();
attributesBuilder.put("test", 1, 2).put("test2", 1);
Attributes attribute = attributesBuilder.build();

Resource resource = Resource.create(attribute);
```

```
//直接使用默认的Resources
Resource resource = Resource.getDefault();
```

### MetricReader的创建
- MetricReader用于根据不同的规则从Provider中拉取数据,处理来自sdk的ForceFlush和ShutDown信号
- 构建MetricReader时，至少需要提供MetricExporter实例，可选择提供数据的累计性质和默认的聚合方式(histogram使用)
- 在当前示例中,使用的是PeriodicMetricReader,该Reader负责根据设置的时间定时拉取注册的Provider
- 同一个Provider中可以注册多个Reader,也就是说可以并列使用多种拉取规则
- (但是不允许将一个Reader注册到多个Provider中)
```
//设置采集间隔为一天
PeriodicMetricReader reader = PeriodicMetricReader.builder(传入的Exporter实例).setInterval(Duration.ofDays(1)).build();
```

### MeterProvider的创建
- MeterProvider用于创建Meter对象，在创建MeterProvider这步，会注册对应的Reader,以及设置公用的Resources
```
SdkMeterProvider sdkMeterProvider = SdkMeterProvider.builder()
                                .registerMetricReader(reader)
                                .setResource(resource)
                                .build();
```

### Meter的创建
- Meter用于注册具体的指标值，对于手动埋点的情况，由于未使用现有的instrumentation,因此上传自定义的Instrumentation Name
```
Meter meter = sdkMeterProvider.meterBuilder(instrumentScopeName).build();
```

### 注册需要采集的指标
- 以长整型的增减计数器为例：
```
LongUpDownCounter upDownCounter = meter.upDownCounterBuilder("queue_size")//指标名称
                                .setDescription("queue size")//指标描述
                                .setUnit("1")//指标单位
                                .build();
```
由于注册的类型为增减计数器，因此生成的Metric数据中单调性一栏会为False

### 进行指标的记录
```
//可传入指定的attributes，记录额外的label信息
upDownCounter.add(10, attributes);
upDownCounter.add(-5, attributes);
```

