---
title: Mysql技术内幕-第一章-MySql存储引擎
copyright: true
abbrlink: b46adbe2
date: 2020-04-07 17:01:07
categories:
  - NoteBook
tags: 
  - Mysql
  - Mysql技术内幕
  - 读书笔记
top:

---

> 存储引擎是基于表的，而不是数据库。

# 一：InnoDB存储引擎

​	InnoDB存储以引擎支持事务，其实际目标主要面向在线事务处理的应用。其特点是行锁设计、支持外键、并支持类似于Oracle的非锁定读，即默认读取操作不会产生锁。

​		

​	InnoDB将数据放在一个逻辑的表空间中，这个表空间就像黑河一样由InnoDB存储引擎自身进行管理。

​	InnoDB通过视同端版本并发控制（MVCC）来获得高并发性，并且实现了SQL标准的4中隔离级别，默认是REPEATABLE级别。InnoDB还提供了插入缓存、二次写、自适应哈希索引、预读等高性能高可用的功能。

​	对于表中数据的存储，InnoDB采用聚集的方式，因此每张表的存储都是按照主键的顺序进行存放。如果没有显示的在表定义时指定主键，InnoDB存储引擎会为每一行生成一个6字节的rewid，并以此作为主键。

