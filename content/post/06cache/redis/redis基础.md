---
title: "redis基础"
date: 2020-12-11T08:06:25+06:00
description: redis基础
tags:
    - 中间件
    - 缓存
categories:
    - 中间件
    - 缓存
---

### redis线程

redis做为一个进程，一直是多线程的。处理io和处理内存的是不同线程。

* io线程

  * redis6之前(2020.05)：io处理是单线程

  * redis6：io处理多线程，采用nio模型    => 主要的性能提升点

* 内存处理线程

  * 单线程  =>高性能核心，不用考虑线程调度

    

## 压测redis-benchmark

- 环境mac 4核8g

```shell
mokernetdeMac-mini:redis-6.0.9 mokernet$ ./bin/redis-benchmark -n 100000 -c 32 -t SET,GET,INCR,HSET,LPUSH,MSET -q
SET: 121065.38 requests per second
GET: 118764.84 requests per second
INCR: 117508.81 requests per second
LPUSH: 123001.23 requests per second
HSET: 123915.74 requests per second
MSET (10 keys): 96711.80 requests per second
```

## redis的5种基本数据结构

https://redis.io/commands

### 1.字符串(string)

简单来说就是三种:int、string、byte[]

Redis中字符串类型的value最多可以容纳的数据长度是512M

```
set/get/setnx/getset/del/exists/append incr/decr/incrby/decrby
```



### 2.散列(hash)

Redis中的Hash类型可以看成具有String key 和String value的map容器。

```
hset/hget/hmset/hmget/hgetall/hdel/hincrby hexists/hlen/hkeys/hvals
```



* hmset 相对于hset可一次设置多个键值对
* hmget 相对于hget可一次获取多个键的值

###  3.列表(list)

java的LinkedList

在Redis中，List类型是按照插入顺序排序的字符串链表。和数据结构中的普通链表 一 样，我们可以在其头部(Left)和尾部(Right)添加新的元素。在插入时，如果该键并不存 在，Redis将为该键创建一个新的链表。与此相反，如果链表中所有的元素均被移除， 那么该键也将会被从数据库中删除。

```
lpush/rpush/lrange/lpop/rpop
```



### 4.集合(set)

java的set，不重复的list

在redis中，可以将Set类型看作是没有排序的字符集合，和List类型一样，我们也可以 在该类型的数值上执行添加、删除和判断某一元素是否存在等操作。这些操作的时间复 杂度为O(1),即常量时间内完成依次操作。

和List类型不同的是，Set集合中不允许出现重复的元素。

```
sadd/srem/smembers/sismember  类比java中set的add, remove, all,  contains, 
spop key [count] 随机返回集合中一个或多个 移除
SRANDMEMBER key [count] 返回集合中一个或多个随机数，不移除, 抽奖
sdiff/sinter/sunion  集合求差集，求交集，求并集
```



### 5.有序集合(sorted set)

按权重排序

sortedset在set基础上给每个元素加了个分数score。

redis 正是通过分数来为集合的成员进行从小到大的排序。sortedset中分数是可以重复的。

```
zadd key score member score2 member2... : 将成员以及该成员的分数存放到sortedset中 
zscore key member : 返回指定成员的分数
zcard key : 获取集合中成员数量
zrem key member [member...] : 移除集合中指定的成员，可以指定多个成员
zrange key start end [withscores] : 获取集合中脚注为start-end的成员，[withscores]参数表明返回的成员 包含其分数
zrevrange key start stop [withscores] : 按照分数从大到小的顺序返回索引从start到stop之间的所有元素 (包含两端的元素)
zremrangebyrank key start stop : 按照排名范围删除元素
```

## redis高级数据结构

### 1.Bitmaps

bitmaps不是一个真实的数据结构。而是String类型上的一组面向bit操作的集合。由于 strings是二进制安全的blob，并且它们的最大长度是512m，所以bitmaps能最大设置 2^32个不同的bit。

```
setbit/getbit/bitop/bitcount/bitpos
```

可用作集中式的冥等去重    数据压缩在字节位上，极大的节约了空间



### 2.HyperLogLog

Redis  是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基 数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

```
pfadd 添加
pfcount 获得基数值
pfmerge 合并多个key
```

```shell
127.0.0.1:6379> pfadd pf1 1 3 1 4 1 5
(integer) 1
127.0.0.1:6379> pfcount pf1
(integer) 4
127.0.0.1:6379> pfadd pf2 3 4 5  6 7
(integer) 1
127.0.0.1:6379> pfcount pf2
(integer) 5
127.0.0.1:6379> pfmerge pf3 pf1 pf2
OK
127.0.0.1:6379> pfcount pf3
(integer) 6
```



应用场景说明：

* 基数不大，数据量不大就用不上，会有点大材小用浪费空间
* 有局限性，就是只能统计基数数量，而没办法去知道具体的内容是什么
* 和bitmap相比，属于两种特定统计情况，简单来说，HyperLogLog 去重比 bitmap 方便很多
* 一般可以bitmap和hyperloglog配合使用，bitmap标识哪些用户活跃，hyperloglog计数

一般使用：

- 统计注册 IP 数
- 统计每日访问 IP 数
- 统计页面实时 UV 数
- 统计在线用户数
- 统计用户每天搜索不同词条的个数



### 3.GEO

```
geoadd/geohash/geopos/geodist/georadius/georadiusbymember
```

Redis的GEO特性在 Redis3.2版本中推出，这个功能可以将用户给定的地理位置(经 度和纬度)信息储存起来，并对这些信息进行操作。



### 4.Redis 中的布隆过滤器

Redis v4.0 之后有了 [Module](https://redis.io/modules)（模块/插件）,RedisBloom 作为 Redis 布隆过滤器的 Module，地址：https://github.com/RedisBloom/RedisBloom 



## Redis Lua

* 类比openrestry = nginx + lua jit 

* 类似于数据库的存储过程，mongodb的js脚本

* redis内存操作的单线程 使得一段lua脚本之心具有：**原子性**，操作不会被打断，保证了**事务**

  * 直接执行
    eval "return'hello java'" 0
    eval "redis.call('set',KEYS[1],ARGV[1])" 1 lua-key lua-value

    ```
    127.0.0.1:6379> eval "return'hello java'" 0
    "hello java"
    127.0.0.1:6379> eval "redis.call('set',KEYS[1],ARGV[1])" 1 ff zz
    (nil)
    127.0.0.1:6379> get ff
    "zz"
    ```

    

  * 预编译
    script load script脚本片段
    返回一个SHA-1签名 shastring
    evalsha shastring keynum [key1 key2 key3 ...] [param1 param2 param3 ...]

    ```
    127.0.0.1:6379> script load "redis.call('set',KEYS[1],ARGV[1])"
    "7cfb4342127e7ab3d63ac05e0d3615fd50b45b06"
    127.0.0.1:6379> evalsha 7cfb4342127e7ab3d63ac05e0d3615fd50b45b06 1 fff zzz
    (nil)
    127.0.0.1:6379> get fff
    "zzz"
    ```




## redis pipeline

使用管道一次执行多个命令，多个命令的结果一起返回，减少每个命令来回建立链接，来回响应的时间。

https://redis.io/topics/pipelining

## redis事务

* 开启事务:multi 

* 命令入队 

* 执行事务:exec 

* 撤销事务:discard
* Watch 监控事务： watch 一个key，发生变化则事务失败
* unwatch 取消监听

```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set ff ff
QUEUED
127.0.0.1:6379> set fff fff
QUEUED
127.0.0.1:6379> exec
1) OK
2) OK
```



## redis管道



## redis备份恢复机制

### RDB方式

* 快照恢复，类似mysql的frm.等数据：备份当前瞬间 Redis 在内存中的数据记录

* save后在数据目录生成dump.rdb

* bgsave 异步执行备份

  ![img](http://pics5.baidu.com/feed/1c950a7b02087bf43b4490d50ac25f2a11dfcf7e.jpeg?token=22f387ba78130c6115420059481b2393&s=EF48A15796784D8816E1D9EB03007024)

* 恢复：将备份文件 (dump.rdb) 移动到 redis 数据目录并启动服务即可

redis.conf中

```
#Redis默认配置文件中提供了三个备份条件： ##指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
save 900 1
save 300 10
save 60 10000
```

### AOF方式

* 追加文件（Append-Only File，AOF） 类比mysql的binlog 
* 配置为 always，其含义为当 Redis 执行 命令的时候，则同时同步到 AOF 文件，这样会使得 Redis 同步刷新 AOF 文件，造成 缓慢。而采用 evarysec 则代表每秒同步一次命令到 AOF 文件

```
appendfilename "appendonly.aof" 
# appendfsync always
 appendfsync everysec
# appendfsync no......
```



![img](http://pics5.baidu.com/feed/b17eca8065380cd7df69859ba056a5325982816c.jpeg?token=a060f459d81c409c3d6c7208d2118888&s=AF4AA5574ED85CC841D04BE60300A036)



* 恢复：自动加载



## redis性能优化

```shell
slowlog get 10
```





## 几个缓存问题

### 缓存穿透

* 现象：key对应的数据在数据源并不存在，每次针对此key的请求从缓存获取不到，请求都会到数据源，从而可能压垮数据源。比如用一个不存在的用户id获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库
* 解决：
  * 采用布隆过滤器：将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被 这个bitmap拦截掉，从而避免了对底层存储系统的查询压力，详见https://www.cnblogs.com/rinack/p/9712477.html
  * 直接缓存空结果：如果一个查询返回的数据为空，仍然把这个空结果进行缓存。

### 缓存击穿

* 现象：key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。
* 解决：使用**互斥分布式锁**进行从DB加载数据，其他的请求继续重试缓存。使用锁可能会死锁、线程池阻塞等问题，针对高热点key，最好是在并发量最小的时候，写定时器更新key的过期时间

### 缓存雪崩

* 现象：当缓存服务器重启或者大量缓存集中在某一个时间段失效，这样在失效的时候，也会给后端系统(比如DB)带来很大压力。与缓存击穿的区别在于这里针对很多key缓存，前者则是某一个key。
* 解决：
  * 最简单尽量将过期时间分散开来：可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，
  * 设置过期标志更新缓存:  
    * 1，新加一个缓存key的标记。缓存数据key的value时，同时缓存key_sign, key_sign的过期时间小于key的过期时间。
    * 2 在查询缓存时，先判断key_sign是否过期，a，如果过期，先直接返回value，再启用异步线程去更新key_sign以及加载db到key的value,重新设置两个的过期时间。b. 没有过期，直接返回value