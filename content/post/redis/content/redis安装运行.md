---
title: "安装redis"
date: 2020-12-10T08:06:25+06:00
description: 安装redis
tags:
    - 中间件
    - 缓存
categories:
    - 中间件
    - 缓存
---

## 下载

https://redis.io/download

## 编译

```shell
wget https://download.redis.io/releases/redis-6.0.10.tar.gz
tar xzf redis-6.0.10.tar.gz
cd redis-6.0.10
sudo make
```

## 运行

```shell
#复制配置文件及命令
mkdir ./bin
mkdir ./conf
sudo cp ./src/mkreleasehdr.sh ./bin
sudo cp ./src/redis-benchmark ./bin
sudo cp ./src/redis-check-rdb ./bin
sudo cp ./src/redis-cli ./bin
sudo cp ./src/redis-server ./bin
sudo cp ./redis.conf ./conf
#运行
./bin/redis-server ./conf/redis.conf
```

## 配置

redis.conf

```
#修改为守护模式
daemonize yes
#设置进程锁文件
pidfile /usr/local/redis-4.0.11/redis.pid
#端口
port 6379
#客户端超时时间
timeout 300
#日志级别
loglevel debug
#日志文件位置
logfile /usr/local/redis-4.0.11/log-redis.log
#设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
databases 16
##指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
#save <seconds> <changes>
#Redis默认配置文件中提供了三个条件：
save 900 1
save 300 10
save 60 10000
#指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，
#可以关闭该#选项，但会导致数据库文件变的巨大
rdbcompression yes
#指定本地数据库文件名
dbfilename dump.rdb
#指定本地数据库路径
dir /usr/local/redis-4.0.11/db/
#指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能
#会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有
#的数据会在一段时间内只存在于内存中
appendonly no
#指定更新日志条件，共有3个可选值：
#no：表示等操作系统进行数据缓存同步到磁盘（快）
#always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）
#everysec：表示每秒同步一次（折衷，默认值）
appendfsync everysec
```

## Docker

```shell
#最新镜像
docker pull redis
#运行
docker run -itd --name redis-test -p 6379:6379 redis docker image inspect redis:latest|grep -i version
#运行
docker exec -it redis-test /bin/bash
$ redis-cli
> info
```

- 指定配置文件运行

  ```shell
  docker run -p 6379:6379 --name redis-test -v /etc/redis/redis.conf:/etc/redis/redis.conf -v /etc/redis/data:/data -d redis redis-server /etc/redis/redis.conf --appendonly yes
  ```

- 停止

  ```
  docker stop redis-test
  ```