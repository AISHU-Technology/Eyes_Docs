在 SuperAgent 框架下，所有的采集配置由 IngestManager 服务统一创建和管理，并持久化存储在数据库中，每个采集配置由以下部分组成：
- Input： 配置采集数据源，不同的采集器对应的采集数据源不同，因此采集配置也会有很大差异，可以通过在设置 key-value 来扩展配置项
- Processor： 配置对采集数据进行解析、加工、过滤等预处理，
- Output： 配置数据上报端，比如 Kafka

采集配置示例：

```
{
		"input":{
			"type":"json_anyrobot_metric",
			"receiver":["http"]
		},
		"processors":[
			{
				"add_metadata":{
					"type":"json_anyrobot_metric_default",
					"tags":[]
				}
			},
			{
				"add_labels":{
					"labels":{}
				}
			},
			{
				"add_host_attributes":{}
			}
		]
        "output":{
			"type":"",
			"kafka": {
					"address": ["10.4.68.149:9092"],
					"user" : "feeder_kafka",
					"password": "sdhoqwudhjxdbjahd",
					"topic": "kafka_topic"
			}
		},
	}
```

为了方便统一管理采集配置，并降低开发者的门槛，我们将采集配置统一维护在 [ingest](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/DE_IngestPkg?path=%2Fconfig%2Fingest) 目录 ，每个采集器有独立的采集配置目录，并支持管理不同版本采集配置。
同时抽离了 [common](https://devops.aishu.cn/AISHUDevOps/AnyRobot/_git/DE_IngestPkg?path=%2Fconfig%2Fingest%2Fcommon)目录来管理公共的采集配置，比如 Kafka Output，公用的一些 Processors 等 。
