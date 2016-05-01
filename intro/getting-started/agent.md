## **运行Consul代理**

Consul安装后，必须要运行agent(代理)。agent可以运行在server模式或client模式下。每一个数据中必须至少要有一个server，但3或5个server是推荐的配置。单一服务器部署是不被推荐的,失败场景数据丢失是不可避免的。

所有其他的agent运行在client模式。client是一个非常轻量级的进程，用来注册服务，运行健康检查，转发请求到server。cluster上的每一个节点都需要运行agent。

关于引导一个数据中心的更多信息，查看这个[指南](https://www.consul.io/docs/guides/bootstrapping.html).

### 启动Agent

简单起见，我们现在以开发模式启动Consul。这种模式有助于快速轻松地将一个单节点高环境 。不要试图在生产环境中这样用因为他不会保存任何状态和数据。  

    $ consul agent -dev
    ==> Starting Consul agent...
    ==> Starting Consul agent RPC...
    ==> Consul agent running!
           Node name: 'Armons-MacBook-Air'
          Datacenter: 'dc1'
              Server: true (bootstrap: false)
         Client Addr: 127.0.0.1 (HTTP: 8500, HTTPS: -1, DNS: 8600, RPC: 8400)
        Cluster Addr: 172.20.20.11 (LAN: 8301, WAN: 8302)
      Gossip encrypt: false, RPC-TLS: false, TLS-Incoming: false
               Atlas: <disabled>

    ==> Log data will now stream in as it occurs:

    [INFO] raft: Node at 172.20.20.11:8300 [Follower] entering Follower state
    [INFO] serf: EventMemberJoin: Armons-MacBook-Air 172.20.20.11
    [INFO] consul: adding LAN server Armons-MacBook-Air (Addr: 172.20.20.11:8300) (DC: dc1)
    [INFO] serf: EventMemberJoin: Armons-MacBook-Air.dc1 172.20.20.11
    [INFO] consul: adding WAN server Armons-MacBook-Air.dc1 (Addr: 172.20.20.11:8300) (DC: dc1)
    [ERR] agent: failed to sync remote state: No cluster leader
    [WARN] raft: Heartbeat timeout reached, starting election
    [INFO] raft: Node at 172.20.20.11:8300 [Candidate] entering Candidate state
    [DEBUG] raft: Votes needed: 1
    [DEBUG] raft: Vote granted. Tally: 1
    [INFO] raft: Election won. Tally: 1
    [INFO] raft: Node at 172.20.20.11:8300 [Leader] entering Leader state
    [INFO] raft: Disabling EnableSingleNode (bootstrap)
    [INFO] consul: cluster leadership acquired
    [DEBUG] raft: Node 172.20.20.11:8300 updated peer set (2): [172.20.20.11:8300]
    [DEBUG] consul: reset tombstone GC to index 2
    [INFO] consul: New leader elected: Armons-MacBook-Air
    [INFO] consul: member 'Armons-MacBook-Air' joined, marking health alive
    [INFO] agent: Synced service 'consul'

如你所见，Consul agent已经启动并输出了一些日志。从日志中，你可以看到agent已经以server模式启动并且作为cluster的leader。此外,本地的成员已经被标记为一个健康的集群成员。

    OS X 用户注意：
    Consul使用hostname作为结点的名字，如果你的hostname中包含时间，Consul的DNS产销将不工作。为了避免这种情况可以使用-node来设置名字。

  ### 集群成员
  如果你在另一个终端运行`consul members`,你可以看到Consul集群的成员，我们在下节描述如果加入新成员，但是现在你只能看到一个成员（你自己）：

    $ consul members
    Node                Address            Status  Type    Build     Protocol  DC
    Armons-MacBook-Air  172.20.20.11:8301  alive   server  0.6.1dev  2         dc1

输出显示了节点，节点所运行的IP，他的健康状态以及在集群中的角色和版本信息。可以通过提供`-detaitled`标记来查看额外的元数据。
members命令的输出是基于[gossip协议](https://www.consul.io/docs/internals/gossip.html)最终一致的。也就是在任意一个时间点，你从本地agent看到的信息不一定和servers上的相同。为了看到最终一致的信息，用HTTP API来发送请求到Consul server：
    $ curl localhost:8500/v1/catalog/nodes
    [{"Node":"Armons-MacBook-Air","Address":"172.20.20.11","CreateIndex":3,"ModifyIndex":4}]

除了HTTP API ，[DNS接口](https://www.consul.io/docs/agent/dns.html)也可以查询节点信息。请注意,您必须确保您的DNS查找指向Consul代理的DNS服务器的默认端口8600上运行 。DNS条目的格式 (such as "Armons-MacBook-Air.node.consul")  稍后将更详细地讨论。
    $ dig @127.0.0.1 -p 8600 Armons-MacBook-Air.node.consul
    ...

    ;; QUESTION SECTION:
    ;Armons-MacBook-Air.node.consul.    IN  A

    ;; ANSWER SECTION:
    Armons-MacBook-Air.node.consul. 0 IN    A   172.20.20.11

### 关闭Agent  
你可以用Ctrl-C（向系统发送中断信号）优雅的停掉agent。中断agent后，你会看到agent离开了cluster并关闭。

通过优雅的离开，Consul会通知集群中其他成员有node离开。如果你强制kill掉agent进程，其他集群成员将检测到的节点失败了。当一个成员离开，它的服务和检查将从catalog中移除。当一员失败,它的健康是简单地标记为重要,但它不是从catalog中删除。 Consul会自动的尝试从新链接失败的节点，让它恢复一定的网络条件,而左节点不再联系了。

此外,如果一个代理作为一个服务器,一个优雅的离开是很重要的,以避免引起潜在的可用性故障影响达成[一致协议](https://www.consul.io/docs/internals/consensus.html)。 有关详细信息,请参阅[指南部分](https://www.consul.io/docs/guides/)如何安全地添加和删除服务器。

### 下一步  

简单的Consul集群已经启动运行，让我们来了解下[services](https://www.consul.io/intro/getting-started/services.html);
