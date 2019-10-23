---
title: Spring-Cloud
copyright: true
abbrlink: 97375e6f
date: 2019-01-24 16:21:58
categories:
  - NoteBook
tags: 
 - Spring
top:
---
# Spring Cloud简介
> Spring Cloud是一个基于Spring Boot实现的云应用开发工具，它为基于JVM的云应用开发中涉及的配置管理、服务发现、断路器、智能路由、微代理、控制总线、全局锁、决策竞选、分布式会话和集群状态管理等操作提供了一种简单的开发方式。

## 微服务
> 简单的说，微服务架构就是将一个完整的应用从数据存储开始垂直拆分成多个不同的服务，每个服务都能独立部署、独立维护、独立扩展，服务与服务间通过诸如RESTful API的方式互相调用。

## 优势
1. 每个服务都比较简单，只关注一个业务功能
2. 微服务架构方式是松耦合的，可以提供更高的灵活性
3. 每个微服务可由不同团队独立开发，互不影响，加快推出市场速度
4.允许在频繁发布不同服务的同时，保持其他部分的可用性和稳定性
## 问题
1. 运维开销的成本增加
2. 系统复杂度变高
3. 部署的速度变慢
4. 分布式系统的冗余问题
5. 分布式系统的复杂性

<!-- more -->

# 服务治理
![服务注册中心对比](register.png)
- Spring Cloud应用中可以支持多种不同的服务治理框架，比如：Netflix Eureka、Consul、Zookeeper
- Spring Cloud Eureka是Spring Cloud Netflix项目下的服务治理模块。

2. Spring Cloud Ribbon是基于Netflix Ribbon实现的一套客户端负载均衡的工具。它是一个基于HTTP和TCP的客户端负载均衡器。它可以通过在客户端中配置ribbonServerList来设置服务端列表去轮询访问以达到均衡负载的作用。

3. Spring Cloud Feign是一套基于Netflix Feign实现的声明式服务调用客户端。它使得编写Web服务客户端变得更加简单。我们只需要通过创建接口并用注解来配置它既可完成对Web服务接口的绑定。它具备可插拔的注解支持，包括Feign注解、JAX-RS注解。它也支持可插拔的编码器和解码器。Spring Cloud Feign还扩展了对Spring MVC注解的支持，同时还整合了Ribbon和Eureka来提供均衡负载的HTTP客户端实现。


