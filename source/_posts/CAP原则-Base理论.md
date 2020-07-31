---
title: 'CAP原则-Base理论 '
copyright: true
categories:
  - NoteBook
abbrlink: '133821729'
date: 2019-02-10 15:57:16
tags:
top: 1
---

CAP原则：分布式的三个指标， 这三个指标不可能同时做到。这个结论就叫做 CAP 定理。 

- Consistency（一致性）： 写操作之后的读操作，必须返回该值 

- Availability（可用性）： 意思是只要收到用户的请求，服务器就必须给出回应 

- Partition tolerance（分区容错）： 大多数分布式系统都分布在多个子网络。每个子网络就叫做一个区（partition）。分区容错的意思是，区间通信可能失败   

   一般来说，分区容错无法避免，因此可以认为 CAP 的 P 总是成立。CAP 定理告诉我们，剩下的 C 和 A 无法同时做到。 

  CA为什么不能同时成立？因为可能通信失败（分区容错）

Base理论：BASE是Basically Available（基本可用）、Soft state（软状态）和Eventually consistent（最终一致性）的简写 

- 基本可用： 指分布式系统在出现故障的时候，允许损失部分可用性（例如响应时间、功能上的可用性），允许损失部分可用性。需要注意的是，基本可用绝不等价于系统不可用。 
- 最终一致性：最终一致性强调的是系统中所有的数据副本，在经过一段时间的同步后，最终能够达到一个一致的状态。因此，最终一致性的本质是需要系统保证最终数据能够达到一致，而不需要实时保证系统数据的强一致性
- 软状态：指允许系统存在中间状态，而该中间状态不会影响系统整体可用性。分布式存储中一般一份数据会有多个副本，允许不同副本同步的延时就是软状态的体现。mysql replication的异步复制也是一种体现。

 总的来说，BASE理论面向的是大型高可用可扩展的分布式系统，和传统事务的ACID特性使相反的，它完全不同于ACID的强一致性模型，而是提出通过牺牲强一致性来获得可用性，并允许数据在一段时间内是不一致的，但最终达到一致状态。但同时，在实际的分布式场景中，不同业务单元和组件对数据一致性的要求是不同的，因此在具体的分布式系统架构设计过程中，ACID特性与BASE理论往往又会结合在一起使用。

[聊聊分布式存储——轻松理解Raft](http://raft.taillog.cn//)

### 一：Eureka
### 二：Zookeeper
### 三：Consul

 Eureka 典型的 AP,作为分布式场景下的服务发现的产品较为合适，服务发现场景的可用性优先级较高，一致性并不是特别致命。其次 CA 类型的场景 Consul,也能提供较高的可用性，并能 k-v store 服务保证一致性。 而Zookeeper,Etcd则是CP类型 牺牲可用性，在服务发现场景并没太大优势；

|                 | Eureka                                                       | Zookeeper                                                    | Consul                              |
| --------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | ----------------------------------- |
| GitHub          | [https:/](https://github.com/apache/zookeeper)[/github.com/Netflix/eureka](https://github.com/Netflix/eureka) | https://github.com/apache/zookeeper                          | https://github.com/hashicorp/consul |
| 服务健康度检查  | 服务状态，内存，硬盘等                                       | (弱)长连接，keepalive                                        | 连接心跳                            |
| 多数据中心      | —                                                            | 支持                                                         | 支持                                |
| k-v存储服务     | —                                                            | 支持                                                         | 支持                                |
| 一致性          | —                                                            | paxos（Paxos算法是保证在分布式系统中写操作能够顺利进行，保证系统中大多数状态是一致的，没有机会看到不一致，因此，Paxos算法的特点是一致性>可用性。） | rafthttp://raft.taillog.cn/         |
| cap             | ap                                                           | cp                                                           | ca                                  |
| 多语言能力      | http（sidecar）                                              | 客户端                                                       | 支持http和dns                       |
| watch支持       | 支持 long polling/大部分增量                                 | —                                                            | metrics                             |
| 自身监控        | metrics                                                      | —                                                            | metrics                             |
| 安全            | —                                                            | acl                                                          | acl /https                          |
| SpringCloud集成 | 已支持                                                       | 已支持                                                       | 已支持                              |
