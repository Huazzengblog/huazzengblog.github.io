---
title: cloud native 
date: 2025-03-13 00:45:00 +/-TTTT
categories: 25spring
tags: [25spring]
---
# 云原生
## docker
* how to use docker
  * [FDUCSLG.Dokerguide](https://comp101.fducslg.com/tools/docker)
* docker compose
  在 `docker-compose.yml` 文件中，常见的元素和字段有很多，下面是一些主要的元素和字段的列举：

  1. version
  - 用于指定 Docker Compose 文件的版本。

  2. services
  - 定义容器服务。

  3. volumes
  - 定义 Docker Compose 中使用的卷（Volumes）。

  4. networks
  - 定义 Docker Compose 中的网络配置。

  5. build
  - 指定服务的构建配置。

  6. image
  - 用于指定要使用的 Docker 镜像。

  7. ports
  - 定义容器与主机之间的端口映射。

  8. environment
  - 用于设置环境变量。

  9. command
  - 用于覆盖容器的默认命令。

  10. depends_on
  - 用于指定服务之间的依赖关系。

  11. restart
  - 用于指定容器崩溃后的重启策略。

  12. logs
  - 定义日志选项。

  13. extra_hosts
  - 为容器添加额外的主机名映射。


  14.  secrets
  - 用于存储敏感数据。

  15.  healthcheck
  - 用于定义健康检查。
## k8s
![alt text](https://raw.githubusercontent.com/huazZengblog/huazZengblog.github.io/main/_posts/img/cloud/k8s.png)
### 控制平面组件
> 控制平面组件会为集群做出全局决策，比如资源的调度。 以及检测和响应集群事件

## 微服务架构

1. **单体架构**：
    - **定义**：单体架构是将所有功能集成在一个应用程序中，通过明确的模块间接口进行交互。
    - **好处**：开发简单、易于大规模修改、测试直观、部署简单、横向扩展容易。
    - **不足**：代码仓库膨胀、构建和部署变慢、难以扩展、可靠性难以保证、技术栈选择不灵活。

2. **单体地狱**：
    - **问题**：代码仓库急剧膨胀、构建和部署变慢、难以扩展、可靠性难以保证、技术栈选择不灵活。
    - **影响**：开发速度变慢、敏捷开发和快速交付难以实现。

3. **单体架构的扩展**：
    - **X轴扩展**：水平复制，通过克隆应用实例进行扩展。
    - **Z轴扩展**：数据划分，通过分割数据（如按用户ID）进行扩展。
    - **Y轴扩展**：功能分解，将应用拆分为多个服务，每个服务实现不同的功能。

4. **微服务架构**：
    - **定义**：将单个应用程序拆分为一组小的服务，每个服务在自己的进程中运行，并通过轻量级机制进行通信。
    - **特点**：服务可以独立开发和发布、独立部署和扩展、拥有自己的数据库、实例之间具有更好的隔离性、API构成不可逾越的边界。
    - **优势**：服务较小易于维护、支持持续交付和部署、独立部署和扩展、技术选型灵活、团队协作高效、故障隔离。

5. **微服务架构与SOA的区别**：
    - **SOA**：使用智能管道（如企业服务总线ESB），重量级协议（如SOAP）。
    - **微服务**：使用哑管道（如消息代理）或点对点通信，轻量级协议（如REST或gRPC）。

6. **分布式系统的设计要求**：
    - **高性能**：高并发访问下实现高吞吐量和低延迟。
    - **高可用性**：系统在任何时间都能正常运行并提供服务。
    - **可伸缩性**：系统处理能力能够随负载变化而扩展或收缩。
    - **高安全性**：系统难以被外部攻击或利用。

7. **分布式系统的CAP属性**：
    - **一致性（Consistency）**：数据更新后任何客户端都能读取到最新数据。
    - **可用性（Availability）**：请求总能返回正确响应。
    - **分区容错性（Partition Tolerance）**：系统在部分节点或网络分区故障时仍能提供服务。
    - **选择**：大多数互联网应用选择AP（可用性和分区容错性），牺牲一致性。

8. **BASE原则**：
    - **基本可用（Basically Available）**：允许服务质量下降，确保基本可用。
    - **软状态（Soft State）**：允许数据处于不一致的中间状态。
    - **最终一致性（Eventual Consistency）**：数据副本最终达到一致状态。

9. **进程间通信**：
    - **方式**：管道、共享内存、消息队列、Socket。
    - **选择**：内部通信使用异步消息，外部通信使用REST等同步方式。

10. **服务交互方式**：
    - **一对一**：每个客户端请求由一个服务实例处理。
    - **一对多**：每个客户端请求由多个服务实例处理。
    - **同步**：服务端实时响应，客户端阻塞等待。
    - **异步**：服务端非实时响应，客户端不阻塞。

11. **消息格式**：
    - **基于文本**：如XML、JSON，优点是可读性高，缺点是冗余大。
    - **基于二进制**：如Protocol Buffers、Avro，优点是高效紧凑，缺点是需要IDL定义。

12. **同步远程过程调用**：
    - **模式**：客户端通过代理接口调用服务端业务逻辑。
    - **缺点**：存在局部故障风险，客户端需阻塞等待响应。

13. **REST**：
    - **定义**：使用HTTP协议的进程间通信机制，强调组件交互的可扩展性和接口的通用性。
    - **成熟度模型**：从Level 0到Level 3，逐步引入资源概念、HTTP动词操作、资源信息中包含允许的操作及其URL链接。
    - **优点**：规范简单，支持浏览器扩展，直接支持请求/响应方式通信。
    - **缺点**：只支持请求/响应方式通信，可能导致可用性降低。

14. **gRPC**：
    - **定义**：基于二进制消息的跨语言客户端和服务端框架，支持流式RPC。
    - **优点**：高效紧凑的进程间通信机制，支持双向流式消息，实现客户端和服务端之间的互操作性。
    - **缺点**：JavaScript客户端使用需要额外工作，旧式防火墙可能不支持HTTP/2。

15. **异步通信**：
    - **模式**：通过异步交换消息的方式进行通信，支持请求/响应、单向通知、发布/订阅、发布/异步响应。
    - **优点**：避免局部故障风险，提高系统弹性。

16. **消息代理**：
    - **定义**：作为服务之间的中介，负责消息的传递和管理。
    - **优点**：客户端与服务松耦合，可以进行消息缓存，支持各种交互机制。
    - **缺点**：可能存在性能瓶颈，带来潜在的单点故障。

17. **分布式存储**：
    - **分布式文件系统**：如HDFS，部署在多台服务器组成的集群上，提供统一的文件访问。
    - **分布式数据库**：如HBase、MongoDB、Cassandra，部署在多台服务器上，逻辑上统一的非关系型数据库。

18. **分布式缓存**：
    - **定义**：将经常被读取且较少变化的数据放到内存中，提高数据访问效率。
    - **系统**：如Redis，支持多种数据结构，自身也支持持久化数据保存。

19. **分布式事务**：
    - **ACID特性**：原子性、一致性、隔离性、持久性。
    - **模式**：2PC、TCC、Saga，分别通过不同的机制实现分布式事务的一致性和可靠性。

20. **小结**：
    - 单体架构与单体地狱的问题。
    - Web软件体系结构的发展：从单体服务器到多服务器负载均衡，再到面向服务的体系结构和云+容器+微服务。
    - 微服务体系结构的特点和优势。
    - 分布式系统的设计要求：数据一致性、进程间通信、分布式事务。




## 微服务应用设计