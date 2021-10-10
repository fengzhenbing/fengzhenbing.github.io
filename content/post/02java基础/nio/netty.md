### 线程模型

#### nio 多reacto

参考 https://www.jianshu.com/p/e0e7cb085fb4

![](https://gitee.com/fengzhenbing/picgo/raw/master/F6A1AB8C69F049B5A0178FD00F132D6F.png)

#### netty线程模型

![](https://gitee.com/fengzhenbing/picgo/raw/master/BE156EF4454949CF9E6E49CEEC0A2B2D.png)



eventloopgroup  eventloop channel handler关系 

![](https://gitee.com/fengzhenbing/picgo/raw/master/A20B60869216424BA7678D0D5213BFB6.png)



![](https://gitee.com/fengzhenbing/picgo/raw/master/227DA0D6B04B41449E83B72ED7098F5A.png)

### 责任链模型ChannelPipeline

![](https://gitee.com/fengzhenbing/picgo/raw/master/2CE502957CF84D2C8429CD2A0E998D54.png)



   pipeline 持有context的双链表

![](https://gitee.com/fengzhenbing/picgo/raw/master/914EBB37C6084D8A9EA6A105262A45A1.png)



   context 持有下一个和上一个context和 handler  , 并触发链式调用下一个或者上一个context的hander

![](https://gitee.com/fengzhenbing/picgo/raw/master/6F42E200304A47B095E78C10B5246039.png)



handler  自己写的时候，继承适配器即可

![](https://gitee.com/fengzhenbing/picgo/raw/master/B154DADDE045400881317E5E571A7AA8.png)

### 整体结构

![](https://gitee.com/fengzhenbing/picgo/raw/master/2E7F7EA909664C2989958271D27A09B4.png)

### netty 内存管理  bytebuf  pool

 https://people.freebsd.org/~jasone/jemalloc/bsdcan2006/jemalloc.pdf

![](https://gitee.com/fengzhenbing/picgo/raw/master/07723506D21F497FBAE1196D73A7626A.png)

引用计数。

![](https://gitee.com/fengzhenbing/picgo/raw/master/5562B699C19A4BD69705BBCCE0C3DD93.png)



![](https://gitee.com/fengzhenbing/picgo/raw/master/E0F2FA0E08C248C6B3A76F770FFB0947.png)

### 对象回收复用栈

https://www.jianshu.com/p/854b855bd198



### 零拷贝

netty https://www.jianshu.com/p/0ab705ae4f2d

FileChannel.transferTo() 实现nio的零拷贝

Netty之ChannelOption参数





### Netty 高性能表现在哪些方面？
IO 线程模型：同步非阻塞，用最少的资源做更多的事。

内存零拷贝：尽量减少不必要的内存拷贝，实现了更高效率的传输。

内存池设计：申请的内存可以重用，主要指直接内存。内部实现是用一颗二叉查找树管理内存分配情况。

串形化处理读写：避免使用锁带来的性能开销。

高性能序列化协议：支持 protobuf 等高性能序列化协议。 



