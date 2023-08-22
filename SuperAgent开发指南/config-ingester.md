# system-metric ingester (系统指标采集器)

系统配置示例：
```
app:
  mode: release
  scrapecfg_interval: 30s
log:
  filepath: log/ingester.log
  level: info
  max_size: 10
  max_age: 10
  max_backups: 100
  compress: false
kafka:
  filepath: log/sarama.log
  max_size: 10
  max_age: 10
  max_backups: 100
  compress: false

```

|字段|类型|是否必填|描述|
|--|--|--|--|
|app.mode|string| 是|应用运行模式, release:生产模式；debug:调试模式|
|app.scrapecfg_interval|string|是|拉取采集配置频率|
|log.filepath|string|是|采集器日志记录文件|
|log.level|string|是|日志记录级别，info:信息；warning：警告；error：错误|
|log.max_size|int|是|每个日志文件最大空间(单位：MB)|
|log.max_age|int|是|日志文件最大保留时间（单位：天）|
|log.max_backups|int|是|日志文件最多保留多少备份|
|log.compress|bool|是|日志是否压缩|
|kafka.filepath|string|是|kafka日志文件|
|kafka.max_size|string|是|kafka日志文件最大空间（单位：MB）|
|kafka.max_age|int|是|日志文件最大保留时间（单位：天）|
|kafka.max_backups|int|是|日志文件最多保留多少备份|
|kafka.compress|bool|是|日志是否压缩|