## **CONSUL WEB UI**

领事有支持美丽,web ui开箱即用的功能。 ui可以用于查看所有服务和节点,查看所有卫生检查和他们的当前状态,并用于读取和设置键/值数据。UI 自动支持多数据中心。

有两个选项运行一个web UI领事:使用阿特拉斯HashiCorp举办仪表板或用到开源UI。

### 基于Atlas的Dashboard
![Atlas-hosted](https://www.consul.io/assets/images/atlas_web_ui-249f659e.png)
设立领事的阿特拉斯UI,您必须添加两个字段配置:阿特拉斯基础设施和阿特拉斯的名字牌。 下面是一个示例命令行调用领事代理人提供这些设置:

    $ consul agent -atlas=ATLAS_USERNAME/demo -atlas-token="ATLAS_TOKEN"

得到阿特拉斯用户名和令牌,创建一个帐户和替换相应的值在高配置您的凭据。

你可以在这里查看现场演示。

### Consul自建的Dashboard
![](https://www.consul.io/assets/images/consul_web_ui-3a1e7bf9.png)

设置自托管的用户界面,启动与UI领事代理参数:

    $ consul agent -ui
    ...

UI / UI路径的可用的端口HTTP API。 在默认情况下这是http://localhost:8500 / ui。

你可以查看这里的领事Web UI的现场演示。

现场演示时能够访问所有数据中心的数据,我们还设置演示端点的具体数据中心:AMS2(阿姆斯特丹)SFO1(旧金山),和NYC3(纽约)。

### 下一步

这就是我们的入门指南。 看到下一步页面了解更多关于如何与领事继续你的旅程! 
