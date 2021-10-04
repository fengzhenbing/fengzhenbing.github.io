---
title: "pulsar"
date: 2021-02-23
description: pulsar
tags:
    - 消息
categories:
    - 消息
---

## 相关概念

- 计算存储分离
- ![image-20210406220312072](https://gitee.com/fengzhenbing/picgo/raw/master/image-20210406220312072.png)

## 安装测试

```shell
# 下载
wget https://mirrors.bfsu.edu.cn/apache/pulsar/pulsar-2.7.1/apache-pulsar-2.7.1-bin.tar.gz
tar xvfz apache-pulsar-2.7.1-bin.tar.gz
cd apache-pulsar-2.7.1

# 单机启动
bin/pulsar standalone

# 消费消息
bin/pulsar-client consume my-topic -s "first-subscription"

# 生产消息
bin/pulsar-client produce my-topic --messages "hello-pulsar"
```



## 相关资料

[官网快速启动](http://pulsar.apache.org/docs/zh-CN/standalone/)

