[TOC]
# RocketMQ
## 第2章 RocketMQ初探门径
### 2-1 本章导航
* RocketMQ -整体介绍
* RocketMQ - 核心概念模型
* RocketMQ - 源码包下载与结构说明
* RocketMQ - 环境搭建
* RockeMQ - 控制台部署与使用

### 2-2 RocketMQ整体认知
* RocketMQ 是一款分布式、队列模型的消息中间件
* 从4.3.x 版本 开始支持分布式事务
* 支持集群模型、负载均衡、水平扩展能力
* 亿级别的消息堆积能力
* 采用零拷贝的原理、顺序写盘、随机读
* 丰富的API使用
* 代码优秀，底层通信框架采用Netty NIO框架
* NameServer 代替 Zookeeper
* 强调集群无单点，可扩展，任意一点高可用，水平可扩展
* 消息失败重试机制、消息可查询

### 2-3 RocketMQ 概念模型
* Producer: 消息生产者，负责产生消息，一般由业务系统负责产生消息
* 

