---
title: "redis应用场景"
date: 2020-12-13T08:06:25+06:00
description: redis应用场景
tags:
    - 中间件
    - 缓存
categories:
    - 中间件
    - 缓存
---

## 一.业务数据缓存*

经典用法。

1. 通用数据缓存，string，int，list，map等。

   * 验证码等

2. 实时热数据，最新500条数据。 

   * 如热搜新闻。。

3. 会话缓存，token缓存等。

   * spring-session-data-redis sesion共享

   

## 二.业务数据处理

1. 非严格一致性要求的数据

   * 评论，点击，点赞等。

     ```
     set key 0
     incr key // incr readcount::{帖子id} 每阅读一次
     get key // get readcount::{帖子id} 获取阅读量
     ```

      

2. 业务数据去重

   * 订单处理的幂等校验等。 如订单id放到redis 的set中去重复， bitmap 等

3. 业务数据排序

   * 排名，排行榜等。  使用sortedset

   

## 三.全局一致计数 *

1. 全局流控计数 

   多个服务节点使用同一个redis的计数。

2. 秒杀的库存计算 

   和全局计数类似

3. 抢红包 

   和全局计数类似

4. 全局ID生成

   例如：userId, 直接获取一段userId的最大值，缓存到本地服务慢慢累加，快到了userId的最大值时，再去获取一段，一个用户服务宕机了，也就一小段userId没有用到。 用数据库也可以。

   ```
   set userId 0
   incr usrId //返回1
   incrby userId 1000 //返回10001
   ```

   

## 四.高效统计计数

1. id去重，记录访问ip等

   全局bitmap操作 

2. UV、PV等访问量==>非严格一致性要求

## 五.发布订阅与Stream

1. Pub-Sub 模拟队列

   ```
   127.0.0.1:6379> subscribe fzb 
   Reading messages... (press Ctrl-C to quit)
   1) "subscribe"
   2) "fzb"
   3) (integer) 1
   1) "message"
   2) "fzb"
   3) "fff"
   1) "message"
   2) "fzb"
   3) "ffff"
   
   127.0.0.1:6379> publish fzb fff
   (integer) 1
   127.0.0.1:6379> publish fzb ffff
   (integer) 1
   ```

   

2. Redis Stream 是 Redis 5.0 版本新增加的数据结构。
   Redis Stream 主要用于消息队列(MQ，Message Queue)。 

   具体可以参考 [https://www.runoob.com/redis/redis-stream.html](https://www.runoob.com/redis/redis-stream.html)

## 六.分布式锁*

1、获取锁--单个原子性操作

- SET dlock my_random_value NX PX 30000

```
127.0.0.1:6379> set myLock 1 NX PX 30000
OK
127.0.0.1:6379> set myLock 1 NX PX 30000
(nil)
```



2、释放锁--lua脚本-保证原子性+单线程，从而具有事务性 .   => 因为内存操作是单线程的

```
if redis.call("get",KEYS[1]) == ARGV[1] then
return redis.call("del",KEYS[1]) else
return 0 end
```

- 关键点:原子性、互斥、超时



更多细节：[https://www.cnblogs.com/yunlongaimeng/p/10266690.html](https://www.cnblogs.com/yunlongaimeng/p/10266690.html)