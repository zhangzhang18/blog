---
title: MQ归纳
copyright: true
abbrlink: b46a2be6
date: 2019-09-18 10:00:47
categories:
  - NoteBook
tags: 
  - Redis
top:
---

## MQ 
![Kafka](mq.png)

<!-- more -->

每一个Topic，Kafka都保存了一个分区Log，每一个分区都是一个提交日志，一系列有序不可变顺序的消息连续的追加到日志尾部。使用分区使得kafka可以在单个服务器上拓展，承载大量的数据。

这些分区分布在kafka集群上，每一个机器控制一组分区上数据和请求，可以并行接收请求，每个分区通过一定数量的服务器冗余提高容错率。

每一个分区都有一个服务器作为Leader，多个服务器作为Follow。Leader控制所有的读写操作，Follow被动的冗余Leader。如果Leader发生故障，Follow中的一个会自动成为Leader。



