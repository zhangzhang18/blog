---
title: Spring-AOP
copyright: true
abbrlink: 97375e6e
date: 2018-01-20 17:27:23
categories:
  - NoteBook
tags: 
 - Spring
top:
---
# 3.AOP面向切面编程
> OOP面向对象编程的基本单位是**类**，AOP的基本单位是**方法**
> 适用于具有横切逻辑的应用场景，例如性能检测、范文控制、事物管理、及日志管理

AOP希望将分散在各个业务逻辑代码中的相同代码通过横向切割的方式抽取到一个独立独立的模块中。
<!-- more -->
## 3.1概念和术语
1. Aspect(切面):切面通常是指一个类，是通知和切入点的结合，@Aspect类注解。
2. Join point(连接点):程序执行的某个特定位置，例如类初始化前，类初始化后，方法执行前，方法执行后，方法抛出异常时等，Spring只支持方法级别的连接点，即方法执行前，方法执行后，方法抛出异常时，
3. Advice(增强):增强是织入到目标类连接点上的一段程序代码
4. Pointcut(切点):每个程序类都拥有多个连接点，如一个拥有两个方法的类，这两个方法都是连接点，即连接点是程序类中客观存在的事物 @Pointcut("")
5. Introduction(引介):允许向现有的类添加新方法或属性
6. Target object(目标对象):增强逻辑的织入目标类
7. AOP proxy(代理):一个类被AOP织入增强后，就产出了一个结果类，它是融合了原类和增强逻辑的代理类
8. Weaving(织入):织入是将增强添加对目标类具体连接点上的过程
AOP有三种织入的方式：
    - a、编译期织入，这要求使用特殊的Java编译器。
    - b、类装载期织入，这要求使用特殊的类装载器ClassLoader。
    - c、动态代理织入，在运行期为目标类添加增强生成子类的方式。
    Spring采用动态代理织入，而AspectJ采用编译期织入和类装载期织入。


### 3.1.1实现
```xml
<beans>
    <aop:aspectj-autoproxy  poxy-target-class="true"/>
    <!-- 声明自动为spring容器中那些配置@aspectJ切面的bean创建代理，织入切面。-->
    <!--表示使用CGLib动态代理技术织入增强。不过即使proxy-target-class设置为false，如果目标类没有声明接口，则spring将自动使用CGLib动态代理。-->
    <bean id = "testAspect" class="com.test.company.aop.TestAspect" />
</beans>
```
```java
//切面就是切点和通知的组合体
@Aspect
public class TestAspect {
    /**
    * 切点
    */
    @Pointcut("execution(*com.test.company.service.Impl.TestImpl.insert(..))")//Pointcut 使用pointcut定义切点
     private void insertPointcut(){ }
    /**
      * 环绕通知
      * @param pjp
      */
    @Around("insertPointcut()")
     public Object insert(ProceedingJoinPoint pjp) throws Throwable{}
     /**
      * 前置通知
      */
    @Before("execution(*com.test.company.service.Impl.TestImpl.insert(..))")//execution(*insert(..)) 切点表达式“execution”为关键字，“*insert(..)”为操作参数
     private void Before(){ }
   /**
     * 后置通知
     * returnVal,切点方法执行后的返回值
     */    
    @AfterReturning(value="execution(*com.test.company.service.Impl.TestImpl.insert(..))",returning = "returnVal")
     private void AfterReturning(Object returnVal){ }        
}
```
通知有5种类型如下：

     before 目标方法执行前执行，前置通知
     after 目标方法执行后执行，后置通知
     after returning 目标方法返回时执行 ，后置返回通知
     after throwing 目标方法抛出异常时执行 异常通知
     around 在目标函数执行中执行，可控制目标函数是否执行，环绕通知

### 3.1.2 相关Java基础知识
1. 代理模式
> 为某对象提供一个代理，从而通过代理来访问这个对象。

代理模式有三种角色组成:

    抽象角色(卖票)：接口
    代理角色(车票代售点)：Proxy
    真实角色(火车站)：实现

动态代理是动态的生成具体的委托类的代理类实现对象。与静态代理不同，动态代理并不需要为各个委托类逐一实现代理类。只需要为一类代理行为写一个具体实现类就行了。

1. JDK动态代理 
> jdk动态代理是由java内部的反射机制来实现的

主要涉及java.lang.reflect 包中：Proxy和InvocationHandler

使用动态代理需要定义一个位于代理类与委托类之间的中介类,中介类需要实现InvocationHandler定义的横切逻辑，通过反射机制调用目标类的方法，动态的将横切逻辑和业务逻辑编织在一起，InvocationHandler接口只定义了一个invoke方法
通过"Proxy"类提供的一个newProxyInstance方法用来创建一个对象的代理对象

JDK动态代理：是JDK自带的功能;是java.lang.reflect.*包提供的方式，它必须借助一个接口才能产生代理对象。
    实现代理逻辑类必须去实现InvocationHandler类
    1. 建立代理对象和真实对象的关系（blind()）
    2. 实现代理逻辑方法（invoke()）
    
CGLIB：是第三方提供的技术;不需要提供接口，只要有一个非抽象类就可以实现。

二者都是通过getProxy()生成代理对象。
Spring常用这两种，Mybatis还使用Javassist。
拦截器是JDK动态代理。

https://zhuanlan.zhihu.com/p/25522841










