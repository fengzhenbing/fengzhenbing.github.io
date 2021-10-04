---
title: "rocketMQ"
date: 2021-02-20T09:16:25+06:00
description: rocketMQ
tags:
    - 消息
categories:
    - 消息
---

## 相关概念

二代mq, 纯java开发，和kafka无本质区别

## 安装测试

```shell
# 下载 4.8.0
wget https://downloads.apache.org/rocketmq/4.8.0/rocketmq-all-4.8.0-bin-release.zip
unzip rocketmq-all-4.8.0-bin-release.zip

#运行命名服务， 替代kafka的zk
nohup sh bin/mqnamesrv &
#查看日志
tail -f ~/logs/rocketmqlogs/namesrv.log

#运行broker
nohup sh bin/mqbroker -n localhost:9876 &
#查看日志
tail -f ~/logs/rocketmqlogs/broker.log 

# 发送消息
export NAMESRV_ADDR=localhost:9876
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer

#消费消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
```



## 相关资料

[官网快速开始](http://rocketmq.apache.org/docs/quick-start/)

[中文文档](https://github.com/apache/rocketmq/tree/master/docs/cn)

