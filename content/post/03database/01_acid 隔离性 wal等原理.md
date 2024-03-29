---
title: 数据库原理
date: 2020-08-12T14:16:25+06:00
description: 数据库原理
draft: true
categories:                                 
    - 数据库
tags:
    - 数据库
---



## 1 介绍

本地事务（局部事务）在程序代码层面，最多只能对事务接口做一层标准化的包装（如 JDBC 接口），并不能深入参与到事务的运作过程当中，事务的开启、终止、提交、回滚、嵌套、设置隔离级别，乃至与应用代码贴近的事务传播方式，全部都要依赖底层数据源的支持才能工作。



## 2 原子性（A）和持久性（D）

崩溃 （Crash）：数据库、操作系统一侧的崩溃，甚至是机器突然断电宕机等意外情况。

崩溃恢复（Crash Recovery，也有资料称作 Failure Recovery 或 Transaction Recovery）:为了保证原子性和持久性，就只能在崩溃后采取恢复的补救措施

### Commit Logging

为了能够顺利地完成崩溃恢复，在磁盘中写入数据就不能像程序修改内存中变量值那样，直接改变某表某行某列的某个值，而是必须将修改数据这个操作所需的全部信息，包括修改什么数据、数据物理上位于哪个内存页和磁盘块中、从什么值改成什么值，等等，以日志的形式——即仅进行顺序追加的文件写入的形式（这是最高效的写入方式）先记录到磁盘中。只有在日志记录全部都安全落盘，数据库在日志中看到代表事务成功提交的“提交记录”（Commit Record）后，才会根据日志上的信息对真正的数据进行修改，修改完成后，再在日志中加入一条“结束记录”（End Record）表示事务已完成持久化.

阿里的[OceanBase](https://zh.wikipedia.org/wiki/OceanBase)  采用Commit Logging 机制来实现事务



缺点：所有对数据的真实修改都必须发生在事务提交以后，即日志写入了 Commit Record 之后。在此之前，即使磁盘 I/O 有足够空闲、即使某个事务修改的数据量非常庞大，占用了大量的内存缓冲区，无论有何种理由，都决不允许在事务提交之前就修改磁盘上的数据。



### Write-Ahead Logging

按照事务提交时点为界，划分为 FORCE 和 STEAL 两类情况。

- **FORCE**：当事务提交后，要求变动数据必须同时完成写入则称为 FORCE，如果不强制变动数据必须同时完成写入则称为 NO-FORCE。现实中绝大多数数据库采用的都是 NO-FORCE 策略，因为只要有了日志，变动数据随时可以持久化，从优化磁盘 I/O 性能考虑，没有必要强制数据写入立即进行。
- **STEAL**：在事务提交前，允许变动数据提前写入则称为 STEAL，不允许则称为 NO-STEAL。从优化磁盘 I/O 性能考虑，允许数据提前写入，有利于利用空闲 I/O 资源，也有利于节省数据库缓存区的内存。

![force-steal](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnYAAAD3CAMAAACn+emfAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyFpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNS1jMDE0IDc5LjE1MTQ4MSwgMjAxMy8wMy8xMy0xMjowOToxNSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIChXaW5kb3dzKSIgeG1wTU06SW5zdGFuY2VJRD0ieG1wLmlpZDpDNkVCQjZENkRDQjExMUVBQjJFNUFDQzNEQ0I4MEI1RiIgeG1wTU06RG9jdW1lbnRJRD0ieG1wLmRpZDpDNkVCQjZEN0RDQjExMUVBQjJFNUFDQzNEQ0I4MEI1RiI+IDx4bXBNTTpEZXJpdmVkRnJvbSBzdFJlZjppbnN0YW5jZUlEPSJ4bXAuaWlkOkM2RUJCNkQ0RENCMTExRUFCMkU1QUNDM0RDQjgwQjVGIiBzdFJlZjpkb2N1bWVudElEPSJ4bXAuZGlkOkM2RUJCNkQ1RENCMTExRUFCMkU1QUNDM0RDQjgwQjVGIi8+IDwvcmRmOkRlc2NyaXB0aW9uPiA8L3JkZjpSREY+IDwveDp4bXBtZXRhPiA8P3hwYWNrZXQgZW5kPSJyIj8+k8yiTAAAADNQTFRFkpKSS0tL3t7e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)



Write-Ahead Logging 在崩溃恢复时会执行以下三个阶段的操作：

- **分析阶段**（Analysis）：该阶段从最后一次检查点（Checkpoint，可理解为在这个点之前所有应该持久化的变动都已安全落盘）开始扫描日志，找出所有没有 End Record 的事务，组成待恢复的事务集合，这个集合至少会包括 Transaction Table 和 Dirty Page Table 两个组成部分。
- **重做阶段**（Redo）：该阶段依据分析阶段中产生的待恢复的事务集合来重演历史（Repeat History），具体操作为：找出所有包含 Commit Record 的日志，将这些日志修改的数据写入磁盘，写入完成后在日志中增加一条 End Record，然后移除出待恢复事务集合。
- **回滚阶段**（Undo）：该阶段处理经过分析、重做阶段后剩余的恢复事务集合，此时剩下的都是需要回滚的事务，它们被称为 Loser，根据 Undo Log 中的信息，将已经提前写入磁盘的信息重新改写回去，以达到回滚这些 Loser 事务的目的。

### Shadow Paging

副本方式

对数据的变动会写到硬盘的数据中，但并不是直接就地修改原先的数据，而是先将数据复制一份副本，保留原数据，修改副本数据。

事务完成修改数据引用指针 （修改指针保证原子性）



## 3 隔离性

### 锁

#### **写锁**

（Write Lock，也叫作排他锁，eXclusive Lock，简写为 X-Lock）：如果数据有加写锁，就只有持有写锁的事务才能对数据进行写入操作，数据加持着写锁时，其他事务不能写入数据，也不能施加读锁。

#### 读锁

（Read Lock，也叫作共享锁，Shared Lock，简写为 S-Lock）：多个事务可以对同一个数据添加多个读锁，数据被加上读锁后就不能再被加上写锁，所以其他事务不能对该数据进行写入，但仍然可以读取。对于持有读锁的事务，如果该数据只有它自己一个事务加了读锁，允许直接将其升级为写锁，然后写入数据。

#### 范围锁

对于某个范围直接加排他锁，在这个范围内的数据不能被写入

```sql
SELECT * FROM books WHERE price < 200 FOR UPDATE;
```



以下四种隔离级别属于数据库理论的基础知识， 其实不同隔离级别以及幻读、不可重复读、脏读等问题都只是表面现象，是各种锁在不同加锁时间上组合应用所产生的结果，以锁为手段来实现隔离性才是数据库表现出不同隔离级别的根本原因。



### 完全不加锁。。。

### 读未提交

缺点：出现赃读

```sql
SELECT * FROM books WHERE id = 1;   						/* 时间顺序：1，事务： T1 */
/* 注意没有COMMIT */
UPDATE books SET price = 90 WHERE id = 1;					/* 时间顺序：2，事务： T2 */
/* 这条SELECT模拟购书的操作的逻辑 */
SELECT * FROM books WHERE id = 1;			  				/* 时间顺序：3，事务： T1 */
ROLLBACK;			  										/* 时间顺序：4，事务： T2 */
```

实现方式：不加读锁 ，还是有写锁的，不会出现赃写

### 读已提交

缺点：不可重复读，只能读到提交的数据。  缺乏贯穿整个事务周期的读锁，无法禁止读取过的数据发生变化，

```sql
SELECT * FROM books WHERE id = 1;   						/* 时间顺序：1，事务： T1 */
UPDATE books SET price = 110 WHERE id = 1; COMMIT;			/* 时间顺序：2，事务： T2 */
SELECT * FROM books WHERE id = 1; COMMIT;   				/* 时间顺序：3，事务： T1 */
```

实现方式：对事务涉及的数据加的写锁会一直持续到事务结束，但加的读锁在查询操作完成后就马上会释放。

### 可重复读

相对于读已提交，读锁存在时间为整个事物周期

缺点：可能出现幻读

```sql
SELECT count(1) FROM books WHERE price < 100					/* 时间顺序：1，事务： T1 */
INSERT INTO books(name,price) VALUES ('深入理解Java虚拟机',90)	/* 时间顺序：2，事务： T2 */
SELECT count(1) FROM books WHERE price < 100					/* 时间顺序：3，事务： T1 */
```

实现方式：对事务所涉及的数据加读锁和写锁，且一直持有至事务结束，但不再加范围锁。



### 可串行化

缺点：顺序执行，吞吐量低，性能低

实现方式： 对事务所有读、写的数据全都加上读锁、写锁和范围锁；实际还是很复杂的，要分成 Expanding 和 Shrinking 两阶段去处理读锁、写锁与数据间的关系，称为[Two-Phase Lock](https://en.wikipedia.org/wiki/Two-phase_locking)，2PL





## 4 多版本并发控制



参考 （https://icyfenix.cn/architect-perspective/general-architecture/transaction/local.html）