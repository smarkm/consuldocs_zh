## 健康检查

我们现在看到有多么简单运行领事,添加节点和服务和查询这些节点和服务。 在本节中,我们将继续我们的旅行通过添加健康检查两个节点和服务。 健康检查服务盘的关键组件。

这一步构建在先前创建的领事集群。 此时,您应该有一个两节点集群运行。

### 定义坚持

类似于一个服务,检查可以通过提供一个注册检查定义或通过适当的HTTP API调用。

我们将使用检查定义方法,因为,就像服务,定义设置检查是最常见的方式。

创建两个定义文件的高配置目录中第二个节点:

    vagrant@n2:~$ echo '{"check": {"name": "ping",
      "script": "ping -c1 google.com >/dev/null", "interval": "30s"}}' \
      >/etc/consul.d/ping.json

    vagrant@n2:~$ echo '{"service": {"name": "web", "tags": ["rails"], "port": 80,
      "check": {"script": "curl localhost >/dev/null 2>&1", "interval": "10s"}}}' \
      >/etc/consul.d/web.json

第一个定义添加一个host-level检查命名为“平”。 这张支票在30秒的时间间隔运行,调用ping google.com往上平移。 在一个基于脚本的健康检查,检查运行相同的用户开始领事的过程。如果命令退出非零退出代码,那么节点将标记不健康。 这是合同对任何基于脚本的健康检查。

第二个命令修改服务命名web,添加一个检查每10秒发送一个请求通过curl来验证web服务器访问。 与host-level健康检查,如果与一个非零退出代码脚本将退出,该服务将标记不健康。

现在,启动第二个代理或将其发送SIGHUP信号。 您应该能看到以下日志行:

    ==> Starting Consul agent...
    ...
        [INFO] agent: Synced service 'web'
        [INFO] agent: Synced check 'service:web'
        [INFO] agent: Synced check 'ping'
        [WARN] Check 'service:web' is now critical

前几行表明,代理已同步的新定义。 最后一行显示的检查我们添加了web服务是至关重要的。 这是因为我们没有实际运行一个web服务器,所以旋度测试是失败的!

### 健康状态检查

既然我们已经添加了一些简单的检查,我们可以使用HTTP API检查它们。 首先,我们可以使用这个命令寻找任何失败的检查(注意,这既可以运行在节点):

    vagrant@n1:~$ curl http://localhost:8500/v1/health/state/critical
    [{"Node":"agent-two","CheckID":"service:web","Name":"Service 'web' check","Status":"critical","Notes":"","ServiceID":"web","ServiceName":"web"}]

我们可以看到,只有一个,我们的web服务检查,在临界状态。

此外,我们可以尝试使用DNS查询web服务。 领事将不会返回任何结果自服务是不健康的:

    dig @127.0.0.1 -p 8600 web.service.consul
    ...

    ;; QUESTION SECTION:
    ;web.service.consul.        IN  A

### 下一步

在本节中,您学习了是多么容易添加健康检查。 检查定义可以通过改变配置文件更新并发送SIGHUP代理。
此外,HTTP API可以用来添加、删除和修改动态检查。 API还允许“死者的开关”,TTL-based检查。 TTL检查可用于应用程序集成与高更紧密,使业务逻辑

接下来,我们将探索领事的K / V存储。 
