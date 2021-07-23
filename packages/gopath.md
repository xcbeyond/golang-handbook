# 包管理工具之GOPATH

当我们真正用 Go 去做项目，或者阅读 Go 项目（如，Go 实现的开源项目）时，不可避免的会遇到包依赖问题，一些包管理方式总是很难区分、选择。Go的包管理与Java的Maven依赖管理不太一样，起初Go的包管理方式经常会被人吐槽，但随之Go版本的升级也出现了不同的包管理方式，以满足不同的需求。

今天，我们就 `GOPATH` 开始来了解 Go 最基本的包管理方式，让你彻底认清它。（虽然很简单，但作为系列文章还是有必然单独说一下的）

## 1、GOPATH

`GOPATH`, 被称之为“工作目录”，是 Go 语言中使用的一个环境变量，它使用绝对路径提供Go项目的绝对路径，类似于 `eclipse` 中的工作空间 `workspace`。

工作目录是一个工程开发的相对参考目录，好比当你要在公司编写一套服务器代码，你的工位所包含的桌面、计算机及椅子就是你的工作区。工作区的概念与工作目录的概念也是类似的。如果不使用工作目录的概念，在多人开发时，每个人有一套自己的目录结构，读取配置文件的位置不统一，输出的二进制运行文件也不统一，这样会导致开发的标准不统一，影响开发效率。

`GOPATH` 适合处理大量 Go语言源码、多个包组合而成的复杂工程。

### 1.1 GOPATH的基本使用

其实早在[1.2 Go语言从入门到精通：开发环境搭建](./1.2 Go语言从入门到精通：开发环境搭建.md)一文中已经涉及到了 `GOPATH`，GOPATH的配置在安装时已经设置过了。

**查看**

因GOPATH作为一个环境变量而存在，可直接输入命令 `go env`来查看：

```sh
C:\Users\Administrator>go env
…省略其它…
set GOPATH=E:\github
…省略其它…
```

从命令行输出中，可以看到 `GOPATH` 设定的路径为：`E:\github`。（该GOPATH是我手动修改过的）

在 Go 1.8 版本之前，`GOPATH` 环境变量默认为空。从 Go 1.8 版本开始，Go 开发包在安装完成后，将 `GOPATH` 赋予了一个默认的目录。

**设置**

选择一个目录，执行命令`set GOPATH=<GOPATH>`：

```sh
C:\Users\Administrator>set GOPATH=E:\github
```

Linux环境下，执行命令 `export GOPATH=<GOPATH>`。

### 1.2 GOPATH工程结构

在`GOPATH`目录下会存在三个文件夹：

| 目录 | 描述                                             |
| ---- | ------------------------------------------------ |
| src  | 代码，可以创建多个不同的包，手动创建对应包目录。 |
| bin  | 编译代码生成的二进制文件。                       |
| pkg  | 编译生成的中间缓存文件。                         |

`bin`、`pkg` 是在编译时（如，执行`go build`、`go install` 、`go get`等命令）创建。

例如：

```sh
bin/
    hello                          # command executable
    outyet                         # command executable
pkg/
	……
src/
    github.com/golang/example/
        .git/                      # Git repository metadata
	hello/
	    hello.go               # command source
	outyet/
	    main.go                # command source
	    main_test.go           # test source
	stringutil/
	    reverse.go             # package source
	    reverse_test.go        # test source
    golang.org/x/image/
        .git/                      # Git repository metadata
	bmp/
	    reader.go              # package source
	    writer.go              # package source
```

### 1.3 GOPATH和GOROOT的区别

不同于其他语言，Go 语言中没有项目的说法，只有包的概念。其中，`GOROOT` 和 `GOPATH` 很容易被大家混为一谈。

* `GOROOT`： Go 语言的安装目录。（类似于 Java 的 `JDK`，存放的是一些内置包、编译工具等）
* `GOPATH`：我们的工作空间，即：保存 Go 的代码和第三方依赖包。

## 2、小结

`GOPATH` 是最早的依赖包管理方式，是将所有的本地代码和第三方包代码均放在 `GOPATH/src` 目录下，它的优点是统一管理，方便查询和引用。

但是在使用过程中会有一个非常严重的问题，那就是由于所有不同的项目都放在 `GOPATH/src` 目录下，当多个不同项目引用的是同一个第三方包，但是该第三包的不同版本时，便会导致严重的包依赖问题。

因此，这也是 Go 语言之初被诟病的一个点，那么该如何解决上述问题呢？