# [术语表](https://confluence.aishu.cn/pages/viewpage.action?pageId=164298403)
## Event数据格式
|         **字段**          | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**                                                                                                                             |
|:-----------------------:|:------:|:--------:|:--------:|:--------:|:-----------------------------------------------------------------------------------------------------------------------------------|
|       **EventID**       | string |    ✓     |          |    ✓     | 事件的唯一标识符：ULID，一个16字节的数组，且至少有一个非0字节。打印时会转成26个16进制的字符。示例：“0000XSNJG0MQJHBF4QX1EFD6Y3”。                                               |
|      **EventType**      | string |    ✓     |    ✓     |          | 事件的类型：用户来定义有哪些事件类型。示例：“com.github.pull.create”或者“com.example.object.delete.v2”或者“alcvc:MeetingEvent:MemberOperate”。                |
|        **Time**         | string |    ✓     |          |    ✓     | 事件的时间戳：创建事件对象时自动读取系统时间。格式为RFC3339。精确到微秒，保留小数点后7位。示例：“2022-10-18T14:12:03.9053361+08:00”。                                           |
|        **Level**        | string |    ✓     |    ✓     |          | 事件的级别：总共3种，“ERROR，WARN，INFO”。默认INFO。                                                                                               |
|      **Resource**       | object |    ✓     |          |    ✓     | 事件的资源信息：包含Attributes。                                                                                                              |
| **Resource.Attributes** |  map   |    ✓     |          |    ✓     | 事件的附加信息：键值对。键和值都必须非空且有语义，数值只有8种类型：STRING、BOOL、INT、FLOAT、STRINGARRAY、BOOLARRAY、INTARRAY、FLOATARRAY。记录Telemetry的信息，此处可以和Trace、Log关联。 |
|       **Subject**       | string |    ✕     |    ✓     |          | 事件的操作对象：操作实例的名称。当eventtype是object.delete，subject是mynewfile.jpg。                                                                    |
|        **Link**         | array  |    ✕     |    ✓     |          | 事件的关联信息：记录事件和链路之间的关联。                                                                                                              |
|        **Data**         | object |    ✓     |    ✓     |          | 事件的内容：大小应该不超过128KB。                                                                                                                |

## Resource记录信息定义：

|           **字段**           | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**   | **示例**                               |
|:--------------------------:|:------:|:--------:|:--------:|:--------:|:---------|:-------------------------------------|
|        **host.ip**	        | string |    ✓     |          |    ✓     | 主机IP地址   | 1.2.3.4                              |
|       **host.arch**	       | string |    ✓     |          |    ✓     | 主机CPU架构  | amd64、arm64                          |
|       **host.name**	       | string |    ✓     |          |    ✓     | 主机名称     | Desk-028                             |
|        **os.type**	        | string |    ✓     |          |    ✓     | 操作系统类型   | windows、linux                        |
|      **os.version**	       | string |    ✓     |          |    ✓     | 操作系统版本   | CentOS 7.7                           |
|     **os.description**     | string |    ✓     |          |    ✓     | 操作系统详细信息 | Microsoft Windows 10 Enterprise      |
|  **service.instance.id**   | string |    ✕     |          |          | 服务实例的ID  | 627cc493-f310-47de-96bd-71410b7dec09 |
|      **service.name**      | string |    ✓     |    ✓     |          | 服务名称     | ShoppingMart                         |
|    **service.version**     | string |    ✓     |    ✓     |          | 服务的版本号   | 5.3.0                                |
| **telemetry.sdk.language** | string |    ✓     |          |    ✓     | SDK的开发语言 | go                                   |
|   **telemetry.sdk.name**   | string |    ✓     |          |    ✓     | SDK名称    | TelemetrySDK-Go/exporters/artrace    |
| **telemetry.sdk.version**  | string |    ✓     |          |    ✓     | SDK版本号   | v2.2.0                               |
