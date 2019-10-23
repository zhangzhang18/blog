---
title: Redis归纳
copyright: true
abbrlink: b46adbe6
date: 2019-08-18 16:31:47
categories:
  - NoteBook
tags: 
  - Redis
top:
---

## Redis 
一个基于内存的高性能的key-value数据库。

> 数据结构：String字符串，Hash字典，List列表，Set集合，SortedSet有序集合。

![Redis](Redis.png)

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
> - 使用多路 I/O 复用模型（事件轮询）-NIO
> 
> 采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗；
>
>这里“多路”指的是多个**网络连接**，“复用”指的是**复用同一个线程**。采用多路 I/O 复用技术可以让单个线程高效的处理多个连接请求（尽量减少网络 IO 的时间消耗），且 Redis 在内存中操作数据的速度非常快，也就是说内存内的操作不会成为影响Redis性能的瓶颈，主要由以上几点造就了 Redis 具有很高的吞吐量。

4.**过期策略**

```
1.定期删除+惰性删除。
所谓定期删除，指的是 redis 默认是每隔 100ms 就随机抽取一些设置了过期时间的 key，检查其是否过期，如果过期就删除。
惰性删除，获取 key 的时候，如果此时 key 已经过期，就删除，不会返回任何东西。
2.走内存淘汰机制---在内存不足容纳新的数据的的时候
    a).noeviction :    新写入数据报错
    b).allkeys-lru：   在所有键中，删除最近最少使用的键
    c).allkeys-random：在所有键中，随机删除某个键
    d).volatile-lru：  在设置过期时间的键中，删除最近最少使用的键
    e).volatile-random:在设置过期时间的键中，随机删除某个键
    f).volatile-ttl：  在设置过期时间的键中，删除最早过期的键
```

5.**主从同步**

最终一致性

增量同步、快照同步

6.**哨兵模式**

当故障发生时可以自动进行主从切换，程序不用重启的方案。

Redis Sentinel 哨兵，可以看成一个zk集群，是集群高可用的心脏，一般由3-5个节点组成，即使个别节点挂了，集群还可以正常运转。

哨兵负责监控主从节点的健康，主节点挂掉时，自动选择一个最优的节点切换成主节点。客户端连接的时候，先连接Sentinel，通过Sentinel查询主节点地址，在连接主节点进行数据交互。当主节点挂掉，会将最新的主节点告诉客户端。

客户端连接时判断主节点是否变更，如果变更重新用新地址建立连接。

如果是主节点切换成从节点，所有修改指令会抛出ReadOnlyError。如果没有修改性指令，虽然不会得到切换，但是数据不会被破坏，所以不切换也没关系。

7.**Redis锁的使用。**

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
