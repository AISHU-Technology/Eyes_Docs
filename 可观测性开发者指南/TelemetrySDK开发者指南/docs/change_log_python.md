# 版本变化

## 2.4.2

### 修复

- 去除日志关联链路数据中产生独立的日志上报AnyRobot的链路

## 2.4.1

### 增加

- Python版 Exporter，新增支持本地文件输出和控制台输出方式
- 新增日志关联链路数据埋点示例

### 变更

- 完善Eyes_Docs文档描述，提供性能数据参考

## 2.4.0

### 增加

- 提供Python版Trace Exporter，支持Trace数据发送至AnyRobot
- 提供跨服务链路埋点示例

## 2.3.0

### 增加

- 提供Python版日志上报能力，Log Provider支持同步模式、异步模式，支持Log数据发送至AnyRobot

## 2.2.0

### 增加

- 提供Python版Metric Exporter，支持Metric数据发送至AnyRobot

### 变更

- 调整项目目录结构

### 修复

- 去除未使用的引入
- 修复pylint错误
- 遵循SonarCube排除异味
- 补全开发指南

## 2.1.0

### 增加

### 变更

- 项目名称从Akashic_TelemetrySDK-Python改为TelemetrySDK-Python
- 仓库空间从AnyRobot迁移到ONE-Architecture

### 修复

## 2.0.0

### 增加

### 变更

- 优化代码，更换tlogger内部实现
- 仓库地址从GitLab迁移到DevOps

### 修复

- 日志并发写入改为同步写入

## 1.0.0

### 增加

- 增加日志器封装
- 增加benchmark测试用例
- 增加文档说明

### 变更

- Python版本升级为3.7
- 变更日志输出地址

### 修复
