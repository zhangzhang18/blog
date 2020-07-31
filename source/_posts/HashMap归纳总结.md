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
>
> 多线程会导致HashMap的Entry链表形成环形，可能会出现死循环的情况。

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

> 需要线程安全，那么就使用ConcurrentHashMap。分段锁+CAS


HashTable是使用synchronized来锁住整张Hash表来实现线程安全。

JDK1.7:一个 ConcurrentHashMap 由一个个 Segment 组成，Segment代表”部分“或”一段“的意思，所以很多地方都会将其描述为分段锁。
Segment 内部是由 数组+链表 组成的。
(读操作不加锁，由于HashEntry的value变量是 volatile的，也能保证读取到最新的值。)
![ConcurrentHashMap](ConcurrentHashMap.jpg)

JDK1.8: HashMap类似的数组+链表+红黑树的方式实现，而加锁则采用CAS和synchronized实现。 

**CAS：**内存中的值V ，旧值A，要修改的值B

```
if(A==V){
	V=B;
}
```

 修改失败重新获取内存地址V的当前值，并重新计算想要修改的值。这个重新尝试的过程被称为**自旋**。 

 从思想上来说，synchronized属于悲观锁，悲观的认为程序中的并发情况严重，所以严防死守，**CAS属于乐观锁**，乐观地认为程序中的并发情况不那么严重，所以让线程不断去重试更新。 

 链表结构中的数据大于8，则将数据结构升级为TreeBin类型的**红黑树结构** 

```
  final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
         //1. 计算key的hash值
        int hash = spread(key.hashCode());
        int binCount = 0;
        //一个死循环
        for (Node<K,V>[] tab = table;;) {
            Node<K,V> f; int n, i, fh;
            if (tab == null || (n = tab.length) == 0)
          	    //2. 如果当前table还没有初始化先调用initTable方法将tab进行初始化
                tab = initTable();
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            	//3. tab[i]为null，则直接使用CAS原子操作将值插入即可
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
            }
            else if ((fh = f.hash) == MOVED)//Node头结点的hash值为MOVED(-1)，则表示需要扩容，此时调用helpTransfer()方法进行扩容；
          	    //4. 当前正在扩容
                tab = helpTransfer(tab, f);
            else {
                V oldVal = null;
                synchronized (f) {//对这个Node链表或红黑树通过synchronized加锁
                    if (tabAt(tab, i) == f) {
                         //5. 当前为链表，在链表中插入新的键值对
                        if (fh >= 0) {
                            binCount = 1;
                            for (Node<K,V> e = f;; ++binCount) {
                                K ek;
                                if (e.hash == hash &&
                                    ((ek = e.key) == key ||
                                     (ek != null && key.equals(ek)))) {
                                    oldVal = e.val;
                                    if (!onlyIfAbsent)
                                        e.val = value;
                                    break;
                                }
                                Node<K,V> pred = e;
                                if ((e = e.next) == null) {
                                    pred.next = new Node<K,V>(hash, key,
                                                              value, null);
                                    break;
                                }
                            }
                        }
                        // 6.当前为红黑树，将新的键值对插入到红黑树中
                        else if (f instanceof TreeBin) {
                            Node<K,V> p;
                            binCount = 2;
                            if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                           value)) != null) {
                                oldVal = p.val;
                                if (!onlyIfAbsent)
                                    p.val = value;
                            }
                        }
                    }
                }
                // 7.插入完键值对后再根据实际大小看是否需要转换成红黑树
                if (binCount != 0) {
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
                    if (oldVal != null)
                        return oldVal;
                    break;
                }
            }
        }
         //8.对当前容量大小进行检查，如果超过了临界值（实际大小*加载因子）就需要扩容 
        addCount(1L, binCount);
        return null;
    }

```

 helpTransfer() 中 sizeCtl

**ABA问题** 

​	[CAS理解](https://blog.csdn.net/qq_32998153/article/details/79529704)

 怎么解决呢？加个版本号就可以了。 





> （1）ArrayList以数组形式实现，顺序插入、查找快，插入、删除较慢
>    
> （2）LinkedList以链表形式实现，顺序插入、查找较慢，插入、删除方便

https://mp.weixin.qq.com/s/SyKckwLfV2ypJOzTFA7R_g

https://mp.weixin.qq.com/s/__ZnkPAF6ucUqN8CVSVQeA

https://juejin.im/post/5a224e1551882535c56cb940

https://www.jianshu.com/p/5dbaa6707017