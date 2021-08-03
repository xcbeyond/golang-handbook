# Gin 介绍

## 1、Gin 概述

[Gin](https://github.com/gin-gonic/gin) 是一个基于 Go 语言编写的 Web 框架，与 [martini](https://github.com/go-martini/martini) 框架类似，但拥有更好的性能，借助高性能的 [httprouter](https://github.com/julienschmidt/httprouter)，速度提升了近 40 倍。如果你追求高性能和开发效率，你会爱上 Gin 框架。

![Gin](gin.png)

### 1.1 功能特性

* **快速**

  * 基于[Radox树](https://zh.wikipedia.org/wiki/%E5%9F%BA%E6%95%B0%E6%A0%91)（一种更节省空间的 Trie 树结构）的路由，内存占用更小。
  * 没有反射。
  * 可预测的 API 性能。

* **中间件支持**
  
   传入的 HTTP 请求可以由中间件链和最终操作处理。例如：Logger、Authorization、GZIP 以及最终的 DB 操作。

* **Crash 处理**
  
   Gin 框架可以捕获一个发生在 HTTP 请求中的 panic 并 recover 它，从而保证服务器始终可用。此外，你还可以向 [Sentry](https://sentry.io/) 报告这个 panic！

* **JSON 验证**
  
   Gin 可以解析和验证请求的 JSON，例如检查有个必须值是否存在。

* **路由分组**
  
   支持通过路由分组来更好地组织路由，例如是否需要授权、设置 API 的版本等，此外，这些分组可以无限制地嵌套而不会降低性能。

* **错误管理**

   Gin 框架提供了一种方便的机制来收集 HTTP 请求期间发生的所有错误，并且最终通过中间件将它们写入日志文件、数据库或者通过网络发送到其它系统。

* **内置渲染**

   Gin 框架提供了简单易上手的 API， 来返回 JSON、XML 或者 HTML 格式的响应。

* **可扩展**

   我们将会在后续示例代码中看到 Gin 框架非常容易扩展。

### 1.2 应用举例

以下项目都是使用 Gin 框架开发的：

* [gorush](https://github.com/appleboy/gorush)：Go 编写的通知推送服务器。
* [fnproject](https://github.com/fnproject/fn)：容器原生，云 serverless 平台。
* [photoprism](https://github.com/photoprism/photoprism)：基于 Go 和 Google TensorFlow 实现的个人照片管理工具。
* [krakend](https://github.com/devopsfaith/krakend)：拥有中间件的超高性能 API 网关。
* [picfit](https://github.com/thoas/picfit)：Go 编写的图像尺寸调整服务器。
* [gotify](https://github.com/gotify/server)：基于 WebSocket 进行实时消息收发的简单服务器。
* [cds](https://github.com/ovh/cds)：企业级持续交付和 DevOps 自动化开源平台。

## 2、快速入门

### 2.1 安装

1. 准备 Golang 环境
2. 安装 Gin

   ```go
   go get -u github.com/gin-gonic/gin
   ```

### 2.2 示例

1. 创建示例工程目录 gin-demo

2. 新建 `gin.go` 源文件：

   ```go
   package main

   import (
      "github.com/gin-gonic/gin"
   )

   func main() {
      // 初始化gin对象
      g := gin.Default()

      // 设置一个get请求，其URL为/hello，并实现简单的响应
      g.GET("/hello", func(c *gin.Context) {
         c.JSON(200, gin.H{
            "message": "hello world!",
         })
      })

      // 启动服务
      g.Run()
   }

   ```

3. go mod 导入依赖

   项目 gin-demo 目录下，执行 `go mod init gin-demo` 和 `go mod tidy` 命令完成依赖导入，将自动生产 go.mod 、go.sum 文件。

   ```sh
   xcbeyond@xcbeyonddeMacBook-Pro gin-demo % go mod init gin-demo
   go: creating new go.mod: module gin
   go: to add module requirements and sums:
         go mod tidy
   xcbeyond@xcbeyonddeMacBook-Pro gin-demo % go mod tidy
   go: finding module for package github.com/gin-gonic/gin
   go: found github.com/gin-gonic/gin in github.com/gin-gonic/gin v1.7.3
   go: downloading github.com/stretchr/testify v1.4.0
   go: downloading github.com/go-playground/assert/v2 v2.0.1
   ```

   go.mod:

   ```go
   module gin-demo

   go 1.16

   require github.com/gin-gonic/gin v1.7.3

   ```

4. 运行

   gin-demo 目录执行命令 `go run gin.go` 启动 gin Web Server：

   ```sh
   xcbeyond@xcbeyonddeMacBook-Pro gin-demo % go run gin.go
   [GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

   [GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
   - using env:   export GIN_MODE=release
   - using code:  gin.SetMode(gin.ReleaseMode)

   [GIN-debug] GET    /hello                    --> main.main.func1 (3 handlers)
   [GIN-debug] Environment variable PORT is undefined. Using port :8080 by default
   [GIN-debug] Listening and serving HTTP on :8080
   ```

   此时，Web Server 监听 8080 端口，访问 http://localhost:8080/hello, 则会响应：

   ```json
   {"message":"hello world!"}
   ```

## 3、总结

Gin 的入门很简单，简单几行代码就可以启动一个 Web Server，并且还有非常完善的组件等，接下来的文章将会对其一些细节展开具体说明。
