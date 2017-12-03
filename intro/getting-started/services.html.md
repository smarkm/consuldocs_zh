---
layout: "intro"
page_title: "Registering Services"
sidebar_current: "gettingstarted-services"
description: >
  A service can be registered either by providing a service definition or by
  making the appropriate calls to the HTTP API. A configuration file is the
  most common, so we will use this approach to register a service, and then
  query that service using the REST API and DNS interfaces.
---

## **注册服务**
在前面的步骤中,我们跑第一个代理,看到了集群成员(我们的集群成员)和节点的查询。 在本指南中,我们将注册我们的第一个服务和查询服务。

### 定义一个服务

服务可以通过提供注册[服务定义](https://www.consul.io/docs/agent/services.html)或通过适当的[HTTP API](https://www.consul.io/docs/agent/http.html)调用。

服务定义是最常见的方式注册服务,所以这一步我们将使用这种方法。 我们将会建立在[前一步](https://www.consul.io/intro/getting-started/agent.html)我们覆盖的代理配置。

首先,创建一个目录高配置。 领事加载所有配置文件的配置目录,所以常见的Unix系统公约是名字/etc/consul.d之类的目录(.d后缀暗示“这个目录包含一组配置文件”)。
    $ sudo mkdir /etc/consul.d
接下来，我们写一个服务定义配置文件。假设我们有一个叫“web”的服务运行在端口80上。 另外,我们给它一个标签可以使用作为一个额外的方法查询服务:
    $ echo '{"service": {"name": "web", "tags": ["rails"], "port": 80}}' \
        >/etc/consul.d/web.json

现在重启agent提供配置文件目录：
    $ consul agent -dev -config-dir /etc/consul.d
    ==> Starting Consul agent...
    ...
        [INFO] agent: Synced service 'web'
    ...

你会从输出中看到了“synced”（同步了web服务。）这意味着代理的服务定义加载配置文件,和已经成功注册服务目录。

如果你想注册多个服务,您可以创建多个服务定义文件的高配置目录。

### 查询服务
一旦启动代理和服务同步,我们可以使用DNS或HTTP API查询服务。

### DNS API

首先我们通过DNS AOI产销我们的服务。默认情况下,所有DNS名称总是领事名称空间中的,虽然这是可配置的。 服务子域名告诉高我们查询服务,和名字是服务的名称。

我们注册的web服务,这些公约和设置收益率web.service.consul的完全限定域名 。
    $ dig @127.0.0.1 -p 8600 web.service.consul
    ...

    ;; QUESTION SECTION:
    ;web.service.consul.        IN  A

    ;; ANSWER SECTION:
    web.service.consul. 0   IN  A   172.20.20.11

正如你所看到的,一个一个记录返回的IP地址的节点服务是可用的。 记录只能保存IP地址。

您还可以使用DNS API来检索整个地址/端口对SRV记录:
    $ dig @127.0.0.1 -p 8600 web.service.consul SRV
    ...

    ;; QUESTION SECTION:
    ;web.service.consul.        IN  SRV

    ;; ANSWER SECTION:
    web.service.consul. 0   IN  SRV 1 1 80 Armons-MacBook-Air.node.dc1.consul.

    ;; ADDITIONAL SECTION:
    Armons-MacBook-Air.node.dc1.consul. 0 IN A  172.20.20.11

SRV记录说,web服务运行在端口80上,存在于节点Armons-MacBook-Air.node.dc1.consul . . 额外的部分是DNS记录,返回的节点。

最后,我们还可以使用DNS API来过滤服务标记。 基于标签的格式是TAG.NAME.service.consul服务查询。 在下面的示例中,我们问领事所有web服务的“轨道”标签。 我们得到一个成功的响应,因为我们里吉斯。
$ dig @127.0.0.1 -p 8600 rails.web.service.consul
...

;; QUESTION SECTION:
;rails.web.service.consul.      IN  A

;; ANSWER SECTION:
rails.web.service.consul.   0   IN  A   172.20.20.11

### HTTP API
除了DNS API,HTTP API可以用来查询服务:
    $ curl http://localhost:8500/v1/catalog/service/web
    [{"Node":"Armons-MacBook-Air","Address":"172.20.20.11","ServiceID":"web", \
    "ServiceName":"web","ServiceTags":["rails"],"ServicePort":80}]

目录的API给所有节点主机给定的服务。 稍后我们将看到通过健康检查您通常会想查询只是为了传递健康情况检查。 这是DNS在做什么。 这里有一个查询来寻找 ：
    $ curl 'http://localhost:8500/v1/health/service/web?passing'
    [{"Node":"Armons-MacBook-Air","Address":"172.20.20.11","Service":{ \
    "ID":"web", "Service":"web", "Tags":["rails"],"Port":80}, "Checks": ...}]

### 更新服务
服务定义可以通过改变配置文件更新并发送SIGHUP代理。 这允许您更新服务没有任何停机时间或服务不可用查询。
此外,HTTP API可用于添加、删除和密苏里州 。

### 下一个步骤 
现在我们已经配置了一个代理和注册服务。 这是不错的进展,但让我们探索领事的全部价值通过建立第一个集群!
