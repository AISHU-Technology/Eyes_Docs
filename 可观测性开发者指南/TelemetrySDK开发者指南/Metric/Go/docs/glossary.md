# [术语表](https://confluence.aishu.cn/pages/viewpage.action?pageId=160886235)

## ResourceMetric数据格式
|      **字段**      | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**              |
|:----------------:|:------:|:--------:|:--------:|:--------:|:--------------------|
|   **Resource**   |  map   |    ✓     |          |    ✓     | 资源信息，用于区分数据来源。      |
| **ScopeMetrics** | array  |    ✓     |          |    ✓     | Scope和Metrics信息的合集。 |

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
|   **telemetry.sdk.name**   | string |    ✓     |          |    ✓     | SDK名称     | TelemetrySDK-Go/exporters/ar_metric  |
| **telemetry.sdk.version**  | string |    ✓     |          |    ✓     | SDK版本号    | v2.2.0                               |
|  **k8s.namespace.name**	   | string |    ✓     |          |    ✓     | k8s命名空间   | anyshare                             |
|     **k8s.pod.name**	      | string |    ✓     |          |    ✓     | k8s pod名称 | efast-123456789-12345                |
|     **k8s.node.name**	     | string |    ✓     |          |    ✓     | k8s节点名称   | node01                               |

## ScopeMetrics数据格式
|   **字段**    | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**          |
|:-----------:|:------:|:--------:|:--------:|:--------:|:----------------|
|  **Scope**  | object |    ✓     |          |    ✓     | Telemetry工具库信息。 |
| **Metrics** | array  |    ✓     |          |    ✓     | Metric数据集合。     |

## Scope数据格式
|    **字段**     | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**            |
|:-------------:|:------:|:--------:|:--------:|:--------:|:------------------|
|   **Name**    | string |    ✓     |          |    ✓     | Telemetry工具库名称。   |
|  **Version**  | string |    ✓     |          |    ✓     | Telemetry工具库版本。   |
| **SchemaURL** | string |    ✓     |          |    ✓     | Telemetry工具库仓库地址。 |

## Metric数据格式
|     **字段**      | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**    |
|:---------------:|:------:|:--------:|:--------:|:--------:|:----------|
|    **Name**     | string |    ✓     |    ✓     |          | Metric名称。 |
| **Description** | string |    ✕     |    ✓     |          | Metric描述。 |
|    **Unit**     | string |    ✓     |    ✓     |    ✓     | Metric单位。 |
|    **Data**     | object |    ✓     |          |    ✓     | Metric数据。 |

## Gauge数据格式
|     **字段**     | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**  |
|:--------------:|:------:|:--------:|:--------:|:--------:|:--------|
| **DataPoints** | array  |    ✓     |          |    ✓     | 采样数据集合。 |

## Sum数据格式
|     **字段**      | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**  |
|:---------------:|:------:|:--------:|:--------:|:--------:|:--------|
| **DataPoints**  | array  |    ✓     |          |    ✓     | 采样数据集合。 |
| **Temporality** | string |    ✓     |          |    ✓     | 数据采集方式。 |
| **IsMonotonic** |  bool  |    ✓     |    ✓     |          | 是否递增。   |

## Histogram数据格式
|     **字段**      | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**  |
|:---------------:|:------:|:--------:|:--------:|:--------:|:--------|
| **DataPoints**  | array  |    ✓     |          |    ✓     | 采样数据集合。 |
| **Temporality** | string |    ✓     |          |    ✓     | 数据采集方式。 |

## DataPoint数据格式
|     **字段**     |  **类型**   | **是否必填** | **主动设置** | **自动生成** | **说明**     |
|:--------------:|:---------:|:--------:|:--------:|:--------:|:-----------|
| **Attributes** |    map    |    ✓     |    ✓     |          | 标识时间段的键值对。 |
| **StartTime**  |  string   |    ✕     |          |    ✓     | 时间段的开始时刻。  |
|    **Time**    |  string   |    ✕     |          |    ✓     | 时间段的结束时刻。  |
|   **Value**    | int/float |    ✓     |    ✓     |          | 该记录点的值。    |

## HistogramDataPoint数据格式
|      **字段**      | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**     |
|:----------------:|:------:|:--------:|:--------:|:--------:|:-----------|
|  **Attributes**  |  map   |    ✓     |    ✓     |          | 标识时间段的键值对。 |
|  **StartTime**   | string |    ✓     |          |    ✓     | 时间段的开始时刻。  |
|     **Time**     | string |    ✓     |          |    ✓     | 时间段的结束时刻。  |
|    **Count**     |  int   |    ✓     |          |    ✓     | 该记录点的记录次数。 |
|     **Min**      | float  |    ✕     |          |    ✓     | 时间段的最小值。   |
|     **Max**      | float  |    ✕     |          |    ✓     | 时间段的最大值。   |
|     **Sum**      | float  |    ✓     |          |    ✓     | 时间段的总和。    |
|    **Bounds**    | array  |    ✓     |    ✓     |    ✓     | 直方图边界值。    |
| **BucketCounts** | array  |    ✓     |          |    ✓     | 边界值桶内计数。   |

# Metric数据样例
```json
[
	{
		"Resource": [
			{
				"Key": "host.arch",
				"Value": {
					"Type": "STRING",
					"Value": "x86_64"
				}
			},
			{
				"Key": "host.ip",
				"Value": {
					"Type": "STRING",
					"Value": "10.4.34.50"
				}
			},
			{
				"Key": "host.name",
				"Value": {
					"Type": "STRING",
					"Value": "DevDeskV6-029"
				}
			},
			{
				"Key": "os.description",
				"Value": {
					"Type": "STRING",
					"Value": "Microsoft Windows 10 Enterprise"
				}
			},
			{
				"Key": "os.type",
				"Value": {
					"Type": "STRING",
					"Value": "windows"
				}
			},
			{
				"Key": "os.version",
				"Value": {
					"Type": "STRING",
					"Value": "10.0.18363.959 Build 18363.959"
				}
			},
			{
				"Key": "service.instance.id",
				"Value": {
					"Type": "STRING",
					"Value": ""
				}
			},
			{
				"Key": "service.name",
				"Value": {
					"Type": "STRING",
					"Value": "YourServiceName"
				}
			},
			{
				"Key": "service.version",
				"Value": {
					"Type": "STRING",
					"Value": "1.0.0"
				}
			},
			{
				"Key": "telemetry.sdk.language",
				"Value": {
					"Type": "STRING",
					"Value": "go"
				}
			},
			{
				"Key": "telemetry.sdk.name",
				"Value": {
					"Type": "STRING",
					"Value": "TelemetrySDK-Go/exporter/ar_metric"
				}
			},
			{
				"Key": "telemetry.sdk.version",
				"Value": {
					"Type": "STRING",
					"Value": "v2.5.0"
				}
			}
		],
		"ScopeMetrics": [
			{
				"Scope": {
					"Name": "TelemetrySDK-Go/exporter/ar_metric",
					"Version": "v2.5.0",
					"SchemaURL": "https://devops.aishu.cn/AISHUDevOps/ONE-Architecture/_git/TelemetrySDK-Go?path=/exporter/ar_metric"
				},
				"Metrics": [
					{
						"Name": "gauge：用户数峰值",
						"Description": "a simple gauge",
						"Unit": "1",
						"Gauge": {
							"DataPoints": [
								{
									"Attributes": [
										{
											"Key": "用户信息",
											"Value": {
												"Type": "STRING",
												"Value": "在线用户数"
											}
										}
									],
									"Time": "2023-01-12T11:52:32.1473631+08:00",
									"Int": 12
								}
							]
						}
					},
					{
						"Name": "histogram：当前用户数",
						"Description": "a histogram with custom buckets and name",
						"Unit": "1",
						"Histogram": {
							"DataPoints": [
								{
									"Attributes": [
										{
											"Key": "用户信息",
											"Value": {
												"Type": "STRINGARRAY",
												"Value": [
													"在线用户数"
												]
											}
										}
									],
									"StartTime": "2023-01-12T11:52:32.0246543+08:00",
									"Time": "2023-01-12T11:52:32.1473631+08:00",
									"Count": 4,
									"Bounds": [
										0,
										5,
										10,
										25,
										50,
										75,
										100,
										250,
										500,
										750,
										1000,
										2500,
										5000,
										7500,
										10000
									],
									"BucketCounts": [
										0,
										0,
										0,
										0,
										0,
										1,
										0,
										1,
										1,
										1,
										0,
										0,
										0,
										0,
										0,
										0
									],
									"Min": 64,
									"Max": 600,
									"Sum": 1140
								}
							],
							"Temporality": "CumulativeTemporality"
						}
					},
					{
						"Name": "sum：用户数日活",
						"Description": "a simple counter",
						"Unit": "ms",
						"Sum": {
							"DataPoints": [
								{
									"Attributes": [
										{
											"Key": "用户信息",
											"Value": {
												"Type": "STRING",
												"Value": "登录DAU"
											}
										}
									],
									"StartTime": "2023-01-12T11:52:32.0246543+08:00",
									"Time": "2023-01-12T11:52:32.1473631+08:00",
									"Float": 968
								}
							],
							"Temporality": "CumulativeTemporality",
							"IsMonotonic": true
						}
					}
				]
			}
		]
	}
]
```
