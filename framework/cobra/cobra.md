# Cobra介绍

最近一直在看 [Istio](https://istio.io/)（一个 `Service Mesh` 框架）相关的东西，当看到其源码时发现了一个新东西 [`Cobra`](https://github.com/spf13/cobra)，一查却发现这是个好东西，用的地方可不少，比如：`Docker`、`Kubernetes` 等都有它的身影存在。为了更好的了解这些开源框架（如，`Istio`、`Kubernetes` 等），势必需要对 `Cobra` 做一个详细的了解，后续可能用到它的地方会很多。今天就 `Cobra` 做一个整体的介绍，让我们对它能有所认识，或许今后你的项目中也会用到它。

## 1、Cobra 概述

`Cobra` 是一个 Golang 包，它提供了简单的接口来创建命令行程序。同时，`Cobra` 也是一个应用程序，用来生成应用框架，从而开发以 `Cobra` 为基础的应用。

![Cobra](Cobra.png)

### 1.1 主要功能

Cobra 的主要功能如下：

* 简易的子命令行模式，如 `app server`， `app fetch` 等等。
* 完全兼容 `posix` 命令行模式。
* 嵌套子命令 `subcommand`。
* 支持全局，局部，串联 `flags`。
* 使用 `cobra` 很容易生成应用程序和命令（`cobra init appname` 和 `cobra add cmdname`）。
* 提供智能化的提示（如，输出 `app srver` 命令，将提示 你是要输入 `app server` 吗？）。
* 自动生成 `commands` 和 `flags` 的帮助信息。
* 自动生成详细的 `help` 信息，如 `app -help`。
* 自动识别帮助 `flag`、 `-h`，`--help`。
* 自动生成应用程序在 `bash` 下命令自动完成功能。
* 自动生成应用程序的 `man` 手册。
* 命令行别名。
* 自定义 `help` 和 `usage` 信息。
* 可选的与 [`viper`](http://github.com/spf13/viper) 的紧密集成。

对于命令行程序而言，上面这些功能简直就是量身打造。

### 1.2 应用举例

`Cobra` 被用于许多 Go 项目中，例如：[Kubernetes](http://kubernetes.io/)、[Hugo](https://gohugo.io/)和[Github CLI](https://github.com/cli/cli)等，更多广泛使用的项目有：

（来源于：[https://github.com/spf13/cobra/blob/master/projects_using_cobra.md](https://github.com/spf13/cobra/blob/master/projects_using_cobra.md)）

* [Arduino CLI](https://github.com/arduino/arduino-cli)
* [Bleve](http://www.blevesearch.com/)
* [CockroachDB](http://www.cockroachlabs.com/)
* [Cosmos SDK](https://github.com/cosmos/cosmos-sdk)
* [Delve](https://github.com/derekparker/delve)
* [Docker (distribution)](https://github.com/docker/distribution)
* [Etcd](https://etcd.io/)
* [Gardener](https://github.com/gardener/gardenctl)
* [Giant Swarm's gsctl](https://github.com/giantswarm/gsctl)
* [Git Bump](https://github.com/erdaltsksn/git-bump)
* [Github CLI](https://github.com/cli/cli)
* [GitHub Labeler](https://github.com/erdaltsksn/gh-label)
* [Golangci-lint](https://golangci-lint.run/)
* [GopherJS](http://www.gopherjs.org/)
* [Helm](https://helm.sh/)
* [Hugo](https://gohugo.io/)
* [Istio](https://istio.io/)
* [Kool](https://github.com/kool-dev/kool)
* [Kubernetes](http://kubernetes.io/)
* [Linkerd](https://linkerd.io/)
* [Mattermost-server](https://github.com/mattermost/mattermost-server)
* [Metal Stack CLI](https://github.com/metal-stack/metalctl)
* [Moby (former Docker)](https://github.com/moby/moby)
* [Nanobox](https://github.com/nanobox-io/nanobox)/[Nanopack](https://github.com/nanopack)
* [OpenShift](https://www.openshift.com/)
* [Ory Hydra](https://github.com/ory/hydra)
* [Ory Kratos](https://github.com/ory/kratos)
* [Pouch](https://github.com/alibaba/pouch)
* [ProjectAtomic (enterprise)](http://www.projectatomic.io/)
* [Prototool](https://github.com/uber/prototool)
* [Random](https://github.com/erdaltsksn/random)
* [Rclone](https://rclone.org/)
* [Skaffold](https://skaffold.dev/)
* [Tendermint](https://github.com/tendermint/tendermint)
* [Twitch CLI](https://github.com/twitchdev/twitch-cli)
* [Werf](https://werf.io/)

看了这些，一个字“赞”，两个字“优秀”!

了解了 `Cobra` 后，再去看这些 `Kubernetes`、`etcd`、`Registry` 等开源项目的代码时，也就大概知道如何去看了，这也就是我学习 `Cobra` 的目的。

## 2、概念

Cobra 是基于命令（`commands`）、参数（`arguments` ）、选项（`flags`）而创建的。

在具体了解、使用Cobra前有一些概念需要提前知晓一下：命令（`commands`）、参数（`arguments` ）、选项（`flags`）这几个概念。

* **commands**：命令代表行为,一般表示 action，即运行的二进制命令服务。同时可以拥有子命令（children commands）
* **arguments**：参数代表命令行参数。
* **flags**：选项代表对命令行为的改变，即命令行选项。二进制命令的配置参数，可对应配置文件。参数可分为全局参数和子命令参数。

最好的命令行程序在实际使用时，就应该像在读一段优美的语句，能够更加直观的知道如何与用户进行交互。执行命令行程序应该遵循一般的格式： `APPNAME VERB NOUN --ADJECTIVE  `或 `APPNAME COMMAND ARG --FLAG`。

比如下面的示例:

```sh
# server是 commands，port 是 flag
hugo server --port=1313

# clone 是 commands，URL 是 arguments，brae 是 flag
git clone URL --bare
```

再比如：

```sh
xcbeyond@xcbeyonddeMacBook-Pro ~ % docker info --help 

Usage:  docker info [OPTIONS]

Display system-wide information

Options:
  -f, --format string   Format the output using the given Go template

````

你没有看错，像 Docker 这种这么复杂的命令都是用 Cobra 是实现的，其必有过人之处，下一节就让我们一起来看看如何实现一套属于自己的命令行工具吧！