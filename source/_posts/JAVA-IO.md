---
title: JAVA-IO
copyright: true
categories:
  - NoteBook
tags:
  - JAVA
  - 基础
  - IO
abbrlink: d27dc71e
date: 2018-05-18 15:44:56
top:
---

设计模式：装饰者模式
![iostream](iostream.png)

<!-- more -->

[漫画解释同步/异步、阻塞/非阻塞](https://mp.weixin.qq.com/s/Csi_ySQxoZ3YfpkkMwv9Ig)

```
区分同步或异步（synchronous/asynchronous）。
1,简单来说，同步是一种可靠的有序运行机制，当我们进行同步操作时，后续的任务是等待当前调用返回，才会进行下一步；
2,而异步则相反，其他任务不需要等待当前调用返回，通常依靠事件、回调等机制来实现任务间次序关系。

区分阻塞与非阻塞（blocking/non-blocking）。
1,在进行阻塞操作时，当前线程会处于阻塞状态，无法从事其他任务，  只有当条件就绪才能继续，比如 ServerSocket 新连接建立完毕，或数据读取、写入操作完成；
2,而非阻塞则是不管 IO 操作是否结束，直接返回，相应操作在后台继续处理。
```

`女朋友拉我一起逛街，她去挑选衣服，我坐着玩手机，时不时问她好没好【同步非阻塞】`

**NIO**
[Snailclimb Java NIO 概览](https://link.juejin.im/?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzU4NDQ4MzU5OA%3D%3D%26mid%3D2247483956%26idx%3D1%26sn%3D57692bc5b7c2c6dfb812489baadc29c9%26chksm%3Dfd985455caefdd4331d828d8e89b22f19b304aa87d6da73c5d8c66fcef16e4c0b448b1a6f791%23rd)

### IO是什么？

 IO的全称其实是：Input/Output的缩写 。

### BIO、NIO、AIO 的区别是什么？ 

**BIO**（Blocking） **同步、阻塞**：一排烧水壶在烧水，BIO的工作模式是，叫一个线程留着水壶那，直到这个水壶烧开，才去处理下一个水壶。实际上线程在等待水壶烧开的这段时间什么都没做。

**NIO**(New可以用非阻塞理解 )：工作模式是叫一个线程不断的轮询每个水壶的状态，看看水壶状态是否改变，在进行下一步。提供了 Channel、Selector、Buffer 等新的抽象 ， 可以构建**多路复用**的、**同步非阻塞** IO 程序， 

**AIO**（Asynchronous）**异步、非阻塞**：为每个水壶装一个开关，水烧开后，水壶会通知我水烧开了。异步 IO 操作基于**事件和回调机制**，可以简单理解为，应用操作直接返回，而不会阻塞在那里，当后台处理完成，操作系统会通知相应线程进行后续工作。

### NIO

#### Buffer缓冲区

是一个对象，包含一些要写入或者读出的数据。 在NIO库中，所有数据都是用**缓冲区**处理的。在读取数据时，它是直接读到缓冲区中的；在写入数据时，也是写入到缓冲区中。任何时候访问NIO中的数据，都是通过缓冲区进行操作。

 当数据到达时，可以预先被写入缓冲区，再由缓冲区交给线程，因此线程无需阻塞地等待IO。   

  具体的缓存区有这些：ByteBuffe、CharBuffer、 ShortBuffer、IntBuffer、LongBuffer、FloatBuffer、DoubleBuffer。他们实现了相同的接口：Buffer。 

#### Channel通道

我们对数据的读取和写入要通过Channel，它就像水管一样，是一个通道。通道不同于流的地方就是通道是双向的，可以用于读、写和同时读写操作。 

 Java NIO 中权威的说法：通道是 I/O 传输发生时通过的入口，而缓冲区是这些数据传输的来源或目标。对于离开缓冲区的传输，您想传递出去的数据被置于一个缓冲区，被传送到通道。对于传回缓冲区的传输，一个通道将数据放置在您所提供的缓冲区中。 

Channel主要分两大类：

- SelectableChannel：用户网络读写

- FileChannel：用于文件操作

  服务器缓冲区：serverBuffer，客户端缓冲区：clientBuffer。

    当服务器想向客户端发送数据时，需要调用：clientChannel.write(serverBuffer)。当客户端要读时，调用 clientChannel.read(clientBuffer)

    当客户端想向服务器发送数据时，需要调用：serverChannel.write(clientBuffer)。当服务器要读时，调用 serverChannel.read(serverBuffer)

#### Selector多路复用器

   通道和缓冲区的机制，使得线程无需阻塞地等待IO事件的就绪，但是总是要有人来监管这些IO事件。这个工作就交给了selector来完成，这就是所谓的**同步**。

  Selector允许单线程处理多个 Channel。如果你的应用打开了多个连接（通道），但每个连接的流量都很低，使用Selector就会很方便。 

-   IO是面向流的，NIO是面向缓冲区的
-   IO流是阻塞的，NIO流是不阻塞的。
-   NIO有选择器，而IO没有。

### NIO 如何实现多路复用功能？

 Java I/O多路复用简单的说，就是用单线程，记录并跟踪每个I/O流(sock)的状态，达到一个线程同时管理多个I/O流的效果 ，这就是Java I/O multiplexing。 

[图解](https://cs.xieyonghui.com/java/33.html)

[深入理解NIO](https://www.cnblogs.com/geason/p/5774096.html)