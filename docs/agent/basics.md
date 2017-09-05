---
layout: "docs"
page_title: "Agent"
sidebar_current: "docs-agent-running"
description: |-
  The Consul agent is the core process of Consul. The agent maintains membership information, registers services, runs checks, responds to queries, and more. The agent must run on every node that is part of a Consul cluster.
---

# Consul Agent

Consul Agent 是consul核心程序。Agent管理成员的信息，注册服务，运行检查，查询响应等。Agent必须在每一个节点上运行，它是集群的一部分。


任何一个Agent都将运行两中模式中的一种：clent或server。server节点提供[consensus quorum](/docs/internals/consensus.html).的额外信息。
 A server
node takes on the additional responsibility of being part of the [consensus quorum](/docs/internals/consensus.html).
These nodes take part in Raft and provide strong consistency and availability in
the case of failure. The higher burden on the server nodes means that usually they
should be run on dedicated instances -- they are more resource intensive than a client
node. Client nodes make up the majority of the cluster, and they are very lightweight
as they interface with the server nodes for most operations and maintain very little state
of their own.

## 运行 Agent

Agent通过命令[`consul agent`](/docs/commands/agent.html)启动。下面的命令块会一直运行，直到被通知推出。agent命令提供很多配置参数，但是很多是默认提供的。


当运行 [`consul agent`](/docs/commands/agent.html)时, 你会看到类似以下的输出:

```text
$ consul agent -data-dir=/tmp/consul
==> Starting Consul agent...
==> Consul agent running!
       Node name: 'Armons-MacBook-Air'
      Datacenter: 'dc1'
          Server: false (bootstrap: false)
     Client Addr: 127.0.0.1 (HTTP: 8500, DNS: 8600)
    Cluster Addr: 192.168.1.43 (LAN: 8301, WAN: 8302)

==> Log data will now stream in as it occurs:

    [INFO] serf: EventMemberJoin: Armons-MacBook-Air.local 192.168.1.43
...
```
[`consul agent`](/docs/commands/agent.html) 会输出很多重要的信息:

* **Node name**: 这是agent的唯一名字。默认的是机器的hostname，但你可以通过  [`-node`](/docs/agent/options.html#_node)标记自定义。

* **Datacenter**: agent配置运行的数据中心。Consul对多数据中心提供良好的支持，为了有效的工作，每一个节点必须配置报告他的数据中心。[`-datacenter`](/docs/agent/options.html#_datacenter)标记可以用来设置数据 中心。对于单数据中心配置，在 agent中默认为`dc1`.This is the datacenter in which the agent is configured to run.
 Consul has first-class support for multiple datacenters; however, to work efficiently,
 each node must be configured to report its datacenter. The [`-datacenter`](/docs/agent/options.html#_datacenter)
 flag can be used to set the datacenter. For single-DC configurations, the agent
 will default to "dc1".

* **Server**: 指明agent是运行在server还是client模式下。server有协商法定人数的责任，存储集群状态，处理查询。另外，server也许在运行在["bootstrap"](/docs/agent/options.html#_bootstrap_expect) 模式下。多个服务器
不能处于自举模式，因为这将使集群处于不一致的状态。This indicates whether the agent is running in server or client mode.
  Server nodes have the extra burden of participating in the consensus quorum,
  storing cluster state, and handling queries. Additionally, a server may be
  in ["bootstrap"](/docs/agent/options.html#_bootstrap_expect) mode. Multiple servers
  cannot be in bootstrap mode as that would put the cluster in an inconsistent state.

* **Client Addr**: 这是用于代理的客户端接口的地址。包含HTTP和DNS接口的port，绑定在本地。如果想 改变IP和端口，你需要在运行命令指定`-http-addr`来说明如何访问到agent。其他应用同样可以使用HTTP地址和端口来[控制Consul](/api/index.html)。This is the address used for client interfaces to the agent.
  This includes the ports for the HTTP and DNS interfaces. By default, this binds only
  to localhost. If you change this address or port, you'll have to specify a `-http-addr`
  whenever you run commands such as [`consul members`](/docs/commands/members.html) to
  indicate how to reach the agent. Other applications can also use the HTTP address and port
  [to control Consul](/api/index.html).

* **Cluster Addr**: Consul集群中agents相互通信的端口集合，不是集群中所有的agent都需要使用相同的端口，但是地址必须是所有节点可访问的。

当运行在Linux系统的`systemd`下时，Consul通过发出`READY=1`到`$NOTIFY_SOCKET`来通知systemd一个LAN 加入完成。When running under `systemd` on Linux, Consul notifies systemd by sending
`READY=1` to the `$NOTIFY_SOCKET` when a LAN join has completed. For
this either the `join` or `retry_join` option has to be set and the
service definition file has to have `Type=notify` set.

## 停止 Agent

一个agent可以通过两种方法停掉：优雅的和强制的。
An agent can be stopped in two ways: gracefully or forcefully.
对于优雅的停止agent，发送给进程一个终断信号（通常是从命令行使用`Ctrl-C`或者执行 `kill -INT consul_pid`）。当优雅的停掉agent的时，agent首先通知 集群它想要离开集群。这样其他的。。。 To gracefully
halt an agent, send the process an interrupt signal (usually
`Ctrl-C` from a terminal or running `kill -INT consul_pid` ). When gracefully exiting, the agent first notifies
the cluster it intends to leave the cluster. This way, other cluster members
notify the cluster that the node has _left_.

另一种方法，你可以强制
Alternatively, you can force kill the agent by sending it a kill signal.
When force killed, the agent ends immediately. The rest of the cluster will
eventually (usually within seconds) detect that the node has died and
notify the cluster that the node has _failed_.

It is especially important that a server node be allowed to leave gracefully
so that there will be a minimal impact on availability as the server leaves
the consensus quorum.

For client agents, the difference between a node _failing_ and a node _leaving_
may not be important for your use case. For example, for a web server and load
balancer setup, both result in the same outcome: the web node is removed
from the load balancer pool.

## Lifecycle

Every agent in the Consul cluster goes through a lifecycle. Understanding
this lifecycle is useful for building a mental model of an agent's interactions
with a cluster and how the cluster treats a node.

When an agent is first started, it does not know about any other node in the cluster.
To discover its peers, it must _join_ the cluster. This is done with the
[`join`](/docs/commands/join.html)
command or by providing the proper configuration to auto-join on start. Once a node
joins, this information is gossiped to the entire cluster, meaning all nodes will
eventually be aware of each other. If the agent is a server, existing servers will
begin replicating to the new node.

In the case of a network failure, some nodes may be unreachable by other nodes.
In this case, unreachable nodes are marked as _failed_. It is impossible to distinguish
between a network failure and an agent crash, so both cases are handled the same.
Once a node is marked as failed, this information is updated in the service catalog.

-> **Note:** There is some nuance here since this update is only possible if the servers can still [form a quorum](/docs/internals/consensus.html). Once the network recovers or a crashed agent restarts the cluster will repair itself and unmark a node as failed. The health check in the catalog will also be updated to reflect this.

When a node _leaves_, it specifies its intent to do so, and the cluster
marks that node as having _left_. Unlike the _failed_ case, all of the
services provided by a node are immediately deregistered. If the agent was
a server, replication to it will stop.

To prevent an accumulation of dead nodes (nodes in either _failed_ or _left_
states), Consul will automatically remove dead nodes out of the catalog. This
process is called _reaping_. This is currently done on a configurable
interval of 72 hours (changing the reap interval is *not* recommended due to
its consequences during outage situations). Reaping is similar to leaving,
causing all associated services to be deregistered.