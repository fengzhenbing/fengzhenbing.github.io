---
title: "Synchronized锁"
date: 2020-12-15
description: Synchronized锁
categories:                                 
    - 多线程
tags:
    - 锁
    - 多线程
---

## Synchronized

- **Object.wait()**：释放当前对象锁，并进入阻塞队列(wait set)
- **Object.notify()**：唤醒当前对象阻塞队列(wait set)里的任一线程（并不保证唤醒哪一个）
- **Object.notifyAll()**：唤醒当前对象阻塞队列(wait set)里的所有线程, 进到entry set 去竞争锁



## 为什么wait,notify和notifyAll要与synchronized一起使用？

Wait  只有通过synchronized拿到锁，才能进入wait set

notify notifyAll只有通过synchronized拿到锁，才能去唤醒 wait set 里线程 到entry set



## object monitor

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019031712391984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211bGluc2VuNzc=,size_16,color_FFFFFF,t_70)

## 对象在内存中的存储

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019012010560977.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211bGluc2VuNzc=,size_16,color_FFFFFF,t_70)

Markword  32位jvm 结构如下：  重量级锁即为 Synchronized 的锁

![image-20210418232331824](https://gitee.com/fengzhenbing/picgo/raw/master/image-20210418232331824.png)

##  锁升级

参考 https://mp.weixin.qq.com/s/2yxexZUr5MWdMZ02GCSwdA