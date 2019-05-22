---
title: HashMap归纳总结
copyright: true
abbrlink: '860e4400'
date: 2017-05-12 21:49:01
categories:
  - NoteBook
tags: 
 - JAVA
 - 基础
 - 集合
top:
---
## 总结ArrayList和LinkedList
HashMap
![hashMap](hashMap.jpg)

<!-- more -->

---
## HashMap
1. **了解HashMap吗？**

>  - HashMap是一种键值对（Key-Value）形式的存储结构。
>  - key和value都允许为null。
>  - 当key重复的时候会覆盖，value允许重复。
>  - 是无序的，不会按照put的顺序排序。
>  - 是非线程安全的。
>  - HashMap的Entry是一个单向链表
>  - 默认初始长度是16

![base](base.jpg)

2. **知道HashMap的工作原理吗？**
 
> - 内部是一个数组，数组元素Node是实现了Map.Entry(hash,key,value,next)，next非null的时候指向定位相同的另一个Entry。
> - 使用put()传递键和值，先对键调用hashCode()方法，通过hashCode确定bucket位置存储Entry对象。当发生碰撞的时候，使用**散列法**处理碰撞节点，将旧的Entry的引用赋值给新的Entry的next上，就是一个链表，冲突的节点从**链表头部插入**，这样插入新的Entry就不需要遍历链表。
> - 通过get()获取对象。

3. **当两个对象的hashCode相同的时候，怎么获取值对象？**

> - get方法先比较hashCode值，如果hashCode相等，就是用equal()方法比较。
> - == 号与equals()方法的区别:== 基本数据类型比较的是值，对象比较的是对象的地址值。
>  equals()继承自Object类。在所有没有重写equals()方法的类中，equals()内部是==，也是比较的地址值。然而，Java提供的所有类中，绝大多数类都重写了equals()方法，重写后的equals()方法一般都是比较两个对象的值


4. **如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？**

> 默认的负载因子大小为**0.75**，初始容量是16，,也就是说，当一个map填满了**75%**的bucket时候，就会发生resize。简单的说就是把bucket扩充为2倍，之后重新计算index，把节点再放到新的bucket中。

5. **重新调整HashMap大小存在什么问题？**

> 在多线程的情况下，整个HashMap中的元素都需要重新算一遍。rehash，成本非常大。
> 链表中节点的转移可能会出现死循环的情况。

6. **HashMap与HashTable的区别：**

> - HashTable不接受为null的键值(key)和值(value)
> - Hashtable是线程安全的也是synchronized在单线程环境下它比HashMap要慢。

7. **HashMap同步？**

> Map m = Collections.synchronizeMap(hashMap);

8. 高并发下的HashMap

Rehash是HashMap在扩容时的一个步骤，HashMap的容量是有限的。使得HashMap达到一定饱和度时，Key映射位置发生冲突的几率会逐渐提高。
这时候，HashMap需要扩展它的长度，也就是进行Resize。

Resize的条件是:HashMap.Size >= Capacity * LoadFactor。

Resize要经过两个过程：扩容和ReHash
1. 扩容：创建一个新的Entry空数组，长度是原数组的2倍。
 
2. ReHash：遍历原Entry数组，把所有的Entry重新Hash到新数组。为什么要重新Hash呢？因为长度扩大以后，Hash的规则也随之改变。

Hash公式：index = HashCode（Key） & （Length - 1）



![ConcurrentHashMap](ConcurrentHashMap.png)
![HashMap](HashMap.jpg)
---
## ConcurrentHashMap

> 需要线程安全，那么就使用ConcurrentHashMap。


HashTable是使用synchronized来锁住整张Hash表来实现线程安全。

一个 ConcurrentHashMap 由一个个 Segment 组成，Segment代表”部分“或”一段“的意思，所以很多地方都会将其描述为分段锁。
Segment 内部是由 数组+链表 组成的。

![ConcurrentHashMap](ConcurrentHashMap.jpg)



> （1）ArrayList以数组形式实现，顺序插入、查找快，插入、删除较慢
>    
> （2）LinkedList以链表形式实现，顺序插入、查找较慢，插入、删除方便

https://mp.weixin.qq.com/s/SyKckwLfV2ypJOzTFA7R_g

https://mp.weixin.qq.com/s/__ZnkPAF6ucUqN8CVSVQeA

https://juejin.im/post/5a224e1551882535c56cb940
