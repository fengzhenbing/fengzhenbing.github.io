---
title: "rabbitMQ"
date: 2021-02-19T10:16:25+06:00
description: rabbitMQ
tags:
    - 消息
categories:
    - 消息
---


### Rabbitmq相关概念

   一代mq，erlang开发， 改进activemq

- Publisher 消息生产者, 返送消息时指定exchange 和routing key, 即可以将消息路由到匹配的queue中
- Routing key
- Binding   通过routing key 将queue和exchange绑定
- Exchange  工具人。。交易所。。代理
  - FanoutExchange: 将消息分发到所有的绑定队列，无routingkey的概念，发送时不指定routing key
  - HeadersExchange ：通过添加属性key-value匹配
  - DirectExchange: 按照routingkey分发到指定队列
  - TopicExchange:多关键字匹配 正则
- Consumer 

![image-20210405203826959](/images/blog/image-20210405203826959.png)

**1. Exchange概念**

- Exchange：接收消息，并根据路由键转发消息所绑定的队列。

 ![img](https://gitee.com/fengzhenbing/picgo/raw/master/1577453-20200519174721973-114601627.png) 

蓝色框：客户端发送消息至交换机，通过路由键路由至指定的队列。
黄色框：交换机和队列通过路由键有一个绑定的关系。
绿色框：消费端通过监听队列来接收消息。

### Docker方式安装运行

```shell
docker pull rabbitmq:management
docker run -itd --name rabbitmq-test -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 rabbitmq:management
docker exec -it rabbitmq-test /bin/bash
```





高可用

#### 镜像集群模式（高可用性）


这种模式，才是所谓的 RabbitMQ 的高可用模式。跟普通集群模式不一样的是，在镜像集群模式下，你创建的 queue，无论元数据还是 queue 里的消息都会**存在于多个实例上**，就是说，每个 RabbitMQ 节点都有这个 queue 的一个**完整镜像**，包含 queue 的全部数据的意思。然后每次你写消息到 queue 的时候，都会自动把**消息同步**到多个实例的 queue 上。

![mq-8](https://gitee.com/fengzhenbing/picgo/raw/master/mq-8.png)

那么**如何开启这个镜像集群模式**呢？其实很简单，RabbitMQ 有很好的管理控制台，就是在后台新增一个策略，这个策略是**镜像集群模式的策略**，指定的时候是可以要求数据同步到所有节点的，也可以要求同步到指定数量的节点，再次创建 queue 的时候，应用这个策略，就会自动将数据同步到其他的节点上去了。

这样的话，好处在于，你任何一个机器宕机了，没事儿，其它机器（节点）还包含了这个 queue 的完整数据，别的 consumer 都可以到其它节点上去消费数据。坏处在于，第一，这个性能开销也太大了吧，消息需要同步到所有机器上，导致网络带宽压力和消耗很重！第二，这么玩儿，不是分布式的，就**没有扩展性可言**了，如果某个 queue 负载很重，你加机器，新增的机器也包含了这个 queue 的所有数据，并**没有办法线性扩展**你的 queue。你想，如果这个 queue 的数据量很大，大到这个机器上的容量无法容纳了，此时该怎么办呢？
