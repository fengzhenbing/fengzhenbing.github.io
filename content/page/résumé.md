---
title: 关于
description: 我是一个软件工程师，热爱coding，热爱生活.
date: '2019-02-28'
aliases:
  - about-us
  - contact
license: CC BY-NC-ND
lastmod: '2020-10-09'
draft: true
menu:
    main: 
        weight: -90
        pre: résumé
---

## 个人介绍

**冯振兵/男/1989    Apache ShenYu PPMC** 

- `教育背景`：本科/中国矿业大学/计算机科学与技术专业/13年毕业/英语6级
- `电话`: 15380837691      `邮箱`： fengzhenbing@apache.org      `微信号`：fengzhenbing98
- `Github`：https://github.com/fengzhenbing/        `博客`：https://fengzhenbing.github.io/
- `期望职位`：Java web架构师       `工作年限`：8年       `定居城市`：南京

## 专业技能

* 语言：熟练掌握Java基础：多线程，集合，Nio等基础框架; 理解Jvm原理，包括内存模型，GC机制等；

* 数据库：熟练掌握Mysql数据库原理，掌握分库分表框架Shardingsphere及相关原理；

* Spring框架：掌握Spring Cloud微服务架构，对业务/流量网关有开发经验；

* 分布式：熟悉分布式理论Base，CAP等，了解分布式共识算法；熟悉分布式事务原理：2PC，TCC事务，AT事务等；

* 中间件：熟练使用Redis，熟悉相关原理及应用场景；熟悉消息中间件原理及应用；

* 前端：对前端框架react.js，antd.js，taro.js有使用经验；尝试过dart编写flutter应用；

## 开源经历

深入参与过Apache孵化项目 [ShenYu](https://shenyu.apache.org/) `API网关`的开发，负责了shenyu-admin模块及部分插件的开发维护，官网文档的维护。通过该项目扩展了自己对设计模式，微内核插件式架构的认识，对负载均衡，熔断限流，可观测性等高可用技术有了更深刻的理解。

## 工作经历 

#### 南京乾中码网络科技有限公司-技术合伙人（ 2020年6月 ~ 至今）

##### 爱彼易建材服务平台

* 介绍：多商家入驻的建材行业垂直领域服务平台，提供建材展示，营销，接单等功能。提供小程序端和服务管理web系统。

* 技术：Spring Cloud/Spring Sercurity+Jwt/Nacos/Mysql/Redis/Shardingsphere/Nginx/Elk等
* 负责工作：
  * 负责核心技术选型：前端antd pro搭建商家管理页面, taro.js构建小程序，nginx做前端代理，后端Spring Cloud Gateway搭建网关，Nacos作为服务注册中心和配置中心，网关后端提供项目系统微服务，订单微服务，用户微服务，服务之间通过openfeign进行rpc调用。
  * 核心代码编写：认证鉴权，Aop处理非业务功能，Elk搭建日志平台等。

#### 江苏墨客网络科技有限公司-技术负责人（ 2017年6月 ~ 2020年5月 ）

##### 墨客低代码工作流平台

* 介绍：一套`代码生成`+`表单生成`+`自定义流程`低代码工作流平台。

* 技术关键词：

  * 后端Spring cloud/Nacos/Activiti/Mysql/Redis/Rabbitmq   前端 reactjs/antd ui/antd pro/react native移动端/flutter移动端/taro小程序
* 负责工作：
  * 配置引擎：提供配置页面，允许用户自定义数据库表结构，自定义业务字段，自定义表单组件，后端使用mysql元数据做配置存储，扩展activiti工作流配置功能，将表单与流程绑定。
  * 代码生成引擎：使用配置生成各端的表单组件代码。
  * 流程业务执行引擎：使用redis缓存各类业务配置，集成Rabbitmq处理各类异步业务消息。使用shardingsphere-jdbc做读写分离。

##### 展厅控制云平台

* 介绍：对展厅内部硬件设备，软件进行集中管理，提供多租户，可以多级展厅共用，分享资源。

* 技术关键词：  Spring Cloud/Nacos/Netty4/Mysql/Redis/JWT/RabbitMq

* 负责工作：

  * 通讯模块：使用Netty提供网络通讯服务，自定义控制协议控制硬件执行。维护与客户端的连接池，并采用心跳机制保持连接等。

  * 任务调度： 命令编排成组任务，基于quartz实现分布式任务调度模块，定时执行任务组。使用到了消息队列缓存任务，削峰填谷。

##### 南京聚世能erp解决方案

* 技术关键词： Spring Cloud/Nacos/Mysql/Redis/Jwt/Activiti/Shardingsphere/Rabbitmq/Elk
* 负责整体技术把控和研发。

### 北京数字政通科技股份有限公司-java研发工程师（ 2015年6月 ~ 2017年5月 ）

##### 社会管理平台系列产品

* 技术关键词： tomcat/oracle/ssm/redis/kettle
* 负责`社会网格化管理采集子系统`：对市级别的人口，房屋等数据进行清洗采集入库。负责`社会网格化综合治理子系统`的业务流程编码。

### 南京富士通南大软件技术有限公司-java研发工程师（ 2013年7月 ~ 2015年5月 ）

##### 集团文档共享系统

* 技术关键词： Tomcat/Interstage Symforware Struts2  Solr Jmeter Ajax Jquery Css 

* 文章发表，检索，分类，统计，履历等等功能。通过solr服务，完成智能检索，推荐功能。

## 自我评价

 通过业余时间不断学习源码到加入一个开源团队贡献源码，技术视野得到了大大扩展，与一群技术牛人交流成长，技术之路越走越宽。

* 积极乐观，适应能力强。
* 具有良好的沟通和团队协作能力。
* 技术视野好，学习能力强，喜欢钻研，热衷编码。
