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
**工作流程**
    
    第一步：provider向注册中心注册服务
    第二部：consumer从注册中心订阅服务，注册中心会通知consumer注册好的服务
    第三部：consumer调用provider
    第四步：provider和consumer都异步通知监控中心

**调用关系说明：**

	* 服务容器负责启动，加载，运行服务提供者。
	* 服务提供者在启动时，向注册中心注册自己提供的服务。
	* 服务消费者在启动时，向注册中心订阅自己所需的服务。
	* 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
	* 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
	* 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。
**Dubbo 负载均衡策略**

    默认情况下，dubbo 是 random load balance 随机调用实现负载均衡，可以对 provider 不同实例设置不同的权重，会按照权重来负载均衡，权重越大分配流量越高，一般就用这个默认的就可以了。
    leastactive loadbalance
    这个就是自动感知一下，如果某个机器性能越差，那么接收的请求越少，越不活跃，此时就会给不活跃的性能差的机器更少的请求。
    consistanthash loadbalance
    一致性 Hash 算法，相同参数的请求一定分发到一个 provider 上去，provider 挂掉的时候，会基于虚拟节点均匀分配剩余的流量，抖动不会太大。如果你需要的不是随机负载均衡，是要一类请求都到一个节点，那就走这个一致性 Hash 策略。
**Dubbo 集群容错策略**

    failover cluster 模式
    失败自动切换，自动重试其他机器，默认就是这个，常见于读操作。（失败重试其它机器）
    
    failfast cluster模式
    一次调用失败就立即失败，常见于写操作。（调用失败就立即失败）
    
    failsafe cluster 模式 
    出现异常时忽略掉，常用于不重要的接口调用，比如记录日志。
    
    failback cluster 模式
    失败了后台自动记录请求，然后定时重发，比较适合于写消息队列这种。
    
    forking cluster 模式
    并行调用多个 provider，只要一个成功就立即返回。
    
    broadcacst cluster
    逐个调用所有的 provider。

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
注册中心挂了还可以继续通信：因为刚开始初始化的时候，消费者会将服务提供者的地址拉取到本地。