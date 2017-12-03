---
layout: "intro"
page_title: "Consul Cluster"
sidebar_current: "gettingstarted-join"
description: >
  When a Consul agent is started, it begins as an isolated cluster of its own.
  To learn about other cluster members, the agent must join one or more other
  nodes using a provided join address. In this step, we will set up a two-node
  cluster and join the nodes together.
---

## **Consul 集群**

我们启动了第一个agent并在agent上注册和查询一个服务。这显示是多么容易使用高但没有显示这可能扩展到一个可伸缩的,工业生产服务发现基础设施。 在这一步中,我们将创建我们的第一个真正的集群与多个成员。

当一个Consul agent启动，开始没有任何其他节点的知识 ：这是一个孤立的集群的一个 。要了解其他集群成员,代理必须加入到现有的集群中。 加入后,代理将与这个成员八卦,很快发现在集群中的其他成员。 领事代理可以加入任何其他代理,不仅代理服务器模式。

### 启动 Agent

模拟一个更现实的集群中,我们将开始通过流浪的一个两节点集群。 可以找到Vagrantfile我们将使用在领事的演示部分回购。

我们第一次启动我们的两个节点:

    $ vagrant up

一旦系统可用,我们可以ssh到他们开始我们的集群配置。 我们首先登录到第一个节点:

    $ vagrant ssh n1

我们之前的例子中,我们使用- dev国旗快速建立一个开发服务器。 然而,这是不够的在集群环境中使用。 从这里我们将省略- dev国旗,而不是指定我们的集群旗帜如下面。

集群中的每个节点必须有一个唯一的名称。 默认情况下,高使用机器的主机名,但是我们将使用- node命令行选项手动覆盖它。

我们还将指定一个绑定地址:这是领事监听地址,它必须可以访问集群中的所有其他节点。 而绑定地址并不是必需的(领事将默认情况下监听第一个私人IP系统上)

第一个节点将作为我们唯一的服务器集群中,我们显示与服务器开关。

-bootstrap-expect标志提示领事服务器的数量我们期待加入额外的服务器节点。这个标志的目的是推迟的引导复制日志之前预期的服务器数量已成功加入 。你可以阅读更多关于这个引导指南。

最后,我们添加config-dir国旗标志,可以找到服务和检查定义。

 一起,这些设置产生领事代理命令如下:

    vagrant@n1:~$ consul agent -server -bootstrap-expect 1 \
        -data-dir /tmp/consul -node=agent-one -bind=172.20.20.10 \
        -config-dir /etc/consul.d
    ...

现在,在另一个终端,我们将连接到第二个节点:

    $ vagrant ssh n2

这一次,我们设置了绑定的IP地址地址匹配第二个节点按Vagrantfile agent-two和节点名称。因为这个节点将不是一个领事服务器,我们不提供服务器切换。

一起,这些设置产生领事代理命令如下:

    vagrant@n2:~$ consul agent -data-dir /tmp/consul -node=agent-two \
        -bind=172.20.20.11 -config-dir /etc/consul.d
    ...

此时,您有两个领事代理运行:一台服务器和一个客户端。 这两个领事代理还不了解彼此,都是自己的单节点集群的一部分。 您可以验证通过运行领事成员对每个代理和注意的是,只有一个成员是可见的每个代理。

### 加入一个集群

现在,我们要告诉第一个代理加入第二个代理在一个新的终端通过运行以下命令:

    $ vagrant ssh n1
    ...
    vagrant@n1:~$ consul join 172.20.20.11
    Successfully joined cluster by contacting 1 nodes.

您应该看到在每个代理的一些日志输出日志。 如果你仔细阅读,你会发现他们收到连接信息。如果你运行对每个代理领事成员,您将看到这两个代理现在彼此相互了解:

    vagrant@n2:~$ consul members
    Node       Address            Status  Type    Build  Protocol
    agent-two  172.20.20.11:8301  alive   client  0.5.0  2
    agent-one  172.20.20.10:8301  alive   server  0.5.0  2

    记住:加入一个集群,一个领事代理只需要了解一个现有的成员。 加入集群后,代理商互相八卦传播完全成员信息。

### 启动时自动加入集群

理想情况下,每当一个新节点在自己的数据中心,它应该会自动加入高集群而无需人工干预。 为了实现这一点,您可以使用阿特拉斯HashiCorp和-atlas-join国旗。 一个例子配置如下所示:

    $ consul agent -atlas-join \
    -atlas=ATLAS_USERNAME/infrastructure \
    -atlas-token="YOUR_ATLAS_TOKEN"

得到阿特拉斯用户名和令牌,创建一个帐户和替换相应的值在高配置您的凭据。 现在,每当一个新节点提出了一个领事代理,它会自动加入您的领事集群没有哈 。

或者,你可以加入一个集群在启动时使用加入标志或start_join与其他已知的硬编码地址领事代理设置。

### 查询节点

就像查询服务,驻一个API查询节点本身。 你可以通过DNS或HTTP API。

DNS的API,名字是NAME.node的结构。 领事或NAME.node.DATACENTER.consul。 如果省略数据中心,领事将只搜索本地数据中心。

例如,从“agent-one”,我们可以查询节点的地址“agent-two”:

    vagrant@n1:~$ dig @127.0.0.1 -p 8600 agent-two.node.consul
    ...

    ;; QUESTION SECTION:
    ;agent-two.node.consul. IN  A

    ;; ANSWER SECTION:
    agent-two.node.consul.  0 IN    A   172.20.20.11

除了查找节点服务的能力对系统管理任务非常有用。 例如,知道节点的地址SSH到一样容易使集群节点的一部分领事和查询。

### 留下一个集群

离开集群,可以优雅地退出一个代理(使用ctrl - c)或者迫使杀死的一个代理。 优雅地离开允许节点过渡到左边状态;否则,其他节点将检测失败。 不同之处在于有限公司。

### 下一步

我们现在有一个多节点集群高启动并运行。 让我们使我们的服务更加健壮,向他们提供健康检查!
