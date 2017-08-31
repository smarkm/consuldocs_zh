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

To compile from source, you will need [Go](https://golang.org) installed and
configured properly (including a `GOPATH` environment variable set), as well as
a copy of [`git`](https://www.git-scm.com/) in your `PATH`.

  1. Clone the Consul repository from GitHub into your `GOPATH`:

    ```shell
    $ mkdir -p $GOPATH/src/github.com/hashicorp && cd $!
    $ git clone https://github.com/hashicorp/consul.git
    $ cd consul
    ```

  1. Bootstrap the project. This will download and compile libraries and tools
  needed to compile Consul:

    ```shell
    $ make bootstrap
    ```

  1. Build Consul for your current system and put the binary in `./bin/`
  (relative to the git checkout). The `make dev` target is just a shortcut that
  builds `consul` for only your local build environment (no cross-compiled
  targets).

    ```shell
    $ make dev
    ```

## Verifying the Installation

To verify Consul is properly installed, run `consul -v` on your system. You
should see help output. If you are executing it from the command line, make sure
it is on your PATH or you may get an error about Consul not being found.

```shell
$ consul -v
```