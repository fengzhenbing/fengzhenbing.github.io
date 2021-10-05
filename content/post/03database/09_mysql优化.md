---
title: mysql优化
date: 2020-09-12T14:16:25+06:00
description: mysql优化
draft: true
categories:                                 
    - 数据库
tags:
    - 数据库


---



## 

### 参数配置优化

参数配置优化**-1**

1)连接请求的变量

 1、max_connections
 2、back_log 

3、wait_timeout和interative_timeout



参数配置优化**-2**

2)缓冲区变量
 4、key_buffer_size 

5、query_cache_size(查询缓存简称 QC) 

6、max_connect_errors: 

7、sort_buffer_size: 

8、max_allowed_packet=32M 

9、join_buffer_size=2M 

10、thread_cache_size=300



###  Innodb配置优化

参数配置优化**-3**

3)配置 Innodb 的几个变量 

11、innodb_buffer_pool_size 

12、innodb_flush_log_at_trx_commit 

13、innodb_thread_concurrency=0 

14、innodb_log_buffer_size 

15、innodb_log_file_size=50M 

16、innodb_log_files_in_group=3 

17、read_buffer_size=1M 

18、read_rnd_buffer_size=16M 

19、bulk_insert_buffer_size=64M 

20、binary log