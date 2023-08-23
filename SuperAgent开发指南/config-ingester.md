# 通用系统配置

系统配置示例：
```
app:
  job_interval: 10
  deploy_type: host
  data_file_dir: /var/lib/SuperAgent
log:
  log_filepath: /opt/executor/log/executor.log
  log_level: debug
  develop_mode: true
  max_age: 100
  max_backups: 20
  max_size: 100

```

|字段|类型|是否必填|描述|
|--|--|--|--|
|app.job_interval|int| 是|执行器拉取任务执行频率（单位：秒）|
|app.deploy_type|string|是|部署方式， host:主机；kubernetes：Kubernetes|
|app.data_file_dir|string|是|数据目录|
|log.log_filepath|string|是|采集器日志记录文件|
|log.log_level|string|是|日志记录级别，info:信息；warning：警告；error：错误|
|log.max_size|int|是|每个日志文件最大空间(单位：MB)|
|log.max_age|int|是|日志文件最大保留时间（单位：天）|
|log.max_backups|int|是|日志文件最多保留多少备份|

