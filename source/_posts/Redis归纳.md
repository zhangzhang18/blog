---
title: Redis归纳
copyright: true
abbrlink: b46adbe6
date: 2018-08-18 16:31:47
categories:
  - NoteBook
tags: 
  - Redis
top:
---

## Redis 
一个基于内存的高性能的key-value数据库。

> 数据结构：String字符串，Hash字典，List列表，Set集合，SortedSet有序集合。
<!-- more -->
1. **使用Redis有哪些好处？**

> - (1) 速度快，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)
> - (2) 支持丰富数据类型，支持string，list，set，sorted set，hash
> - (3) 支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行
> - (4) 丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除

2.**为什么redis需要把所有数据放到内存中?**
> 　Redis为了达到最快的读写速度将数据都读到内存中，并通过异步的方式将数据写入磁盘。所以redis具有快速和数据持久化的特征。如果不将数据放在内存中，磁盘I/O速度为严重影响redis的性能。在内存越来越便宜的今天，redis将会越来越受欢迎。
> 　　　如果设置了最大使用的内存，则数据已有记录数达到内存限值后不能继续插入新值。

3.**Redis是单进程单线程的为什么也那么快？**

> 　Redis快的主要原因是：
> - 完全基于内存
> - 数据结构简单，对数据操作也简单
> - 使用多路 I/O 复用模型
> 
> 采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗；
>
>这里“多路”指的是多个网络连接，“复用”指的是复用同一个线程。采用多路 I/O 复用技术可以让单个线程高效的处理多个连接请求（尽量减少网络 IO 的时间消耗），且 Redis 在内存中操作数据的速度非常快，也就是说内存内的操作不会成为影响Redis性能的瓶颈，主要由以上几点造就了 Redis 具有很高的吞吐量。

4.**Redis锁的使用。**

```
private static Redisson redisson = RedissonManager.getRedisson();

    private static final String LOCK_FLAG = "mylock_";

    /**
     * 根据name对进行上锁操作，redissonLock 阻塞的，采用的机制发布/订阅
     * @param key
     */
    public static void lock(String key){
        String lockKey = LOCK_FLAG + key;
        RLock lock = redisson.getLock(lockKey);
        //lock提供带timeout参数，timeout结束强制解锁，防止死锁 ：1分钟
        lock.lock(1, TimeUnit.MINUTES);
        logger.info("lock key:{}",lockKey);
    }

    /**
     * 根据name对进行解锁操作
     * @param key
     */
    public static void unlock(String key){
        String lockKey = LOCK_FLAG + key;
        RLock lock = redisson.getLock(lockKey);
        //如果锁被当前线程持有，则释放
        if(lock.isHeldByCurrentThread()){
            lock.unlock();
            logger.info("unlock key:{}",lockKey);
        }
    }
```
