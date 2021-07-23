# 包管理工具之Go Vendor

`Govendor` 是 Go 的另一个依赖包管理工具，它的出现可以避免不同用户在 `clone` 同一个工程后从外部获取不同依赖库版本的问题，很好的弥补了 `GOPATH` 包管理的缺陷。`govendor` 会将工程需要的依赖包添加到工程的 `vendor` 目录下，并且该目录中 `vendor.json` 文件会保存添加的依赖包的路径等信息。

（出于知识体系的完整性以及对各类包管理工具的全面了解，本文重点只是介绍`Govendor` 的存在，并不推荐使用它，仅作了解即可。）

## 1、Govendor 概述

Go 工程的依赖包经常使用 `go get` 命令来获取，例如：`go get  github.com/spf13/cobra/cobra` ，会将依赖包下载到 `GOPATH` 的路径下。

随着 Go 的不断升级，在 Go 1.5版本之后，Go 提供了 `GO15VENDOREXPERIMENT` 环境变量(Go 1.6版本默认开启该环境变量)和 `Govendor` 包管理工具，用于将 `go build` 时的应用路径搜索调整成为当前工程`/vendor` 目录的方式，有效的解决了不同工程使用自己独立的依赖包目录。

### 1.1 Govendor介绍

[`Govendor`](https://github.com/kardianos/govendor) 是 Go 1.5版本之后引入包管理工具。

其基本思路是，将引用的外部包的源代码放在当前工程的 `vendor`目录下面（类似于 `Nodejs` 的 `node_modules` 目录），Go 1.5 以后编译 Go 代码会优先从`vendor`目录先寻找依赖包，`vendor`目录如果没有找到，然后在 `GOPATH` 中查找，都没找到最后在 `GOROOT` 中查找。

**特点：**

* 可以采用 `govendor add/update`  从`$GOPATH` 复制现有的依赖。
* 如果要忽视 `vendor/*/`，可采用 `govendor` 同步恢复依赖。
* 可直接通过 `govendor fetch` 控制器添加新的依赖或者更新现有依赖。
* 可采用 `govendor migrate ` 实现系统间迁移。
* 支持 `Linux`,  `OS X`,  `Windows`，甚至现有所有操作系统。
* 支持`Git`，`Hg`，`SVN`，`BZR`（必须指定一个路径）。

### 1.2 解决的问题

* 不同版本依赖问题：解决不同用户在 `clone` 同一个工程后从外部获取不同依赖库版本的问题。
* 迁移问题：将 Go 源码拷贝到当前工程的 `vendor`目录下，这样打包当前的工程代码到任意机器的 `$GOPATH/src`下都可以通过编译，避免项目代码外部依赖过多。迁移后， 无需再次 `go get` 拉取外部依赖包，况且再次 `go get` 重新拉取的外部依赖包的版本可能和工程开发时使用的不一致，将会导致编译错误问题。

### 1.3 常用命令

`govendor` 提供了很多命令供大家使用，下面列举了一些常用命令，更多命令可通过 `govendor --help` 查看。

| 命令     | 功能                                                         |
| -------- | ------------------------------------------------------------ |
| `init`   | 初始化 `vendor` 目录                                         |
| `list`   | 列出所有的依赖包                                             |
| `add`    | 添加包到 `vendor` 目录，如 `govendor add +external` 添加所有外部包 |
| `update` | 从 `$GOPATH` 更新依赖包到 `vendor` 目录                      |
| `remove` | 从 `vendor` 管理中删除依赖                                   |
| `status` | 列出所有缺失、过期和修改过的包                               |
| `fetch`  | 添加或更新包到本地 `vendor` 目录                             |
| `sync`   | 本地存在 `vendor.json` 时候拉去依赖包，匹配所记录的版本      |
| `get`    | 类似 `go get` 目录，拉取依赖包到 `vendor` 目录               |

## 2、快速入门

### 2.1 安装

使用 `go get`命令快速安装：

```sh
go get -u -v github.com/kardianos/govendor
```

> 建议将 `$GOPATH/bin` 加入到 `PATH` 中，方便直接使用 `govendor` 命令。

### 2.2 初始化

进入项目根目录下执行 `govendor init` 命令，会在项目根目录下自动新建一个 `vendor` 目录，并在其中生成`vendor.json`(存储包的版本信息)

```sh
govendor init
```

### 2.3 拉取包

使用 `govendor fetch` 或 `govendor get` 命令远程拉取外部依赖包。 例如：

```sh
govendor fetch github.com/spf13/cobra/cobra
```

## 3、小结

本文主要是讲解了一下 `Govendor` 的存在以及如何使用，但随着Go 的不断壮大，随之被 `Go modules` 而取代了，所以不推荐使用 `Govendor`。下一篇文章将会对 `Go modules` 展开说明，它将作为目前包管理工具的首选，也是必选。