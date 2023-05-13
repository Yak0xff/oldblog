---
title: "2022-07-04  关于死锁的简单记录" 
date: 2022-07-04 14:21:38
categories:
-  Logs
---

* **死锁** 指的是在多线程环境中，两个以上的线程在执行过程中，因争夺资源而造成一种相互等待的现象，如果无外力作用下，它们都将无法推进下去。

举个简单例子：

如下图所示，线程 A 持有资源 2，线程 B 持有资源 1，他们同时都想申请对方的资源，所以这两个线程就会互相等待而进入死锁状态。


![死锁图例](/images/logs/2022-07-04_log.png)

* **死锁的条件：**

* **互斥条件：**线程对资源的访问是排他性的，如果一个线程占用了某个资源，那么其他线程等待，直到锁被释放。
* **不可剥夺条件：**线程在已经获得资源的情况下，未使用完之前，不能被其他线程剥夺，只能在使用完之后自己释放。
* **保持和请求条件： **线程T1至少已经保持了一个资源R1的占用，同时又提出对另一个资源R2的请求，但是R2 又被其他线程T2占用，所以线程T1必须等待，但又对自己保持对R1 不释放，而T2又必须要得到R1的资源才能继续执行。
* **环路等待条件：**在死锁发生时，必然存在一个“线程-资源环形链”，即：{p0,p1,p2,…pn},进程p0（或线程）等待p1占用的资源，p1等待p2占用的资源，pn等待p0占用的资源。


* **避免线程死锁：**

避免死锁的方案可以从造成死锁的四个条件入手，破坏导致死锁必要条件中的任意一个就可以预防死锁：

* **破坏互斥条件：**这个条件我们没有办法破坏，因为我们用锁本来就是想让他们互斥的(临界资源需要互斥访问)。
* **破坏请求与保持条件：**不再分批申请资源，一次性申请所有的资源。
* **破坏不剥夺条件：**占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源。
* **破坏循环等待条件：**靠按序申请资源来预防。按某一顺序申请资源，释放资源则反序释放。破坏循环等待条件。对于前面的例子如果线程1，线程2都是先申请资源1，再申请资源2就不会导致死锁了。

**死锁的例子：**

```objc
NSObject *resource1 = [NSObject new];
NSObject *resource2 = [NSObject new];

dispatch_queue_t queue1 = dispatch_queue_create("thread1", DISPATCH_QUEUE_CONCURRENT);
dispatch_queue_t queue2 = dispatch_queue_create("thread2", DISPATCH_QUEUE_CONCURRENT);

dispatch_async(queue1, ^{
    @synchronized (resource1) {
        IDLLogInfo(@"queue1获取到资源1");
        sleep(10);
        IDLLogInfo(@"queue1尝试获取资源2");
        @synchronized (resource2) {
             IDLLogInfo(@"queue1获取到资源2");
         }
    }
});
    
dispatch_async(queue2, ^{
  @synchronized (resource2) {
      IDLLogInfo(@"queue2获取到资源2");
      sleep(20);
       IDLLogInfo(@"queue2尝试获取资源1");
       @synchronized (resource1) {
            IDLLogInfo(@"queue2获取到资源1");
       }
   }
});
```

