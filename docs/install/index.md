# 安装 Consul
安装 Consul很简单。可以通过以下两种方法：

1. 使用 [预编译二进制文件](#precompiled-binaries)

2. 编译[源文件](#compiling-from-source)安装

下载预编译二进制文件很简单，我们提供通过TLS来下载并附带SHA256校验和。同行提供SHA256的PGP签名来校验。

## 预编译二进制文件
安装二进制文件，首先[下载](/downloads.html)适合你操作系统的安装包。Consul当前带包为zip文件。没有计划提供系统的安装包。

zip文件下载完成后，解压到任意目录。`consul`二进制文件满足所有运行Consul的需要。不需要其他的任何附加文件。

可以将二进制文件拷贝到系统的任何地方。如果你尝试从命令行访问，请确保其在系统变量`PATH`下。

## 从源码编译
为了从源码编译，你需要安装 [Go](https://golang.org) 并配置变量(including a `GOPATH` environment variable set), 并且[`git`](https://www.git-scm.com/) 需要在环境变量 `PATH`下.

  1. 从github克隆Consul Repo到`GOPATH`下: 

    ```shell
    $ mkdir -p $GOPATH/src/github.com/hashicorp && cd $!
    $ git clone https://github.com/hashicorp/consul.git
    $ cd consul
    ```

  1. 引导项目，这会下载和编译lib和工具（编译Consul需要的）:

    ```shell
    $ make bootstrap
    ```

  1. 从当前系统构建Consul并将二进制文件放到 `./bin/`
  (相对git checkout). `make dev`目的只是创建快捷方式 `consul` for only your local build environment (no cross-compiled
  targets).

    ```shell
    $ make dev
    ```

## 验证安装成功
验证Consul的是否正确安装，运行 `consul -v`。你应该能看到帮助信息。如果你从命令行执行,需要确保其在PATH环境变量下否则会出现Consul找不到的错误。

```shell
$ consul -v
```