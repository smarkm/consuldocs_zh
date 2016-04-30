## **Consul安装**
Consul必须在每一个Consul集群的节点上安装。为了使安装简便，Consul为所有支持平台和体系结构提供[二进制包](https://www.consul.io/downloads.html)。本页面不会描述如何通过源码进行编译。

### 安装Consul
Consul被打包成一个`zip`文件，安装Consul需要选择[合适的安装包](https://www.consul.io/downloads.html)。

下载Consul后解压。将`consul`二进制文件拷贝到`PTAH`下面一般能够通过命令行执行。在Unix系统下，通常安装在`~/bin`或`/usr/local/bin`目录下，取决于如果你想限制单个用户的安装或使整个系统 。在Windows系统下面，你可以放到任何地方，通常会放在`%PATH%`。

### OS X
如果你使用homebrew作为包管理器，你可以通过如下命令安装：
  $brew cask install consul

如果你错过cask plugin，你可以使用下面的命令安装：
  $brew install caskroom/cask/brew-cask

### 验证安装
安装完成后打开新的终端验证consul是否可用。执行consul你应该会看到类似下面的信息：

    $ consul
    usage: consul [--version] [--help] <command> [<args>]  
    Available commands are:  
      agent          Runs a Consul agent  
      event          Fire a new event  
      exec           Executes a command on Consul nodes  
      force-leave    Forces a member of the cluster to enter the "left" state  
      info           Provides debugging information for operators  
      join           Tell Consul agent to join cluster  
      keygen         Generates a new encryption key  
      leave          Gracefully leaves the Consul cluster and shuts down  
      members        Lists the members of a Consul cluster  
      monitor        Stream logs from a Consul agent  
      reload         Triggers the agent to reload configuration files  
      version        Prints the Consul version  
      watch          Watch for changes in Consul  

如果出错可能是consul没有安装好，可能是你的PATH环境没有设置。请返回确认你的PATH参数包含COnsul安装的目录。

### 下一步
Consul安装好准备使用，然我们来[运行agent](https://www.consul.io/intro/getting-started/agent.html)!
