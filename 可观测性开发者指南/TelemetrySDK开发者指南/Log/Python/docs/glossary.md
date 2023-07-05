# [术语表](https://confluence.aishu.cn/pages/viewpage.action?pageId=160886225)
## Log 数据格式
|      **字段**      | **类型** | **是否必填** |  **主动设置**  |  **自动生成**  | **说明**                                                                 |
|:----------------:|:------:|:--------:|:----------:|:----------:|:-----------------------------------------------------------------------|
|  **Timestamp**   | string |    ✓     |            |     ✓      | 事件发生的时间：格式为RFC3339Nano。精确到纳秒。示例："2022-12-29T15:28:13.587199587+08:00"。 |
|     **Link**     | object |    ✕     |     ✓      |            | 关联的信息：包含TraceId、SpanId的信息                                              |
| **SeverityText** | string |    ✓     |     ✓      |            | 日志级别：从低到高对应为Trace、Debug、Info、Warn、Error、Fatal                          |
|     **Body**     |  map   |    ✕     |     ✓      |            | 一次日志记录行为 。                                                             |
|   **Resource**   |  map   |    ✓     | ✓(部分可主动设置) | ✓(部分可自动生成) | 系统进程属性，与Attributes 区别在于attributes 更倾向于业务信息，resource 关注系统或进程上下文。        |
|  **Attributes**  |  map   |    ✕     |            |     ✓      | 一次内部调用的业务属性                                                            |

## Link字段信息定义：

|   **字段**    | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**  | **示例**                             |
|:-----------:|:------:|:--------:|:--------:|:--------:|:--------|:-----------------------------------|
| **TraceId** | string |    ✓     |          |    ✓     | TraceId | "add9aea31219729e2100cbe05fd24992" |
| **SpanId**  | string |    ✓     |          |    ✓     | SpanId  | "a416500c3fc83087"                 |

## Resource记录信息定义：

|           **字段**           | **类型** | **是否必填** | **主动设置** | **自动生成** | **说明**    | **示例**                               |
|:--------------------------:|:------:|:--------:|:--------:|:--------:|:----------|:-------------------------------------|
|        **host.ip**         | string |    ✓     |          |    ✓     | 主机IP地址    | 127.0.0.1                            |
|       **host.arch**        | string |    ✓     |          |    ✓     | 主机CPU架构   | amd64、arm64                          |
|       **host.name**        | string |    ✓     |          |    ✓     | 主机名称      | Desk-028                             |
|        **os.type**         | string |    ✓     |          |    ✓     | 操作系统类型    | windows、linux                        |
|       **os.version**       | string |    ✓     |          |    ✓     | 操作系统版本    | CentOS 7.7                           |
|     **os.description**     | string |    ✓     |          |    ✓     | 操作系统详细信息  | Microsoft Windows 10 Enterprise      |
|  **service.instance.id**   | string |    ✕     |    ✓     |          | 服务实例的ID   | 627cc493-f310-47de-96bd-71410b7dec09 |
|      **service.name**      | string |    ✓     |    ✓     |          | 服务名称      | ShoppingMart                         |
|    **service.version**     | string |    ✓     |    ✓     |          | 服务的版本号    | 5.3.0                                |
| **telemetry.sdk.language** | string |    ✓     |          |    ✓     | SDK的开发语言  | python                               |
|   **telemetry.sdk.name**   | string |    ✓     |          |    ✓     | SDK名称     | TelemetrySDK-Python/exporter/ar_log  |
| **telemetry.sdk.version**  | string |    ✓     |          |    ✓     | SDK版本号    | v2.4.1                               |
|   **k8s.namespace.name**   | string |    ✓     |          |    ✓     | k8s 命名空间  | anyshare                             |
|      **k8s.pod.name**      | string |    ✓     |          |    ✓     | k8s pod名称 | efast-123456789-12345                |
|     **k8s.node.name**      | string |    ✓     |          |    ✓     | k8s 节点名称  | node01                               |

## Body结构定义：

| **序号** | **key 字段名**         | **value 类型** | **是否必须** | **描述**                                                                                                                                                                                                                               |
|--------|---------------------|--------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Body.1 | Type                | string       | 否        | 序号Body.1中"Type"分两种情况（Type类型需要统一管理）：1. 序号1中"Type"默认为 Message，此时 Body.2 为字符串，在 json 序列化时会被序列化为 json 安全编码字符串；2. 序号Body.1中"Type"存在时（Type类型需要统一管理）, Body.2 中Message 必须不能存在，且Body.2 中的 key 必须为 Body.1 中 Type 的 value 值，主要用于应用根据自己的需要扩展数据类型 |
| Body.2 | 上述 Type 对应的 value 值 | 任意类型         | 否        |                                                                                                                                                                                                                                      |

## Attributes结构定义：

| **字段名**       | **类型** | **是否必须** | **描述**                                     |
|---------------|--------|----------|--------------------------------------------|
| Type对应的value值 | map    | 是        | 其他字段提供给使用者定义属性信息，该字段名为Type字段值，Type类型需要统一管理 |

# Log数据样例
```json
[
  {
    "Link": {
      "TraceId": "00000000000000000000000000000000",
      "SpanId": "0000000000000000"
    },
    "Timestamp": "2023-07-04T14:21:31.322274+08:00",
    "SeverityText": "Warn",
    "Body": {
      "Message": "this is a info message"
    },
    "Attributes": {
      "Type": "pair",
      "pair": {
        "param_x": 1,
        "param_y": 2
      }
    },
    "Resource": {
      "host": {
        "ip": "10.4.34.50",
        "name": "DevDeskV6-029",
        "arch": "AMD64"
      },
      "os": {
        "type": "Windows",
        "version": "10.0.18362",
        "description": "Windows10"
      },
      "telemetry": {
        "sdk": {
          "name": "TelemetrySDK-Python/exporter/ar_log",
          "version": "2.4.2",
          "language": "python"
        }
      },
      "service": {
        "name": "YourServiceName",
        "version": "2.4.2",
        "instance": {
          "id": "983d7e1d5e8cda64"
        }
      }
    }
  },
  {
    "Link": {
      "TraceId": "00000000000000000000000000000000",
      "SpanId": "0000000000000000"
    },
    "Timestamp": "2023-07-04T14:21:31.331358+08:00",
    "SeverityText": "Warn",
    "Body": {
      "Message": "this is a info message"
    },
    "Attributes": {
      "Type": "pair",
      "pair": {
        "param_x": 6,
        "param_y": 3
      }
    },
    "Resource": {
      "host": {
        "ip": "10.4.34.50",
        "name": "DevDeskV6-029",
        "arch": "AMD64"
      },
      "os": {
        "type": "Windows",
        "version": "10.0.18362",
        "description": "Windows10"
      },
      "telemetry": {
        "sdk": {
          "name": "TelemetrySDK-Python/exporter/ar_log",
          "version": "2.4.2",
          "language": "python"
        }
      },
      "service": {
        "name": "YourServiceName",
        "version": "2.4.2",
        "instance": {
          "id": "983d7e1d5e8cda64"
        }
      }
    }
  }
]
```