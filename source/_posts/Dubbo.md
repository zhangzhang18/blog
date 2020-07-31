---
title: Dubbo
copyright: true
abbrlink: f6253398
date: 2018-10-15 11:35:25
categories:
  - NoteBook
tags:
  - 分布式
  - Dubbo
top:
---
Dubbo 工作原理：服务注册、注册中心、消费者、代理通信、负载均衡；

[RPC原理](https://www.cnblogs.com/LBSer/p/4853234.html)

[Dubbo](http://dubbo.apache.org/zh-cn/docs/user/quick-start.html)

[Dubbo集群容错](https://www.cnblogs.com/hd3013779515/p/6896942.html)

一般的mvc项目 包含 Controller、Servicei、ServiceImpl、dao三层
使用doubbo我们可以把项目拆分：
Controller 作为 “消费着” 一个项目
ServiceImpl +dao 作为 “提供者” 一个项目

Service “接口” 可以作为一个项目

https://github.com/doocs/advanced-java/blob/master/docs/distributed-system/dubbo-operating-principle.md

![dubbo-operating-principle](dubbo-operating-principle.png)

Dubbo缺省协议采用单一长连接和NIO异步通讯，适合于小数据量大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况。

**节点角色说明：**

	*  Provider: 暴露服务的服务提供方。
	*  Consumer: 调用远程服务的服务消费方。
	*  Registry: 服务注册与发现的注册中心。
	*  Monitor: 统计服务的调用次调和调用时间的监控中心。
	*  Container: 服务运行容器。

<!-- more -->

![Dubbo](dubbo.jpg)
###　**工作流程**

    第一步：provider向注册中心注册服务
    第二部：consumer从注册中心订阅服务，注册中心会通知consumer注册好的服务
    第三部：consumer调用provider
    第四步：provider和consumer都异步通知监控中心

### **调用关系说明：**

	* 服务容器负责启动，加载，运行服务提供者。
	* 服务提供者在启动时，向注册中心注册自己提供的服务。
	* 服务消费者在启动时，向注册中心订阅自己所需的服务。
	* 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
	* 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
	* 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。
### **Dubbo 负载均衡策略**
  1.random load balance：**随机**调用实现负载均衡，可以对 provider不同实例**设置不同的权重**，会按照权重来负载均衡，权重越大分配流量越高。

  2.roundrobin loadbalance:**均匀**的打到各个机器，可以将性能差的机器权重小一点。

3.leastactive loadbalance：**自动感知**，如果某个机器性能越差，那么接收的请求越少，越不活跃，此时就会给**不活跃的性能差的机器更少的请求**。

 4.consistanthash loadbalance：一致性 Hash 算法，**相同参数的请求**一定分发到一个 provider 上去，provider 挂掉的时候，会基于虚拟节点均匀分配剩余的流量，抖动不会太大。如果需要的不是随机负载均衡，是要一类请求都到一个节点，那就走这个一致性 Hash 策略。

### **Dubbo 集群容错策略**
  1.failover cluster ：**失败自动切换**，自动重试其他机器，默认就是这个，常见于**读操作**。（失败重试其它机器）``<dubbo:service retries="2" />``

2.failfast cluster ：一次调用失败就立即失败，常见于**幂等性写操作**。（调用失败就立即失败）
  3.failsafe cluster ：出现异常时忽略掉，常用于不重要的接口调用，比如**记录日志**。

  4.failback cluster ：失败了后台自动记录请求，然后定时重发，比较适合于**写消息队列**这种。

  5.forking  cluster ：并行调用多个 provider，只要一个成功就立即返回。常用于**实时性要求比较高的读操作**，但是会浪费资源。
  6.broadcast cluster：逐个调用所有的 provider。通常用于**通知**所有提供者更新缓存或日志等本地资源信息。

### 序列化与反序列化
        序列化：把对象转换为字节序列的过程称为对象的序列化。 
        反序列化：把字节序列恢复为对象的过程称为对象的反序列化。  
序列化对于远程调用的**响应速度、吞吐量、网络带宽**消耗等同样也起着至关重要的作用，是我们提升分布式系统性能的最关键因素之一。 
        
        dubbo序列化：阿里尚未开发成熟的高效java序列化实现，阿里不建议在生产环境使用它  
        hessian2序列化：hessian是一种跨语言的高效二进制序列化方式。但这里实际不是原生的hessian2序列化，而是阿里修改过的hessian lite，它是dubbo RPC默认启用的序列化方式  
        json序列化：目前有两种实现，一种是采用的阿里的fastjson库，另一种是采用dubbo中自己实现的简单json库，但其实现都不是特别成熟，而且json这种文本序列化性能一般不如上面两种二进制序列化。  
        java序列化：主要是采用JDK自带的Java序列化实现，性能很不理想。  
在通常情况下，这四种主要序列化方式的性能从上到下依次递减。          
面向生产环境的应用中，建议目前更优先选择Kryo。  
`<dubbo:protocol name="dubbo" serialization="kryo"/>`  
[Dubbo中使用高效的Java序列化](https://blog.csdn.net/moonpure/article/details/53175519)




### 通信协议：
    dubbo：单一长连接，进行NIO异步通信，基于**hessian**序列化协议。（长连接，通俗说就是建立连接之后可以持续发送请求，无需再建立连接）使用场景是传输数据量小（100kb以内），并发量高。
    rmi：java 的二进制序列化，适用于文件传输。
    hessian：hessian序列化
    http：json序列化
    webservice：soap文本序列化

服务治理：1.调用链路自动生成 2.服务访问压力及时长
服务降级：服务A调用服务B，B挂掉了，A重试几次还是不行，降级，走一个备用逻辑返回给用户。
失败重试超时重试：consumer调用provider失败了，可以重试，或者调用超时了也可以重试。

注册中心挂了还可以继续通信：因为刚开始初始化的时候，消费者会将服务提供者的地址拉取到本地。

----
用 Spring 配置声明暴露服务
```xml
<?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
    xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans.xsd        http://code.alibabatech.com/schema/dubbo        http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <!-- 提供方应用信息，用于计算依赖关系 -->
    <dubbo:application name="hello-world-app"  />

    <!-- 使用multicast广播注册中心暴露服务地址 -->
    <dubbo:registry address="multicast://224.5.6.7:1234" />

    <!-- 用dubbo协议在20880端口暴露服务 -->
    <dubbo:protocol name="dubbo" port="20880" />

    <!-- 声明需要暴露的服务接口 -->
    <dubbo:service interface="com.alibaba.dubbo.demo.DemoService" ref="demoService" />

    <!-- 和本地bean一样实现服务 -->
    <bean id="demoService" class="com.alibaba.dubbo.demo.provider.DemoServiceImpl" />

</beans>
```
加载Spring配置Provider.java
```java
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Provider {
    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[]{"http://10.20.160.198/wiki/display/dubbo/provider.xml"});
        context.start();

        System.in.read(); // 按任意键退出
    }
}
```
服务消费者
通过Spring配置引用远程服务
consumer.xml
```xml
<?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

<!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
<dubbo:application name="consumer-of-helloworld-app" />

<!-- 使用multicast广播注册中心暴露发现服务地址 -->
<dubbo:registry address="multicast://224.5.6.7:1234" />

<!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
<dubbo:reference id="demoService" interface="com.alibaba.dubbo.demo.DemoService" /></beans>
```
加载Spring配置，并调用远程服务
Consumer.java
```java
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.alibaba.dubbo.demo.DemoService;

public class Consumer {

    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"http://10.20.160.198/wiki/display/dubbo/consumer.xml"});
        context.start();

        DemoService demoService = (DemoService)context.getBean("demoService"); // 获取远程服务代理
        String hello = demoService.sayHello("world"); // 执行远程方法

        System.out.println( hello ); // 显示调用结果
    }

}
```
