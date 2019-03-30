---
title: "关于Akka"
categories:
  - Tec
tags:
  - Akka
  - Actor Model
toc: true
header:
  teaser: /assets/blog_images/akka.jpeg
---
现代多线程解决方案 Actor Model，还有它的实现：Akka。

## 传统多线程编程遇到的问题
之所以叫现代多线程解决方案，是因为它更加适合现代的硬件设备。
以下介绍面向对象编程语言（OOP）在多线程环境下遇到的问题。
### 关于封装
OOP核心之一：封装。封装使对象中的数据不能直接被外部访问，并且只能
被一组方法所更改，这样可以保证数据的安全性和不变性。假设一个调用链：

![seq_chart](/assets/blog_images/seq_chart.png)

单线程环境：

![seq_chart_thread](/assets/blog_images/seq_chart_thread.png)

多线程环境下：

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

⭐️ [Akka 官网](https://doc.akka.io/docs/akka/current/guide/introduction.html)