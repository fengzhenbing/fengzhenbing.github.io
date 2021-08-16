---
title: "Envoy"
date: 2021-07-20
description: C++ 开发的高性能代理
tags:
    - 微服务
    - 云原生
    - K8S
    - 网关
categories:
    - 云原生
    - 微服务
    - K8S
    - 网关
---

## Envoy概述

Envoy 是以 C++ 开发的高性能代理;

其内置服务发现、负载均衡、TLS终止、HTTP/2、GRPC代理、熔断器、健康检查，基于百分比流量拆分的灰度发布、故障注入等功能

![1.png](http://dockone.io/uploads/article/20190722/e56882465fb16ac21248567c621b90f9.png)

- Downstream：下游主机，指连接到Envoy的主机，这些主机用来发送请求并接受响应。
- Upstream：上游主机，指接收来自Envoy连接和请求的主机，并返回响应。
- Listener：服务或程序的监听器， Envoy暴露一个或多个监听器监听下游主机的请求，当监听到请求时，通过Filter Chain把对请求的处理全部抽象为Filter， 例如ReadFilter、WriteFilter、HttpFilter等。
- Cluster：服务提供集群，指Envoy连接的一组逻辑相同的上游主机。Envoy通过服务发现功能来发现集群内的成员，通过负载均衡功能将流量路由到集群的各个成员。
- xDS：xDS中的x是一个代词，类似云计算里的XaaS可以指代IaaS、PaaS、SaaS等。DS为Discovery Service，即发现服务的意思。xDS包括CDS（cluster discovery service）、RDS（route discovery service）、EDS（endpoint discovery service）、ADS（aggregated discovery service），其中ADS称为聚合的发现服务，是对CDS、RDS、LDS、EDS服务的统一封装，解决CDS、RDS、LDS、EDS信息更新顺序依赖的问题，从而保证以一定的顺序同步各类配置信息。以上Endpoint、Cluster、Route的概念介绍如下：
  - Endpoint：一个具体的“应用实例”，类似于Kubernetes中的一个Pod；
  - Cluster：可以理解“应用集群”，对应提供相同服务的一个或多个Endpoint， 类似Kubernetes中Service概念，即一个Service提供多个相同服务的Pod；
  - Route：当我们做金丝雀发布部署时，同一个服务会有多个版本，这时需要Route规则规定请求如何路由到其中的某个版本上。





http://www.dockone.io/article/9116