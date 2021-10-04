---
title: "开放平台appKey，appSecret设计"
date: 2021-08-19
description: 开放平台appKey，appSecret设计
tags:
    - 设计
categories:
    - 设计
---



### 开放平台appKey appSecret设计

#### 1，手动注册客户端，服务端返回appKey appSecret  客户端和服务端都保存ak as

#### 2, 客户端第一次请求 通过appKey请求token

   服务端通过appKey，appSecret，时间戳，用户的必要信息生成token，可以使用JWTToken.

​    token具有有效期：**Token是客户端访问服务端的凭证。**

#### 3，客户端后续请求

  参数为：   Token + 当前时间戳 + 参数 +签名sign1

  签名sign1为 `Token + 当前时间戳 + 参数+appSecret`   按照一定签名算法比如 SHA256生成的签名字符串，**为了保证请求中参数不被流量劫持篡改**

#### 4，服务端收到请求进行校验

* 时间戳校验：请求时间和当前服务端时间大于一定范围，比如5分钟，拒绝执行
* Token解析：token过期拒绝执行
* 签名校验： 通过token解析获取到appKey，再获取到appSecret， 使用与客户端相同的签名算法SHA256得到签名sign2, 如果sign1不等于sign2，拒绝执行