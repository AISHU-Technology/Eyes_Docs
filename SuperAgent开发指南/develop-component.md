采集器内部包含以下关键组件，均通过接口做了抽象定义， 开发者可以基于接口实现特定的采集能力, 建议参考已经实现的采集器代码

## 数据接口

### Event
```
type Event interface {
	Body() []byte
	//FillBody([]byte) error
	GetError() error
	SetError(error)
	GetMetadata() map[string]interface{}
	SetMetadata(string, interface{})
	GetData(string) interface{}
	SetData(string, interface{})
}
```

### Batch
```
type Batch interface {
	Events() []Event
}
```

## Piplilne 组件

### Input 组件

```
type Input interface {
	Init(ctx context.Context, cfg interface{}) error
	Start()
	Stop()
	Gather(processor ProcessFunc)
}
```

### Processor 组件

```
type Processor interface {
	Init(cfg interface{}) error
	Start() error
	Process(event Event)
	Stop()
}
```

### Queue 组件

```
type Queue interface {
	Init()
	Start()
	Stop()
	In(event Event)
	OutChan() chan Batch
}
```

### Output 组件

```
type Output interface {
	Init(ctx context.Context, cfg interface{}) error
	Start() error
	AsyncPublish(chan Batch)
	Stop()
}
```

### Job 组件

```
type Job interface {
	Init(ctx context.Context, cfg interface{}) error
	Start() error
	Stop()
}
```