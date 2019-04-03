---
title: "现代多线程框架：Akka"
categories:
  - Tec
tags:
  - Akka
  - Actor Model
toc: true
header:
  teaser: /assets/blog_images/akka.jpeg
---
本篇文章介绍 Actor Model，还有它的实现：Akka。

## 传统多线程编程遇到的问题
之所以叫现代多线程解决方案，是因为它更加适合现代的硬件设备。
以下介绍面向对象编程语言（OOP）在多线程环境下遇到的问题。
### 关于封装
OOP核心之一：封装。封装使对象中的数据不能直接被外部访问，并且只能
被一组方法所更改，这样可以保证数据的安全性。假设一个调用链：

![seq_chart](/assets/blog_images/seq_chart.png)

单线程环境下的执行示意图：

![seq_chart_thread](/assets/blog_images/seq_chart_thread.png)

多线程环境下的执行示意图：

![seq_chart_multi_thread](/assets/blog_images/seq_chart_multi_thread.png)

很明显在多线程环境中，传统的封装并不能保证在两个线程同时进入方法时后发生的事情。
为了解决这个问题，我们引入了一个代价非常高的解决方式：锁
* 锁严重的影响了并发性，并且在现代的CPU架构上代价非常高。
* 锁引入了一个新的问题：死锁。
* 在分布式系统中，锁存在一定的局限性。

### 关于共享内存
在现代CPU中每一个内核都有自己的缓存，而这个缓存对其它内核都是不可见的。
在JVM中我们可以使用`volatile`标记或者`Atomic`包装来表示需要跨线程共享的内存位置。但是共享的代价非常高。
在内核中的数据必须先刷入主存储器，其它内核每次使用时都需要从主存储器中取出。更多请了解[缓存一致性协议
（ cache coherence protocol）](https://en.wikipedia.org/wiki/Cache_coherence)。

### 关于调用栈
调用栈是在一个并发编程并不重要的时代发明的，在那个时候多核CPU并不常见，因此并没有为异步调用考虑合适的调用模型。

问题出现在当一个线程打算委派任务给另一个线程时，通常是将对象放入由工作者线程共享的内存，再由工作者在某些事件循环中拾取，
调用者便可返回继续进行其它任务。

第一个问题是，怎样通知调用者任务已经完成？但是更严肃的一个问题是当任务失败或者异常时，异常将会传播到哪里？它会传播给工作者
线程的异常处理程序，而完全忽略了调用者是谁。

![exception_prop](/assets/blog_images/exception_prop.png)

通常情况下工作者线程会无视任务的失败，它需要通过某种方式通知调用者线程，将错误代码放入调用者预先设定好的位置，如果没有这个通知，调用者
将永远不会收到失败通知，任务就会丢失！

当出现问题时，情况会更加糟糕，例如：由BUG引起的内部异常冒泡到线程顶端，并使线程关闭。这引发了另一问题，由谁来重新启动工作者线程，
以及恢复到正常状态？线程正在处理的任务已经不再位于共享的内存中（例如队列）。

## Actor Model 如何处理这些问题

[Akka 官方介绍](https://doc.akka.io/docs/akka/current/guide/introduction.html)