[TOC]
# RocketMQ核心技术精讲与高并发抗压实战
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
* `Producer`: 消息生产者，负责产生消息，一般由业务系统负责产生消息
* `Consumer`: 消息消费者，负责消费消息，一般是后台系统负责异步消费。
* `Push Consumer`:`Consumer`的一种，需要向`Consumer`对象注册监听。
* `Pull Consumer`: `Consumer`的一种，需要主动请求`Broker`拉取消息。
* `Producer Group`: 生产者集合，一般用于发送一类消息
* `Consumer Group`: 消费者集合，一般用于接受一类消息进行消费
* Broker: MQ消息服务（中转角色，用于消息存储与生产消费转发）

### 2-4 RocketMQ - 源码包编译与结构说明
官网地址：http://rocketmq.apache.org/docs/quick-start/

### 2-5 RocketMQ- 源码包编译与结构说明
![-w904](http://it-learn.oss-cn-beijing.aliyuncs.com/2019/11/19/15738972363150.jpg)


* `rocketmq-broker`: 主要的业务逻辑，消息收发，主从同步，pagecache
* rocketmq-client: 客户端接口，比如生产者和消费者
* `rocketmq-example`:示例，比如生产者和消费者
* `rocketmq-common`:公用数据结构等等
* `rocketmq-distribution`: 编译模块，编译输出等
* `rocketmq-filter`: 进行Broker过滤的不感兴趣的消息传输，减少带宽压力
* `rocketmq-logappender`、`rocketmq-logging`: 日志相关
* `rocketmq-namesrv`: Namesrv服务，用于服务协调
* `rocketmq-openmessaging`:对外提供服务
* `rocketmq-remoting`: 远程调用接口，封装Netty底层通信
* `rocketmq-srvutil`:提供一些公用的工具方法，比如解析命令行参数
* `rocketmq-store`:消息存储
* `rocketmq-test`、`rocketmq-example`
* `rocketmq-tools`:管理工具，比如有名的mqadmin工具

### 2-6 RocketMQ 环境搭建
1.配置hosts 信息

```shell
vim /etc/hosts

39.107.105.180 rocketmq-nameserver1
39.107.105.180 rocketmq-master1

vim /etc/profile
export NAMESRV_ADDR=127.0.0.1:9876
```
2.在rocketmq-all 项目下执行mvn clean package
3.将 distribution -> target 下面的tar.gz 包上传到 linux服务器上。
![-w578](http://it-learn.oss-cn-beijing.aliyuncs.com/2019/11/19/15739514468137.jpg)

4.解压 tar.gz

```shell
tar -zxvf apache-rocketmq.tar.gz -C ./rocketmq/
```

5.创建存储路径

```shell
# 数据存储位置
mkdir /usr/local/rocketmq/store

# 实际存储
mkdir /usr/local/rocketmq/commitlog

# 索引文件
mkdir /usr/local/rocketmq/consumequeue

# 索引文件
mkdir /usr/local/rocketmq/index

```

4. 修改rocketmq 配置文件

```
cd /usr/local/software/rocketmq/conf/2m-2s-async

vi broker-a.properties
```


```shell
brokerClusterName=rocketmq-cluster
#broker 名字，注意此处不同的配置文件填写的不一样

brokerName=broker-a|broker-b

#0 表示 Master，>0 表示 Slave

brokerId=0

#nameServer 地址，分号分割

namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876

#在发送消息时，自动创建服务器不存在的 topic，默认创建的队列数

defaultTopicQueueNums=4

#是否允许 Broker 自动创建 Topic，建议线下开启，线上关闭

autoCreateTopicEnable=true

#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭

autoCreateSubscriptionGroup=true

#Broker 对外服务的监听端口

listenPort=10911

#删除文件时间点，默认凌晨 4 点

deleteWhen=04

#文件保留时间，默认 48 小时

fileReservedTime=120

#

commitLog

每个文件的大小默认

1G

mapedFileSizeCommitLog=1073741824

#

ConsumeQueue

每个文件默认存

30W

条，根据业务情况调整

mapedFileSizeConsumeQueue=300000

#destroyMapedFileIntervalForcibly=120000

#redeleteHangedFileInterval=120000

#检测物理文件磁盘空间

diskMaxUsedSpaceRatio=88

#存储路径

storePathRootDir=/usr/local/software/rocketmq/store

#commitLog 存储路径

storePathCommitLog=/usr/local/software/rocketmq/store/commitlog

#消费队列存储路径存储路径

storePathConsumeQueue=/usr/local/software/rocketmq/store/consumequeue

#消息索引存储路径

storePathIndex=/usr/local/software/rocketmq/store/index

#checkpoint 文件存储路径

storeCheckpoint=/usr/local/rocketmq/store/checkpoint

#abort 文件存储路径

abortFile=/usr/local/rocketmq/store/abort

#限制的消息大小

maxMessageSize=65536

#flushCommitLogLeastPages=4

#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000

#flushConsumeQueueThoroughInterval=60000

#Broker 的角色

#- ASYNC_MASTER

#- SYNC_MASTER

#- SLAVE

异步复制 Master

同步双写 Master

brokerRole=ASYNC_MASTER

#刷盘方式

#- ASYNC_FLUSH

#- SYNC_FLUSH

异步刷盘

同步刷盘

flushDiskType=ASYNC_FLUSH

#checkTransactionMessageEnable=false

#发消息线程池数量

#sendMessageThreadPoolNums=128

#拉消息线程池数量

#pullMessageThreadPoolNums=128

 Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
brokerClusterName=DefaultCluster
brokerName=broker-a
brokerId=0
deleteWhen=04
fileReservedTime=48
brokerRole=ASYNC_MASTER
```
6.创建日志文件夹

```
[root@iz2zeb7nht7zwkd4k8tdquz conf]# mkdir logs
cd /usr/local/software/rocketmq/conf && sed -i 's#${user.home}#/usr/local/software/rocketmq#g' *.xml
```

7.启动`server`、`broker`

```shell
#cd /usr/local/rocketmq/bin

#nohup sh mqnamesrv &

#nohup sh bin/mqbroker -n localhost:9876 &
```

 常见报错汇总
>rocketmq org.apache.rocketmq.remoting.exception.RemotingConnectException:connect to failed

* https://blog.csdn.net/q258523454/article/details/82716027
* 阿里云安全策略开启端口

### 2-8 RocketMQ 控制台使用介绍
* 参考手记https://www.imooc.com/article/290092
* 记得开放防火墙端口


### 3-1 生产者使用与管控台查询消息
* RocketMQ QuickStart - 生产者使用
* RocketMQ QuickStart - 消费者使用
* RocketMQ - 四种集群环境构建详解
* RocketMQ - 主从模式集群环境构架与测试

RocketMQ - 生产者使用
* 创建生产者对象 `DefaultMQProducer`
* 设置 `NamesrvAddr`
* 启动生产者服务
* 创建消息并发送


### 3-2 消费者使用与Broker重试机制
* 创建消费者对象 DefaultMQPushConsumer
* 设置NamesrvAddr及其消费位置ConsumeFromWhere
* 进行订阅主题subscribe
* 注册监听并消费registerMessageListener

### 3-3 RocketMQ -四种集群环境构建详解
![-w767](http://it-learn.oss-cn-beijing.aliyuncs.com/2019/11/19/15739252889431.jpg)
