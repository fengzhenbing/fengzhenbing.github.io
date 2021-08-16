---
title: "Disruptor高性能队列"
date: 2021-07-20
description: Disruptor高性能队列
categories:                                 
    - 多线程
tags:
    - 多线程
    - 队列
---


## Disruptor通过以下设计来解决队列速度慢的问题：


- 环形数组结构

- 元素位置定位

  数组长度2^n， 位运算，加快定位的速度

- 无锁设计

  Cas操作保证线程安全



## 参考
 https://tech.meituan.com/2016/11/18/disruptor.html

https://blog.csdn.net/liweisnake/article/details/9113119