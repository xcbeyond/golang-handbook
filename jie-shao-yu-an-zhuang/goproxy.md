# Go代理goproxy

在逐步编写 Go 程序时，会涉及使用到第三方提供的Go依赖包，如：`github.com/spf13/cobra`，在国内使用 `go get` 命令安装时经常会出现 `timeout` 等问题，实在让人头疼。大家都知道，在国内网络环境下，想要解决这种问题，只有掌握科学上网才能彻底解决。但是xx上网却是需要花费额外成本的，那该怎么办呢？

本文将会介绍一种Go代理 `goproxy` 的方式来解决上述问题，让你既不花钱，又能快速下载安装。

## 1、goproxy.cn 介绍

Go 的 1.11 版本以后可以设置环境变量 `GOPROXY`，来设置代理，以加速下载。目前国内使用最多的是 `goproxy.cn`，号称中国最可靠的Go模块代理。

![goproxy.cn](../.gitbook/assets/goproxy.cn.logo.svg)

它有以下特点：

* **极速下载：** 我们正在使用[七牛云 CDN](https://www.qiniu.com/products/qcdn) 在全球范围内加速我们的服务，并且我们没有设置任何带宽限制。凭借着数以千计的 CDN 节点和 **40Gbps+ 的单节点带宽**，你将被赋予一个以极快的速度下载模块版本的能力。
* **没有限制：** 我们不但没有设置带宽限制。更进一步地，我们还**没有设置任何速率限制**。这意味着你可以在任何场景下使用我们的服务，例如 CI/CD。你甚至可以像 [goproxy.baidu.com](https://goproxy.baidu.com/) 一样将我们的服务用作上游代理。
* **功能齐全：** 我们一直在使用最新的 Go 版本，甚至是不稳定版本。因为我们提供了**最前沿的功能支持**。特别地，我们支持[代理](https://golang.org/design/25530-sumdb#proxying-a-checksum-database)默认的校验和数据库 [sum.golang.org](https://sum.golang.org/)。你不需要做任何额外的事情，它就可以工作。
* **数据可视：** 为了使大家可以更好地了解我们的服务中所有模块的活跃度，我们推出了 Go 模块代理世界中的首个[统计数据 API](https://goproxy.cn/stats)。通过得体的 `RESTful API` 设计，你将能够轻松地查询我们服务中所有模块版本的统计数据。

## 2、goproxy.cn 配置

推荐在 Go 1.13 以上版本使用。

打开你的终端执行下面命令：

```bash
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```

**macOS 或 Linux：**

```bash
export GO111MODULE=on
export GOPROXY=https://goproxy.cn
```

或者

```bash
echo "export GO111MODULE=on" >> ~/.profile
echo "export GOPROXY=https://goproxy.cn" >> ~/.profile
source ~/.profile
```

**Windows：**

打开 `cmd` 命令行，执行：

```bash
C:\> $env:GO111MODULE = "on"
C:\> $env:GOPROXY = "https://goproxy.cn"
```

## 3、小结

有上述 goproxy 代理的设置，`go get` 命令下载将会变得很快，轻松解决了 timeout 问题。此外，也可以通过国外 `goproxy.io` 代理完成加速，配置方法和 `goproxy.cn` 完全一样。

参考文章：

1. [https://goproxy.cn](https://goproxy.cn)
2. [https://www.goproxy.io/zh/](https://www.goproxy.io/zh/)

