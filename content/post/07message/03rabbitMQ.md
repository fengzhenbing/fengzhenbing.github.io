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

### Docker方式安装运行

```shell
docker pull rabbitmq:management
docker run -itd --name rabbitmq-test -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 rabbitmq:management
docker exec -it rabbitmq-test /bin/bash
```

