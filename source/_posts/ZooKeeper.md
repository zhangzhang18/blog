---
title: ZooKeeper
copyright: true
abbrlink: bf85b35c
date: 2018-12-26 10:50:47
categories:
  - NoteBook
tags:
  - 分布式
  - ZK
top:
---

分布式协调服务-可以在分布式系统中共享配置，协调锁资源，提供命名服务

Zookeeper的数据模型：像数据结构中的树，也像文件系统中的目录
  <!-- more -->
  - Znode：包含数据，子节点引用，访问权限等。每个节点的数据最大不能超过1MB
Zookeeper包含的基本操作
- create：创建节点
- delete：删除节点
- exists：判断节点是否存在
- getData：获得一个节点的数据
- setData：设置一个节点的数据
- getChildren：获取节点下的所有子节点

Zookeeper一致性
- Zookeeper Service集群是一主多从结构，更新数据时，首先跟新到主节点，在同步从节点。读取数据的时候可以读取任意节点

Zookeeper应用场景：

- 分布式协调服务
- 分布式锁
- 服务注册与发现
- 共享配置和状态信息


 
[Zookeeper IBM](https://www.ibm.com/developerworks/cn/opensource/os-cn-zookeeper/index.html)
[小灰 什么是ZooKeeper？](https://juejin.im/post/5b037d5c518825426e024473)