---
title: Java类加载
copyright: true
abbrlink: c664e20c
date: 2017-07-16 16:49:44
categories:
  - NoteBook
tags:
  - JAVA
  - JVM
top: 
---

![load](load.jpg)

Java虚拟机加载类的全过程包括，加载，验证，准备，解析和初始化。
<!-- more -->

- 加载：根据路径找到对应的class文件，导入
- 验证：检验加载的class文件正确性
- 准备：准备阶段是正式为类变量分配内存并设置类变量初始值的阶段，这些变量所使用的内存都将在方法区中进行分配。
- 解析：给符号引用转换为直接引用(解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用点限定符7类符号引用进行。)
- 初始化：对静态变量和静态代码块执行初始化工作



何时触发初始化

1. 为一个类型创建一个新的对象实例时（比如new、反射、序列化）
2. 调用一个类型的静态方法时（即在字节码中执行invokestatic指令）
3. 调用一个类型或接口的静态字段，或者对这些静态字段执行赋值操作时（即在字节码中，执行getstatic或者putstatic指令），不过用final修饰的静态字段除外，它被初始化为一个编译时常量表达式
4. 调用JavaAPI中的反射方法时（比如调用java.lang.Class中的方法，或者java.lang.reflect包中其他类的方法）
5. 初始化一个类的派生类时（Java虚拟机规范明确要求初始化一个类时，它的超类必须提前完成初始化操作，接口例外）
6. JVM启动包含main方法的启动类时。

类加载器：

- Bootstrap ClassLoader(启动类加载器):负责加载系统类(jre/lib/rt.jar)
- Extension ClassLoader(扩展类加载器):负责加载扩展类(jre/lib/ext/*.jar)
- Applicaiton ClassLoader(应用程序类加载器):用于加载自己定义编写的类(classpath指定目录或jar中类)
- User ClassLoader （用户自己实现的加载器）

“双亲委派模型”简单来说就是：

1. 先检查需要加载的类是否已经被加载，如果没有被加载，则委托父加载器加载，父类继续检查，尝试请父类加载，这个过程是从下-------> 上;
2. 如果走到顶层发现类没有被加载过，那么会从顶层开始往下逐层尝试加载，这个过程是从上 ------> 下;

![parents](parents.jpg)
![parents1](parents1.jpg)

JAVA热部署实现
首先谈一下何为热部署（hotswap），热部署是在不重启 Java 虚拟机的前提下，能自动侦测到 class 文件的变化，更新运行时 class 的行为。Java 类是通过 Java 虚拟机加载的，某个类的 class 文件在被 classloader 加载后，会生成对应的 Class 对象，之后就可以创建该类的实例。默认的虚拟机行为只会在启动时加载类，如果后期有一个类需要更新的话，单纯替换编译的 class 文件，Java 虚拟机是不会更新正在运行的 class。如果要实现热部署，最根本的方式是修改虚拟机的源代码，改变 classloader 的加载行为，使虚拟机能监听 class 文件的更新，重新加载 class 文件，这样的行为破坏性很大，为后续的 JVM 升级埋下了一个大坑。

另一种友好的方法是创建自己的 classloader 来加载需要监听的 class，这样就能控制类加载的时机，从而实现热部署。 

 热部署步骤：

1、销毁自定义classloader(被该加载器加载的class也会自动卸载)；

2、更新class

3、使用新的ClassLoader去加载class 

JVM中的Class只有满足以下三个条件，才能被GC回收，也就是该Class被卸载（unload）：

   - 该类所有的实例都已经被GC，也就是JVM中不存在该Class的任何实例。
   - 加载该类的ClassLoader已经被GC。
   - 该类的java.lang.Class 对象没有在任何地方被引用，如不能在任何地方通过反射访问该类的方法
   
   
   
   
https://juejin.im/post/57c66f386be3ff005851de05
https://juejin.im/post/5a1fad585188252ae93ab953
https://www.cnblogs.com/aspirant/p/7200523.html