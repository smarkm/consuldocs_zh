---
layout: "docs"
page_title: "Guides"
sidebar_current: "docs-guides"
description: |-
  This section provides various guides for common actions. Due to the nature of Consul, some of these procedures can be complex, so our goal is to provide guidance to do them safely.
---

# Consul 指导
本章提供了一些通用操作的指导。由于Consul的特性，一些处理肯能有些复杂，所以我们的目标是为做这些提供安全的指导。

以下是可用的指导：

* [ACLs](/docs/guides/acl.html) - 这个指导覆盖Consul的访问控制列表 (ACL) 能力,它可以用来控制对Consul资源的访问。

* [Adding/Removing Servers](/docs/guides/servers.html) - 这个指导包含了如何安全的在集群上添加和移除Consul server。为了避免可用性中断，应谨慎执行此操作。

* [Autopilot](/docs/guides/autopilot.html) - This guide covers Autopilot, which provides automatic operator-friendly management of Consul servers.

* [Bootstrapping](/docs/guides/bootstrapping.html) - 这个指导包含了如何创建一个新的datacenter和添加初始化Consul server。

* [Consul with Containers](/docs/guides/consul-containers.html) - This guide describes critical aspects of operating a Consul cluster that's run inside containers.

* [DNS Caching](/docs/guides/dns-cache.html) - 为DNS查询缓存使用TTLs 

* [DNS Forwarding](/docs/guides/forwarding.html) - 转发DNS查询到Consul

* [External Services](/docs/guides/external.html) - 这个指导包含如何注册一个外部的服务。运行在Consul 框架中使用第三方服务。

* Federation ([Basic](/docs/guides/datacenters.html) and [Advanced](/docs/guides/areas.html)) - 配置Consul以支持多数据中心。

* [Geo Failover](/docs/guides/geo-failover.html) - This guide covers using [prepared queries](/api/query.html) to implement geo failover for services.

* [Leader Election](/docs/guides/leader-election.html) - 指导的目的是说明如何使用Consul构建客户端的leader选举。

* [Outage Recovery](/docs/guides/outage.html) - 这个说明如何恢复一个因server失败导致不可用的集群。

* [Semaphore](/docs/guides/semaphore.html) - This guide covers using the KV store to implement a semaphore.

* [Server Performance](/docs/guides/performance.html) - This guide covers minumum requirements for Consul servers as well as guidelines for running Consul servers in production.