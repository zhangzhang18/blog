---
title: synchronized关键字
date: '2017/10/01 10:47:54'
copyright: true
categories:
  - NoteBook
tags:
  - JAVA
  - 基础
  - 多线程
abbrlink: ed378e14
top: 1
---
![synchronized](syn.jpg)
synchronized是一种同步锁
同一时刻只能有一个线程能获取到锁

1. 修饰代码块：同步代码块，作用域是{}里面的代码，作用的对象是调用这个代码块的对象。
2. 修饰方法：同步方法，作用范围是整个方法，作用对象是调用这个方法的对象。
3. 修饰静态的方法：作用范围是整个方法，作用对象是这个类的所有对象。
4. 修饰类：作用范围是synchronized后面括号括起来的部分，作用的对象是这个类的所有对象。
<!-- more -->

---
1.**synchronized 代码块**
```
   public void run() {
      synchronized(obj) {
      //一次只能有一个线程进入
      }
   }
```
synchronized锁住的是括号里的对象，不是代码。
当synchronized锁住一个对象时，别的线程也想拿到这个对象的锁，必须等待这个线程执行完释放锁，才能再次给这个对象加锁。

> example

```java
public class SyncThread implements Runnable {

    private static int count;

    public SyncThread() {
    }

    @Override
    public void run() {
        try {
            String lock=new String();
            synchronized (this) {//1 synchronized (lock) 2//synchronized (SyncThread.class)3
                System.out.println(Thread.currentThread().getName()+":a begin");
                Thread.sleep(500);
                System.out.println(Thread.currentThread().getName()+":a   end");
            }
        } catch (InterruptedException e) {
            e.printStackTrace();

        }
    }

}
```


```
//1,2,3
    public static void main(String[] arg){

        SyncThread a = new SyncThread();
        Thread t1 = new Thread(a);
        Thread t2 = new Thread(a);
        t1.start();
        t2.start();
    }

```
```
//3
    public static void main(String[] arg){

        SyncThread a = new SyncThread();
        SyncThread b = new SyncThread();
        Thread t1 = new Thread(a);
        Thread t2 = new Thread(b);
        t1.start();
        t2.start();
    }

```

1,3输出
- t1---:a begin
- t1---:a   end
- t2---:a begin
- t2---:a   end

2输出
- t2---:a begin
- t1---:a begin
- t1---:a   end
- t2---:a   end


1是对类的当前实例加锁
2是对锁特定的实例加锁
3是对该类的所有对象都加了锁，该类所有的对象同一把锁。

1.**synchronized 方法**
```java
   public synchronized void syncAdd() {
      //4
   }
   public static synchronized void syncAdd() {
      //5
   }
```
> example


```java
public class SyncThread {

    public SyncThread() {
    }

    public synchronized void isSyncA() {
        int i = 5;
        while( i-- > 0){
            System.out.println("funA-"+Thread.currentThread().getName() + " : " + i);
            try {
                Thread.sleep(500);
            }
            catch (InterruptedException ie){
            }
        }
    }
    public  synchronized void isSyncB(){
        int i = 5;
        while( i-- > 0){
            System.out.println("funB-"+Thread.currentThread().getName() + " : " + i);
            try {
                Thread.sleep(500);
            }catch (InterruptedException ie){
            }
        }
    }
    public static synchronized void cSync(){
        int i = 5;
        while( i-- > 0) {
            System.out.println("cSync"+Thread.currentThread().getName() + " : " + i);
            try{
                Thread.sleep(500);
            }catch (InterruptedException ie){
            }
        }
    }
}
```
main

```
 public static void main(String[] arg){
 //4
        SyncThread a = new SyncThread();
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                a.isSyncA();
            }
        },"t1");

        Thread t2 =new Thread(new Runnable() {
            @Override
            public void run() {
                a.isSyncB();
            }
        },"t2");

         t1.start();
         t2.start();
    }
```
输出
- funA-t1 : 4
- funA-t1 : 3
- funA-t1 : 2
- funA-t1 : 1
- funA-t1 : 0
- funB-t2 : 4
- funB-t2 : 3
- funB-t2 : 2
- funB-t2 : 1
- funB-t2 : 0

```
 public static void main(String[] arg){
  //5
        SyncThread a = new SyncThread();
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                SyncThread.cSync();
            }
        },"t1");

        Thread t2 =new Thread(new Runnable() {
            @Override
            public void run() {
                SyncThread.cSync();
            }
        },"t2");

         t1.start();
         t2.start();
    }
```
- cSynct1 : 4
- cSynct1 : 3
- cSynct1 : 2
- cSynct1 : 1
- cSynct1 : 0
- cSynct2 : 4
- cSynct2 : 3
- cSynct2 : 2
- cSynct2 : 1
- cSynct2 : 0

4是对象锁
3,5得到的锁是类的锁

4是防止多线程同时访问这个对象的synchronized方法，（如果这个对象有多个synchronized方法，只要有一个线程访问了一个synchronized方法，其他的线程不能访问这个对象的任一synchronized方法），不同对象的synchronized方法互不影响。

5是防止多线程中不同实例对象同时访问方法，它对类的所有实例起作用。


---

synchronized的实现原理 

1,synchronized代码块 monitorenter //进入同步方法 monitorexit //退出同步方法 2,synchronized方法 ACC_SYNCHRONIZED指明该方法为同步方法

[参考](https://www.cnblogs.com/lixuwu/p/5676143.html)


