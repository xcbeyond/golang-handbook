# 包管理工具之Go Module

`Go modules` 是 Go 语言目前最佳的依赖解决方案，发布于 Go 1.11版本，Go1.14版本 上已经明确建议生产上使用。而 `Go modules` 之前，Go 项目使用 `GOPATH` 、`Govendor`包管理方式，但却都存在一定的问题，本文就重点讨论关于另外一个包管理工具 `Go module` 的由来及使用。

## 1、Go module 概述

### 1.1 Go module介绍

使用 `GOPATH` 包管理方式，最严重的问题就是当使用`go ge`t 命令时，没有版本选择机制，拉取下来的依赖代码都会默认当前最新版本，而且如果当项目 A 和项目 B 分别依赖项目 C 的两个不兼容版本时， `GOPATH` 路径下只有一个版本，则 C 将无法同时满足 A 和 B 的依赖需求。这可以说是一个很大的缺陷了，因而 Go 1.13版本 起，官方就不再推荐使用 `GOPATH` 方式了。

随着 Go 语言使用人数的增长，依赖包的丰富，依赖版本问题尤其严重。

于是 Go 官方在 Go 1.5版本的时候提出了实验性质的 `vendor` 机制：每个项目都可以有一个` vendor/` 目录来存放项目所需版本依赖的拷贝。

社区中基于官方给的机制，开发出了各种版本管理工具。比较流行的比如 `govendor`，以及之前曾被官方认定的 `godep` 工具等。

这些工具的思路基本都是为每个项目单独维护一份对应版本依赖的拷贝。

管理工具虽然丰富了起来，但是不同版本工具之间不兼容，无法协作，各种工具还都有学习成本。这时候在 Go 官方扶持下成立的 `dep` 项目被大家认为是未来一统江湖的版本管理工具，被称作 `official experiment`。

`dep` 采用了和 `Rust` 的管理工具 `Cargo` 类似的管理模式，原理在此不深究。

没过多久，Go 社区的核心人物 `rsc` 提出了 `vgo` 方案。一时间竟然出现了两个所谓的 Go 官方的版本管理方案。最终官方采用了 `vgo` 方案，随着 `vgo` 的逐渐成熟，Go 1.11版本发布了该功能，并集成到了 Go 的官方工具中，也就是当前的 `Go modules`。

`Go module` 是Go语言从 1.11 版本之后官方推出的版本管理工具，并且从 Go 1.13 版本开始，`Go module` 成为了Go 语言默认的依赖管理工具。

`Modules` 官方定义为：

`Modules` 是相关 Go 包的集合，是源代码交换和版本控制的单元。Go 语言命令直接支持使用 `Modules`，包括记录和解析对其他模块的依赖性，`Modules` 替换旧的基于 GOPATH 的方法，来指定使用哪些源文件。

### 1.2 Go module 常用命令

| 命令            | 作用                                           |
| --------------- | ---------------------------------------------- |
| `go mod download` | 下载依赖包到本地（默认为 `GOPATH/pkg/mod` 目录） |
| `go mod edit`	| 编辑 `go.mod` 文件 |
| `go mod graph` |	打印模块依赖图 |
| `go mod init`	| 初始化当前文件夹，创建 `go.mod` 文件 |
| `go mod tidy`	| 增加缺少的包，删除无用的包 |
| `go mod vendor`	| 将依赖复制到 `vendor` 目录下 |
| `go mod verify`	| 校验依赖 |
| `go mod why`	| 解释为什么需要依赖 |

## 2、快速入门

### 2.1 设置环境变量

要使用 `Go module` 必须确保 Go 版本在1.11之上。

**设置 `GO111MODULE`**

在Go 1.12 版本之前，要启用 `go module` 工具首先要设置环境变量 `GO111MODULE`，不过在Go 1.13及以后的版本，则不再需要设置环境变量。通过 `GO111MODULE` 可以开启或关闭 `go module` 工具。

* `GO111MODULE=off`  禁用 `go module`，编译时会从 `GOPATH` 和 `vendor` 文件夹中查找包。
* `GO111MODULE=on` 启用 `go module`，编译时会忽略 `GOPATH` 和 `vendor` 文件夹，只根据 `go.mod`下载依赖。
* `GO111MODULE=auto`（默认值），当项目在 `GOPATH/src` 目录之外，并且项目根目录有 `go.mod` 文件时，开启 `go module`。

`Window`：

```sh
set GO111MODULE=on 
// 或者
set GO111MODULE=auto
```

`MacOS` 或 `Linux`：

```sh
export GO111MODULE=on 
// 或者
export GO111MODULE=auto
```

### 2.2 项目初始化

在 `GOPATH` 目录之外新建一个目录，并使用 `go mod init` 初始化生成 `go.mod` 文件。

```sh
E:\github\golangLearning>go mod init golangLearning
go: creating new go.mod: module golangLearning
go: to add module requirements and sums:
        go mod tidy
```

初始化生成的 go.mod 文件如下所示：

```sh
module golangLearning

go 1.16

```

### 2.3 添加依赖

新建一个 `main.go` 文件，代码如下：

```go
package main

import (
    "net/http"
    "github.com/labstack/echo"
)

func main() {
    e := echo.New()
    e.GET("/", func(c echo.Context) error {
        return c.String(http.StatusOK, "Hello, World!")
    })
    e.Logger.Fatal(e.Start(":1323"))
}
```

执行 `go run main.go` 命令运行代码，会发现 `go mod` 会自动查找依赖自动下载：

```sh
E:\github\golangLearning>go run main.go
go: finding module for package github.com/labstack/echo
go: downloading github.com/labstack/echo v1.4.4
go: downloading github.com/labstack/echo v3.3.10+incompatible
go: found github.com/labstack/echo in github.com/labstack/echo v3.3.10+incompatible
go: finding module for package github.com/stretchr/testify/assert
go: finding module for package github.com/labstack/gommon/log
go: finding module for package github.com/labstack/gommon/color
go: finding module for package golang.org/x/crypto/acme/autocert
go: downloading github.com/labstack/gommon v0.3.0
go: downloading golang.org/x/crypto v0.0.0-20210322153248-0c34fe9e7dc2
go: found github.com/labstack/gommon/color in github.com/labstack/gommon v0.3.0
go: found github.com/labstack/gommon/log in github.com/labstack/gommon v0.3.0
go: found golang.org/x/crypto/acme/autocert in golang.org/x/crypto v0.0.0-20210322153248-0c34fe9e7dc2
go: found github.com/stretchr/testify/assert in github.com/stretchr/testify v1.7.0
go: downloading github.com/mattn/go-colorable v0.1.2
go: downloading github.com/valyala/fasttemplate v1.0.1
go: downloading github.com/mattn/go-isatty v0.0.9
go: downloading github.com/davecgh/go-spew v1.1.0
go: downloading gopkg.in/yaml.v3 v3.0.0-20200313102051-9f266ea9e77c
go: downloading golang.org/x/text v0.3.3
go: downloading github.com/valyala/bytebufferpool v1.0.0
go: downloading golang.org/x/sys v0.0.0-20201119102817-f84b799fce68
   ____    __
  / __/___/ /  ___
 / _// __/ _ \/ _ \
/___/\__/_//_/\___/ v3.3.10-dev
High performance, minimalist Go web framework
https://echo.labstack.com
____________________________________O/_______
                                    O\
⇨ http server started on [::]:1323
exit status 3221225786
```

再查看 `go.mod` 文件：

```go
module golangLearning

go 1.16

require (
	github.com/labstack/echo v3.3.10+incompatible
	github.com/labstack/gommon v0.3.0 // indirect
	github.com/stretchr/testify v1.7.0 // indirect
	golang.org/x/crypto v0.0.0-20210322153248-0c34fe9e7dc2 // indirect
)

```

此外，会自动生成一个 `go.sum` 文件来记录 dependency tree：

```go
github.com/davecgh/go-spew v1.1.0 h1:ZDRjVQ15GmhC3fiQ8ni8+OwkZQO4DARzQgrnXU1Liz8=
github.com/davecgh/go-spew v1.1.0/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
github.com/labstack/echo v3.3.10+incompatible h1:pGRcYk231ExFAyoAjAfD85kQzRJCRI8bbnE7CX5OEgg=
github.com/labstack/echo v3.3.10+incompatible/go.mod h1:0INS7j/VjnFxD4E2wkz67b8cVwCLbBmJyDaka6Cmk1s=
github.com/labstack/gommon v0.3.0 h1:JEeO0bvc78PKdyHxloTKiF8BD5iGrH8T6MSeGvSgob0=
github.com/labstack/gommon v0.3.0/go.mod h1:MULnywXg0yavhxWKc+lOruYdAhDwPK9wf0OL7NoOu+k=
github.com/mattn/go-colorable v0.1.2 h1:/bC9yWikZXAL9uJdulbSfyVNIR3n3trXl+v8+1sx8mU=
github.com/mattn/go-colorable v0.1.2/go.mod h1:U0ppj6V5qS13XJ6of8GYAs25YV2eR4EVcfRqFIhoBtE=
github.com/mattn/go-isatty v0.0.8/go.mod h1:Iq45c/XA43vh69/j3iqttzPXn0bhXyGjM0Hdxcsrc5s=
github.com/mattn/go-isatty v0.0.9 h1:d5US/mDsogSGW37IV293h//ZFaeajb69h+EHFsv2xGg=
github.com/mattn/go-isatty v0.0.9/go.mod h1:YNRxwqDuOph6SZLI9vUUz6OYw3QyUt7WiY2yME+cCiQ=
github.com/pmezard/go-difflib v1.0.0 h1:4DBwDE0NGyQoBHbLQYPwSUPoCMWR5BEzIk/f1lZbAQM=
github.com/pmezard/go-difflib v1.0.0/go.mod h1:iKH77koFhYxTK1pcRnkKkqfTogsbg7gZNVY4sRDYZ/4=
github.com/stretchr/objx v0.1.0/go.mod h1:HFkY916IF+rwdDfMAkV7OtwuqBVzrE8GR6GFx+wExME=
github.com/stretchr/testify v1.4.0/go.mod h1:j7eGeouHqKxXV5pUuKE4zz7dFj8WfuZ+81PSLYec5m4=
github.com/stretchr/testify v1.7.0 h1:nwc3DEeHmmLAfoZucVR881uASk0Mfjw8xYJ99tb5CcY=
github.com/stretchr/testify v1.7.0/go.mod h1:6Fq8oRcR53rry900zMqJjRRixrwX3KX962/h/Wwjteg=
github.com/valyala/bytebufferpool v1.0.0 h1:GqA5TC/0021Y/b9FG4Oi9Mr3q7XYx6KllzawFIhcdPw=
github.com/valyala/bytebufferpool v1.0.0/go.mod h1:6bBcMArwyJ5K/AmCkWv1jt77kVWyCJ6HpOuEn7z0Csc=
github.com/valyala/fasttemplate v1.0.1 h1:tY9CJiPnMXf1ERmG2EyK7gNUd+c6RKGD0IfU8WdUSz8=
github.com/valyala/fasttemplate v1.0.1/go.mod h1:UQGH1tvbgY+Nz5t2n7tXsz52dQxojPUpymEIMZ47gx8=
golang.org/x/crypto v0.0.0-20210322153248-0c34fe9e7dc2 h1:It14KIkyBFYkHkwZ7k45minvA9aorojkyjGk9KJ5B/w=
golang.org/x/crypto v0.0.0-20210322153248-0c34fe9e7dc2/go.mod h1:T9bdIzuCu7OtxOm1hfPfRQxPLYneinmdGuTeoZ9dtd4=
golang.org/x/net v0.0.0-20210226172049-e18ecbb05110 h1:qWPm9rbaAMKs8Bq/9LRpbMqxWRVUAQwMI9fVrssnTfw=
golang.org/x/net v0.0.0-20210226172049-e18ecbb05110/go.mod h1:m0MpNAwzfU5UDzcl9v0D8zg8gWTRqZa9RBIspLL5mdg=
golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223/go.mod h1:STP8DvDyc/dI5b8T5hshtkjS+E42TnysNCUPdjciGhY=
golang.org/x/sys v0.0.0-20190813064441-fde4db37ae7a/go.mod h1:h1NjWce9XRLGQEsW7wpKNCjG9DtNlClVuFLEZdDNbEs=
golang.org/x/sys v0.0.0-20201119102817-f84b799fce68 h1:nxC68pudNYkKU6jWhgrqdreuFiOQWj1Fs7T3VrH4Pjw=
golang.org/x/sys v0.0.0-20201119102817-f84b799fce68/go.mod h1:h1NjWce9XRLGQEsW7wpKNCjG9DtNlClVuFLEZdDNbEs=
golang.org/x/term v0.0.0-20201126162022-7de9c90e9dd1/go.mod h1:bj7SfCRtBDWHUb9snDiAeCFNEtKQo2Wmx5Cou7ajbmo=
golang.org/x/text v0.3.3 h1:cokOdA+Jmi5PJGXLlLllQSgYigAEfHXJAERHVMaCc2k=
golang.org/x/text v0.3.3/go.mod h1:5Zoc/QRtKVWzQhOtBMvqHzDpF6irO9z98xDceosuGiQ=
golang.org/x/tools v0.0.0-20180917221912-90fa682c2a6e/go.mod h1:n7NCudcB/nEzxVGmLbDWY5pfWTLqBcC2KZ6jyYvM4mQ=
gopkg.in/check.v1 v0.0.0-20161208181325-20d25e280405/go.mod h1:Co6ibVJAznAaIkqp8huTwlJQCZ016jof/cbN4VW5Yz0=
gopkg.in/yaml.v2 v2.2.2/go.mod h1:hI93XBmqTisBFMUTm0b8Fm+jr3Dg1NNxqwp+5A1VGuI=
gopkg.in/yaml.v3 v3.0.0-20200313102051-9f266ea9e77c h1:dUUwHk2QECo/6vqA44rthZ8ie2QXMNeKRTHCNY2nXvo=
gopkg.in/yaml.v3 v3.0.0-20200313102051-9f266ea9e77c/go.mod h1:K4uyk7z7BCEPqu6E+C64Yfv1cQ7kz7rIZviUmN+EgEM=

```

## 3、小结

至此，是不是感觉`Go module` 很好用，再也不用依赖 `GOPATH`了，灵活方便。