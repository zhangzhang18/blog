---
title: Spring-IOC
copyright: true
abbrlink: 6c92115f
date: 2018-01-16 17:27:23
categories:
  - NoteBook
tags: 
 - Spring
top:
---

# 1.Spring框架简介

Spring框架是基于Java平台的，它为开发Java应用提供了全方位的基础设施支持，并且它很好地处理了这些基础设施，所以你只需要关注你的应用本身即可。

Spring可以使用POJO（普通的Java对象，plain old java objects）创建应用，并且可以将企业服务非侵入式地应用到POJO。这项功能适用于Java SE编程模型以及全部或部分的Java EE。
<!-- more -->
# 2.Spring模块结构
![core](core.jpg)
## 2.1 IOC 控制反转
> IoC也称为依赖注入（DI）
> 是为了解决对象之间的**耦合度**过高的问题

钟表拥有多个独立的齿轮，这些齿轮相互啮合在一起，齿轮相互啮合在一起，协同工作，共同完成某项任务。如果有一个齿轮出了问题，就可能会影响到整个齿轮组的正常运转。与软件系统中对象之间的耦合关系非常相似

软件专家Michael Mattson提出了IOC理论，用来实现对象之间的**“解耦”**。

借助于**“第三方”**实现具有依赖关系的对象之间的解耦，这个“第三方”也就是**IOC容器**。

**哪些方面的控制被反转了呢**
 - 获得依赖对象的过程被反转了

所谓依赖注入，就是由IOC容器在运行期间，动态地将某种依赖关系注入到对象之中。

所以，依赖注入(DI)和控制反转(IOC)是从不同的角度的描述的同一件事情，就是指通过引入IOC容器，利用依赖关系注入的方式，实现对象之间的解耦。

把依赖注入应用到软件系统中，再来描述一下这个过程：
 - 对象A依赖于对象B,当对象 A需要用到对象B的时候，IOC容器就会立即创建一个对象B送给对象A。
 - IOC容器就是一个对象制造工厂，你需要什么，它会给你送去，你直接使用就行了，而再也不用去关心你所用的东西是如何制成的，也不用关心最后是怎么被销毁的，这一切全部由IOC容器包办。

## IOC容器设计
> 是基于BeanFactory和ApplicationContext两个接口。  
  ApplicationContext是BeanFactory的子接口之一。
  BeanFactory是IOC容器定义的最底层接口，ApplicationContext是其中高级接口之一，并且做了许多的拓展。

```java
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
BeanFactory factory =  context;
```
#### BeanFactory和ApplicationContext有什么区别
BeanFactory可以理解为含有Bean集合的工厂类。  
BeanFactory 包含了Bean的定义，以便在接收到客户端请求时将对应的Bean实例化。  
BeanFactory还能在实例化对象时生成协作类之间的关系。此举将Bean自身从Bean客户端的配置中解放出来。BeanFactory还包含Bean生命周期的控制，调用客户端的初始化方法（Initialization Method）和销毁方法（Destruction Method）。 

从表面上看，ApplicationContext如同BeanFactory一样具有Bean定义、Bean关联关系的设置及根据请求分发Bean的功能。

但ApplicationContext在此基础上还提供了其他功能。 

（1）提供了支持国际化的文本消息。  
（2）统一的资源文件读取方式。  
（3）已在监听器中注册的Bean的事件。  
以下是三种较常见的 ApplicationContext 实现方式。 
（1）ClassPathXmlApplicationContext：从ClassPath的XML配置文件中读取上下文，并生成上下文定义。应用程序上下文从程序环境变量中取得。  

```
ApplicationContext context = new ClassPathXmlApplicationContext(“application.xml”); 
```
（2）FileSystemXmlApplicationContext ：由文件系统中的XML配置文件读取上下文。
```
ApplicationContext context = new FileSystemXmlApplicationContext(“application.xml”); 
```
（3）XmlWebApplicationContext：由Web应用的XML文件读取上下文。

### 2.1.1 相关Java基础知识

IOC流程
![ioc](ioc.png)

**反射**：Java语言允许通过程序化的方式间接对class操作，Class文件由类加载器转载后，JVM形成一份描述Class结构的元信息，通过元信息对象可以获取到构造函数，属性和方法等。
      通过这个元信息对象间接的调用Class对象的功能。

几个重要的反射类：
> ClassLoader，Class，Constructor，和Method 

ClassLoader：类装载器，把一个类装入到JVM。
   需要经过：
    - 1.装载
    - 2.链接 校验 准备  解析
    - 3.初始化
### 2.1.2 IOC容器中转配Bean
1.    
 ```xml
 <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/views/" />
           <property name="suffix" value=".jsp" />
</bean>
 ```
2. **Spring 支持两种依赖注入方式：属性注入和构造函数注入，还支持工厂方法注入方式。**
```java
public class SpringBeanFactory {
    private static BeanFactory beanFactory;

    private static Logger logger = LoggerFactory.getLogger(SpringBeanFactory.class);

    public static BeanFactory getBeanFactory() {
        if (beanFactory == null) {
            synchronized (BeanFactory.class) {
                try {
                    String path = Config.getConfigFolder();
                    if (beanFactory == null) {
                        beanFactory = new FileSystemXmlApplicationContext("/" + path + "aplication-spring-dubbo.xml");
                    }
                } catch (Exception e) {
                    logger.error("初始化SpringDubbo Error", e);
                }
            }
        }
        return beanFactory;
    }
}
```
3. **自动装配 - autowire="自动装配类型"**
   - byName：根据名称自动匹配
   - byType：根据类型自动匹配
   - constructor：与byType类似，它只针对构造函数注入的
   - autodetect：
4. **bean作用域 - scope="作用域"**
   - singleton：在IOC容器中只存在一个实例，以单例的方式存在,默认值
   - prototype：每次从容器调用Bean时，都返回一个新的实例
   - request：每次HTTP请求都会创建一个新的Bean，只适用于WebApplicationContext环境
   - session：同一个HTTP Session共享一个Bean，只适用于WebApplicationContext环境
   - global-session：

5. **基于注解定义Bean**

@component可以替代下面三种注解，为了清晰化，建议使用特定的注解
   - @Repository：DAO层实现类
   - @Service：Service实现类
   - @Controller：Service实现类
扫描包以应用注解的Bean
```xml
<context:component-scan base-package="com.test.company.*" />
```
   Spring使用@Autowired注解实现Bean依赖注入
   @Autowired默认按照byType匹配在容器中查找Bean
   若想希望找不到Bean也不报NoSuchBeanDefinitionException异常，可以使用@Autowired(require=false)
   @Qualifier注解可以限定Bean的名字

```
@Qualifier("userDaop")
private UserDao userDao;
```





https://www.cnblogs.com/wang-meng/p/5597490.html
