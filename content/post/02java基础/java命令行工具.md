---
title: "test"
date: 2021-03-15
description: test
draft: true
tags:
    - JVM
    - 工具
categories:
    - JVM
---



### **JVM** **命令行工具**

| **工具             | 简介**                          | 常用                                                         |
| :----------------- | :------------------------------ | ------------------------------------------------------------ |
| **jps/jinfo**      | 查看 java 进程                  |                                                              |
| **jstat**          | 查看 JVM 内部 gc 相关信息       | jstat -gc pid 1000 1000                                      |
| **jmap**           | 查看 heap 或类占用空间统计      | jmap -heap pid <br/>jmap -histo pid <br/>jmap -dump:format=b,file=3826.hprof 3826 |
| **jstack**         | 查看线程信息                    | jstack pid -l                                                |
| **jcmd**           | 执行 JVM 相关分析命令(整合命令) |                                                              |
| **jrunscript/jjs** | 执行 js 命令                    |                                                              |
| **javap**          | Java 字节码分析工具             | Javap -c ..                                                  |
| Jhat               | 分析 jmap -dump的文件           |                                                              |

#### jstat

\> jstat -options

-class 类加载(Class loader)信息统计.

-compiler JIT 即时编译器相关的统计信息。

-gc GC 相关的堆内存信息. 用法: jstat -gc -h 10 -t 864 1s 20

-gccapacity 各个内存池分代空间的容量。

-gccause 看上次 GC, 本次 GC(如果正在 GC中)的原因, 其他 输出和 -gcutil 选项一致。

-gcnew 年轻代的统计信息. (New = Young = Eden + S0 + S1) -gcnewcapacity 年轻代空间大小统计.
 -gcold 老年代和元数据区的行为统计。
 -gcoldcapacity old 空间大小统计.

-gcmetacapacity meta 区大小统计.
 -gcutil GC 相关区域的使用率(utilization)统计。 -printcompilation 打印 JVM 编译统计信息。

#### jmap

**查看实时堆中 对象大小个数**

```shell
 # 查看存活的 会导致一次fullgc
 jmap -histo:live  4551|grep com.xxx| head -150 
jmap -histo 29133 | head -150

jmap -dump:format=b,file=3826.hprof 3826
```

**内存排序** 

ps aux | sort -k4nr | head -10



#### jstack

**堆栈信息**

```shell
jstack  -l 29133
```



### **图形化工具**

| **工具        | 简介**     | 常用 |
| :------------ | :--------- | ---- |
| **jconsole**  |            |      |
| **jvisualvm** |            |      |
| **VisualGC**  | Ideas 插件 |      |
| **jmc**       |            |      |

