---
title: Spring-事物
copyright: true
abbrlink: b5de27d3
date: 2018-02-16 12:50:21
categories:
  - NoteBook
tags:
  - JAVA
  - 基础
top:
---

![事物](shiwu.png)
<!-- more -->
1. 原子性：要么全部执行成功，要么全部执行失败
2. 一致性：在事务开始之前和事务结束以后，数据库的完整性没有被破坏
3. 隔离性：并发的食物是互相隔离的，一个事物执行不被其他事物影响
4. 持久性：事物一旦提交，对数据库的改变是永久性的

1. 脏读：指在一个事务处理过程里读取了另一个未提交的事务中的数据。
2. 不可重复读：一个事务范围内多次查询却返回了不同的数据值，这是由于在查询间隔，被另一个事务修改并提交了。
3. 幻读：同样的事务操作，在前后两个时间段内执行对同一个数据项的读取，可能出现不一致的结果。幻读和不可重复读都是读取了另一条已经提交的事务

MySQL数据库提供的四种隔离级别：

　　① Serializable (串行化)：可避免脏读、不可重复读、幻读的发生。

　　② Repeatable read (可重复读)：可避免脏读、不可重复读的发生。

　　③ Read committed (读已提交)：可避免脏读的发生。

　　④ Read uncommitted (读未提交)：最低级别，任何情况都无法保证。

[spring事物配置](https://blog.csdn.net/bao19901210/article/details/41724355)
事务隔离级别

**隔离级别是指若干个并发的事务之间的隔离程度**。TransactionDefinition 接口中定义了五个表示隔离级别的常量：

> - TransactionDefinition.ISOLATION_DEFAULT：这是默认值，表示使用底层数据库的默认隔离级别。对大部分数据库而言，通常这值就是TransactionDefinition.ISOLATION_READ_COMMITTED。
> - TransactionDefinition.ISOLATION_READ_UNCOMMITTED：该隔离级别表示一个事务可以读取另一个事务修改但还没有提交的数据。该级别不能防止脏读，不可重复读和幻读，因此很少使用该隔离级别。比如PostgreSQL实际上并没有此级别。
> - TransactionDefinition.ISOLATION_READ_COMMITTED：该隔离级别表示一个事务只能读取另一个事务已经提交的数据。该级别可以防止脏读，这也是大多数情况下的推荐值。
> - TransactionDefinition.ISOLATION_REPEATABLE_READ：该隔离级别表示一个事务在整个过程中可以多次重复执行某个查询，并且每次返回的记录都相同。该级别可以防止脏读和不可重复读。
> - TransactionDefinition.ISOLATION_SERIALIZABLE：所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。
--------------------- 
事务传播行为

所谓事务的传播行为是指，**如果在开始当前事务之前，一个事务上下文已经存在，此时有若干选项可以指定一个事务性方法的执行行为。** 在TransactionDefinition定义中包括了如下几个表示传播行为的常量：

> - TransactionDefinition.PROPAGATION_REQUIRED：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。这是默认值。
> - TransactionDefinition.PROPAGATION_REQUIRES_NEW：创建一个新的事务，如果当前存在事务，则把当前事务挂起。
> - TransactionDefinition.PROPAGATION_SUPPORTS：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
> - TransactionDefinition.PROPAGATION_NOT_SUPPORTED：以非事务方式运行，如果当前存在事务，则把当前事务挂起。
> - TransactionDefinition.PROPAGATION_NEVER：以非事务方式运行，如果当前存在事务，则抛出异常。
> - TransactionDefinition.PROPAGATION_MANDATORY：如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
> - TransactionDefinition.PROPAGATION_NESTED：如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。
--------------------- 


数据库范式

第一范式：所有字段值都是不可分解的原子值（属性不可分）
 
第二范式：也就是说在一个数据库表中，一个表中只能保存一种数据，不可以把多种数据保存在同一张数据库表中。（符合1NF，并且，非主属性完全依赖于码。）

第三范式：每一列数据都和主键直接相关，而不能间接相关。（符合2NF，并且，消除传递依赖）

