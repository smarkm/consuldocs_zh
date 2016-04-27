## **Consul介绍**

欢迎使用Consul简介指导，本指南是使用Consul最好的起点。我们描述了Consul是什么，他将解决什么问题，和其他同类软件项目有什么不同，以及如何入门使用。如果你熟悉Consul的基本知识，[文档]()([Documentation](https://www.consul.io/docs/))提供了更详细的可用特性参考。

### Consul是什么
Consul有多个组件，但是作为一个整体，他在你的基础设施中是一个提供发现和配置服务的工具。它提供了一些主要特性。

* 服务发现：Consul客户端可以提供服务例如`api`或者 `mysql`，其他的客户端可以使用Consul发现被提供的服务。通过DNS或HTTP，应用可以很容易的找到依赖的服务。

* 健康检查：Consul 客户端可以提供任何数量的健康检查，或者与指定的服务想关联（"is the webserver returning 200 OK"），或者使用本地节点（"is memory utilization below 90%"）。这些信息可以被操作者用来健康集群的健康，也可以被服务发组件用来从肺健康的主机上转发流量。

* 键/值对存储：应用程序可以利用Consul的分层键/值存储为任意数量的目的，包括动态配置，功能标记，协调，leader选举等。简单的HTTP API使得它非常易于使用 。

* 多数据中心：Consul支持多数据中心。这意味着Consul的用户不必担心建设额外的抽象层来应对多个区域。 

Consul 被设计为DevOps社区和应用开发者友好的，使它适合现代的、弹性的基础设施 。

### Consul的基本架构
Consul是一个分布式的，高可用的系统。本节将描述基本的内容，故意忽略一些不必要的细节
，以便你能快速的理解Consul是如何工作的。对应更多的信息，请参考[ in-depth architecture overview](https://www.consul.io/docs/internals/architecture.html)。

每一个COnsul中提供服务的节点运行一个Consul代理（*Consul agent*）。Running an agent is not required for discovering other services or getting/setting key/value data。代理负责健康检查服务节点以及节点本身。
代理和一个或多个*Consul Server*通信。*Consul Server* 是数据存储和复制的地方。所有的*Consul Server*  会选出一个leader。Consulo可以通过一个server来提供服务，但推荐使用3到5个server来避免服务失败导致的数据丢失。推荐每一个数据中心建立一个Consul集群。

在你基础设施的组件需要发现其他服务或节点能查看任何COnsul servers或者Consul agents。代理自动转发查询到servers。

每一个数据中心运行一个Consul Server的集群。当有需求需要苦熬数据中心进行服务发现或配置，本地COnsul servers转发请求道远程数据中心并返回结果。

### 下一步
* 查看[Consul与软件软件的比较](https://www.consul.io/intro/vs/)来决定如何来在你的基础设施中使用Consul。
* 继续使用[入门指南[入门指南](https://www.consul.io/intro/getting-started/install.html)来启动和运行Consul。
