---
title: "hazelcast"
date: 2020-12-14T08:06:25+06:00
description: hazelcast安装
tags:
    - 中间件
    - 缓存
categories:
    - 中间件
    - 缓存
---

## 安装
### docker
```shell
docker pull hazelcast/hazelcast

docker run -e HZ_NETWORK_PUBLICADDRESS=192.168.3.14:5701 -p 5701:5701 hazelcast/hazelcast:$HAZELCAST_VERSION
docker run -e HZ_NETWORK_PUBLICADDRESS=192.168.3.14:5702 -p 5702:5701 hazelcast/hazelcast:$HAZELCAST_VERSION

docker run -p 8080:8080 hazelcast/management-center
```

