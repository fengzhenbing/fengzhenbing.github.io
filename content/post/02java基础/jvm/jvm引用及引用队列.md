![image-20211007143642433](https://gitee.com/fengzhenbing/picgo/raw/master/image-20211007143642433.png)

##### 强引用(StrongReference)

强引用就是我们平时创建对象，创建数组时的引用。强引用在任何时候都不会被GC回收掉。内存不足，oom



##### 软引用(SoftReference)

软引用是在系统发生OOM之前才被JVM回收掉。软引用常被用来对于内存敏感的缓存。

##### 弱引用(WeakReference)

一旦JVM执行GC，弱引用就会被回收掉。

##### (虚引用)PhantomReference

虚引用主要作为其指向referent被回收时的一种通知机制。



#####  ReferenceQueue引用队列

用于存放待回收的引用对象（GC 会把引用对象本身添加到这个队列中）

ReferenceQueue一般用来与SoftReference、WeakReference或者PhantomReference配合使用，将需要关注的引用对象注册到引用队列后，便可以通过监控该队列来判断关注的对象是否被回收，从而执行相应的方法。

主要使用场景：

1、使用引用队列进行数据监控，类似前面栗子的用法。

2、队列监控的反向操作

参考 https://www.cnblogs.com/mfrank/p/10104586.html