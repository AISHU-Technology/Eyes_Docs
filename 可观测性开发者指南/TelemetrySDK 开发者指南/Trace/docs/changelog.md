# 版本变化

## 2.2.0

### 增加

- 提供Golang版Trace Exporter，支持Trace数据发送至AnyRobot
- 增加生产发送链路数据开关

### 变更

- Go版本升级为1.18
- 调整项目目录结构

### 修复

- 去除未使用的引入
- 修复golang ci-lint错误
- 遵循SonarCube排除异味
- 修复https缺少证书
- 修复错误的埋点方式
- 补全开发指南

## 2.1.0

### 增加

### 变更

- Go版本升级为1.16
- 项目名称从Akashic_TelemetrySDK-Go改为TelemetrySDK-Go
- 仓库空间从AnyRobot迁移到ONE-Architecture

### 修复

## 2.0.0

### 增加

- 增加详细的文档描述
- 增加log支持可选参数

### 变更

- 调整日志的4个级别
- 优化代码
- 仓库地址从GitLab迁移到DevOps
- 重命名Go文件

### 修复

- 日志并发写入改为同步写入

## 1.0.1

### 增加

- 增加日志添加json类型接口

### 变更

### 修复

## 1.0.0

### 增加

- 增加日志器封装
- 增加benchmark测试用例
- encoder添加缓存避免分段输出导致数据混淆
- 增加文档说明
- 增加日志导出错误时panic

### 变更

- Go版本升级为1.15
- 代码包结构调整
- 变更日志输出地址

### 修复

- 重命名record's message避免冲突
- 删除未使用的引入
- 修复关闭runtime线程与runtime运行线程同步可能导致数据丢失的问题