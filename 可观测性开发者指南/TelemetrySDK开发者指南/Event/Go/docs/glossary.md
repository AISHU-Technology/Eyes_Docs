# [术语表](https://confluence.aishu.cn/pages/viewpage.action?pageId=164298403)
## Event数据格式
|     **字段**     | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**                                                                                                              |
|:--------------:|:------:|:--------:|:--------:|:--------:|:--------------------------------------------------------------------------------------------------------------------|
|  **EventID**   | string |    ✓     |          |    ✓     | 事件的唯一标识符：ULID，一个16字节的数组，且至少有一个非0字节。打印时会转成26个16进制的字符。示例：“0000XSNJG0MQJHBF4QX1EFD6Y3”。                                |
| **EventType**  | string |    ✓     |    ✓     |          | 事件的类型：用户来定义有哪些事件类型。示例：“com.github.pull.create”或者“com.example.object.delete.v2”或者“alcvc:MeetingEvent:MemberOperate”。 |
|    **Time**    | string |    ✓     |          |    ✓     | 事件的时间戳：创建事件对象时自动读取系统时间。格式为RFC3339Nano。示例：“2022-10-18T14:12:03.9053361+08:00”。                                       |
|   **Level**    | string |    ✓     |    ✓     |          | 事件的级别：总共3种，“ERROR，WARN，INFO”。默认INFO。                                                                                |
| **Attributes** |  map   |    ✓     |    ✓     |          | 事件的属性：业务相关的键值对。键和值都必须非空且有语义。                                                                                        |
|  **Resource**  | object |    ✓     |          |    ✓     | 事件的资源信息：记录服务资源，包括主机信息和容器信息。                                                                                         |
|  **Subject**   | string |    ✕     |    ✓     |          | 事件的主题：未设置时为空字符串""。                                                                                                  |
|    **Link**    | array  |    ✕     |    ✓     |          | 事件的关联信息：记录事件和链路之间的关联。未设置时隐藏不显示。                                                                                     |
|    **Data**    | object |    ✓     |    ✓     |          | 事件的内容：大小应该不超过128KB。                                                                                                 |

## Resource数据格式
|           **字段**           | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**    | **示例**                               |
|:--------------------------:|:------:|:--------:|:--------:|:--------:|:----------|:-------------------------------------|
|        **host.ip**	        | string |    ✓     |          |    ✓     | 主机IP地址    | 1.2.3.4                              |
|       **host.arch**	       | string |    ✓     |          |    ✓     | 主机CPU架构   | amd64、arm64                          |
|       **host.name**	       | string |    ✓     |          |    ✓     | 主机名称      | Desk-028                             |
|        **os.type**	        | string |    ✓     |          |    ✓     | 操作系统类型    | windows、linux                        |
|      **os.version**	       | string |    ✓     |          |    ✓     | 操作系统版本    | CentOS 7.7                           |
|     **os.description**     | string |    ✓     |          |    ✓     | 操作系统详细信息  | Microsoft Windows 10 Enterprise      |
|    **service.instance**    | string |    ✕     |          |          | 服务实例的ID   | 627cc493-f310-47de-96bd-71410b7dec09 |
|      **service.name**      | string |    ✓     |    ✓     |          | 服务名称      | ShoppingMart                         |
|    **service.version**     | string |    ✓     |    ✓     |          | 服务的版本号    | 5.3.0                                |
| **telemetry.sdk.language** | string |    ✓     |          |    ✓     | SDK的开发语言  | go                                   |
|   **telemetry.sdk.name**   | string |    ✓     |          |    ✓     | SDK名称     | TelemetrySDK-Go/exporters/ar_event   |
| **telemetry.sdk.version**  | string |    ✓     |          |    ✓     | SDK版本号    | v2.2.0                               |
|  **k8s.namespace.name**	   | string |    ✓     |          |    ✓     | k8s命名空间   | anyshare                             |
|     **k8s.pod.name**	      | string |    ✓     |          |    ✓     | k8s pod名称 | efast-123456789-12345                |
|     **k8s.node.name**	     | string |    ✓     |          |    ✓     | k8s节点名称   | node01                               |

# Event数据样例
```json
[
	{
		"EventID": "01GPJ1Q7SBE11D8CK9FZHP00FF",
		"EventType": "Default.EventType",
		"Time": "2023-01-12T11:52:31.7872427+08:00",
		"Level": "WARN",
		"Attributes": {
			"key": false
		},
		"Resource": {
			"host": {
				"arch": "x86_64",
				"ip": "10.4.34.50",
				"name": "DevDeskV6-029"
			},
			"os": {
				"description": "Microsoft Windows 10 Enterprise",
				"type": "windows",
				"version": "10.0.18363.959 Build 18363.959"
			},
			"service": {
				"instance": "",
				"name": "YourServiceName",
				"version": "1.0.0"
			},
			"telemetry": {
				"sdk": {
					"language": "go",
					"name": "TelemetrySDK-Go/exporter/ar_event",
					"version": "v2.6.0"
				}
			}
		},
		"Subject": "主题",
		"Link": {
			"TraceID": "2fbf46e1c0ee31989ca7b2c90d75e861",
			"SpanID": "e910cd0d7d63c1d5"
		},
		"Data": {
			"data": "data",
			"key": "value"
		}
	},
	{
		"EventID": "01GPJ1Q7WVPVFM6CC6BA33GFHM",
		"EventType": "NewExporter/add",
		"Time": "2023-01-12T11:52:31.8993027+08:00",
		"Level": "INFO",
		"Attributes": {},
		"Resource": {
			"host": {
				"arch": "x86_64",
				"ip": "10.4.34.50",
				"name": "DevDeskV6-029"
			},
			"os": {
				"description": "Microsoft Windows 10 Enterprise",
				"type": "windows",
				"version": "10.0.18363.959 Build 18363.959"
			},
			"service": {
				"instance": "",
				"name": "YourServiceName",
				"version": "1.0.0"
			},
			"telemetry": {
				"sdk": {
					"language": "go",
					"name": "TelemetrySDK-Go/exporter/ar_event",
					"version": "v2.6.0"
				}
			}
		},
		"Subject": "",
		"Data": {
			"Name": "name",
			"Age": 31
		}
	}
]
```
