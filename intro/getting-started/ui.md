## **CONSUL WEB UI**

Consul支持一个漂亮的Web UI开箱即用。 UI可以用于查看所有服务和节点,查看所有健康检查和他们的当前状态,并用于读取和设置键/值数据。这个UI自动支持多数据中心。

有两个选项来运行一个Consul的web UI:基于[HashiCorp的Atlas](https://atlas.hashicorp.com/)的Dashboard或自建的[开源UI](https://www.consul.io/downloads.html)。


### 基于Atlas的Dashboard
![Atlas-hosted](https://www.consul.io/assets/images/atlas_web_ui-249f659e.png)
为了使用Consul的Atlas UI,您必须添加两个字段到配置:
[Atlas基础设施的名字](https://www.consul.io/docs/agent/options.html#_atlas)和[Atlas token](https://www.consul.io/docs/agent/options.html#_atlas_token)。 下面是一个示例命令行调用Consul代理提供这些设置:

    $ consul agent -atlas=ATLAS_USERNAME/demo -atlas-token="ATLAS_TOKEN"

为了获得Atlas用户名和令牌,创建一个帐户和替换相应的值在Consul配置您的凭据。

你可以在这里查看现场演示。

### Consul自建的Dashboard
![](https://www.consul.io/assets/images/consul_web_ui-3a1e7bf9.png)

设置自建的UI界面，使用`-ui`参数启动Consul agent:

    $ consul agent -ui
    ...

UI / UI路径的可用的端口HTTP API。 在默认的是http://localhost:8500/ui。

你可以在[这里](http://demo.consul.io/)查看Consul的在线演示。

现场演示时能够访问所有数据中心的数据,我们还设置演示端点的具体数据中心:AMS2(阿姆斯特丹)SFO1(旧金山),和NYC3(纽约)。

### 下一步

这就是我们的入门指南。 看到[下一步](next-steps.md
  )页面了解更多关于如何与Consul的旅程!
