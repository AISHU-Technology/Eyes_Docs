## 性能数据参考

## Python

### Trace(Python)

### 模拟真实场景性能测试

#### 发送anyrobot性能测试

测试用例：https://devops.aishu.cn/AISHUDevOps/AnyRobot/_testPlans/execute?planId=400838&suiteId=400898

性能测试：发送anyrobot性能测试

说明：

1. 测试应用A， http for循环创建50万条span数据（2KB/Span）
2. 记录创建50万条数据发送完成需要多长时间

结论：

1. 发送性能较差，117秒可发送50万条数据（go语言是 3.2秒）
2. TPS为4273span/秒，由于是同步发送，所以每一条span会拖累业务代码0.23毫秒
3. 并且需要如下图修改默认配置，不然数据太多会被丢弃
4. 修改配置后发送，没有数据丢失

解决方案：

如图修改provider的配置，提高队列大小等，但是相应的会增加等量的内存占用，需要用户自行判断。 修改默认配置图：

![PythonBatchSpanProcessor.png](../images/PythonBatchSpanProcessor.png)

![PythonTraceARPerformance.png](../images/PythonTraceARPerformance.png)

#### 发送标准输出性能测试

说明：

1. 测试应用A， http for循环创建50万条span数据（2KB/Span）
2. 记录创建50万条数据发送完成需要多长时间

结论：

1. 发送性能较差，105秒可发送50万条数据
2. TPS为4761span/秒
3. 无数据丢失

![PythonTraceConsolePerformance.png](../images/PythonTraceConsolePerformance.png)

### Log(Python)

### 模拟真实场景性能测试

说明：

1. Python原生标准输出打印5万条日志字符串
2. TelemetrySDK控制台打印5万条日志

结论：

1. print性能结果： 耗时：3.6秒
2. Python-logger性能结果：耗时：3.2秒
3. TelemetrySDK-Log(Python)与print作为对比，性能接近，测试通过

![PythonPrintPerformance.png](../images/PythonPrintPerformance.png)

![PythonSamplerLoggerPerformance1.png](../images/PythonSamplerLoggerPerformance1.png)

### 发送anyrobot性能测试

说明：

1. 生产50万条日志并上报AnyRobot
2. 推荐修改python env参数，将队列大小与组batch的大小长度设置合理
3. 如果设置过大，则会导致批量发送大小超过5MB，数据接收器拒绝接收，HTTP发送失败
4. 如果设置过小，则会导致日志器处理速度慢，可能会丢数据

![PythonSamplerLoggerEnv.png](../images/PythonSamplerLoggerEnv.png)

结论：

1. 生产50万条日志数据并上报AnyRobot，共耗时35秒。平均14285条日志/秒

![PythonSamplerLoggerPerformance2.png](../images/PythonSamplerLoggerPerformance2.png)