# Cobra 实战

**实战是最好的学习方式！** 本节将从一个实战带你快速入门 Cobra。

## 环境准备

### 前提条件&环境

* 操作系统：MacOS
* Go 环境：go 1.16
* Go 开发 IDE：VSCode

(上述环境可以个人环境而定，不做限制)

### Cobra 安装

使用 `go get` 命令获取最新版本的 Cobra 库。下面命令将会安装 Cobra 及其相关依赖包：

```sh
go get -u github.com/spf13/cobra/cobra
```

下载安装完 Cobra 后，打开 GOPATH 目录，在 bin 目录下会下载好 cobra 程序（Window: cobra.exe, MacOS: cobra）。

### 工程初始化

假设现需要开发一个基于 Cobra 的 CLI 的命令行程序，命名为 `cobra-demo`。

由于 Cobra 提供了代码生成器的功能，我们可以直接使用 Cobra 提供的初始化命令 `cobra init` 进行快速初始化创建 Cobra 工程。

切换到 `GOPATH` 目录（如，`/Users/xcbeyond/github`），执行命令 `cobra init <name> --pkg-name <pkgname>`,如下：

```sh
xcbeyond@xcbeyonddeMacBook-Pro github % .\bin\cobra init cobra-demo --pkg-name github.com/xcbeyond/cobra-demo
Your Cobra application is ready at
/Users/xcbeyond/github/cobra-demo
```

初始化成功后，cobra-demo 程序目录结构如下：

```sh
.
├── cmd
│   ├── root.go
│   └── show.go
├── go.mod
├── go.sum
└── main.go
```

### 运行

执行命令 `go run` 运行：

```sh
xcbeyond@xcbeyonddeMacBook-Pro cobra-demo % go run main.go
A longer description that spans multiple lines and likely contains
examples and usage of using your application. For example:

Cobra is a CLI library for Go that empowers applications.
This application is a tool to generate the needed files
to quickly create a Cobra application.
```

## 实战

这里以一个简单的 `time` 命令为例，实战讲解如何 Cobra 开发一个命令。

功能如下：

* `show`：查看当前时间。
* `parse`：指定时间格式 --format，parse 为 show 的子命令。

### 实现 show 命令

1. 添加 show 命令

   通过命令 `cobra add` 添加 show 命令：

   ```sh
	xcbeyond@xcbeyonddeMacBook-Pro cobra-demo % ../bin/cobra add show
	show created at /Users/xcbeyond/github/cobra-demo
	```

	此时，项目目录下会创建一个 `show.go` 文件，在该文件中可完成命令的具体操作逻辑。

	下面是 `show.go` 文件的初始代码：

	```go
	// showCmd represents the show command
	var showCmd = &cobra.Command{
		Use:   "show",
		Short: "A brief description of your command",
		Long: `A longer description that spans multiple lines and likely contains examples
	and usage of using your command. For example:

	Cobra is a CLI library for Go that empowers applications.
	This application is a tool to generate the needed files
	to quickly create a Cobra application.`,
		Run: func(cmd *cobra.Command, args []string) {
			fmt.Println("show called")
		},
	}

	func init() {
		rootCmd.AddCommand(showCmd)

		// Here you will define your flags and configuration settings.

		// Cobra supports Persistent Flags which will work for this command
		// and all subcommands, e.g.:
		// showCmd.PersistentFlags().String("foo", "", "A help for foo")

		// Cobra supports local flags which will only run when this command
		// is called directly, e.g.:
		// showCmd.Flags().BoolP("toggle", "t", false, "Help message for toggle")
	}
	```

	`&cobra.Command` 作为命令的定义，其中有如下变量：

	* `Use`：用于指定具体的命令，如：show。
	* `Short`：命令的简短描述。
	* `Long`：命令的详细描述。
   * `Run`：命令执行入口，用于实现命令的具体处理逻辑。

   `rootCmd.AddCommand(showCmd)` 命令的添加，将命令添加到根命令。（Cobra 支持命令的子命令）

2. 实现显示当前时间逻辑

	在 &cobra.Command.Run 中添加获取当前时间逻辑 `time.Now()`：

	```go
	var showCmd = &cobra.Command{
		Use:   "show",
		Short: "A brief description of your command",
		Long: `A longer description that spans multiple lines and likely contains examples
	and usage of using your command. For example:

	Cobra is a CLI library for Go that empowers applications.
	This application is a tool to generate the needed files
	to quickly create a Cobra application.`,
		Run: func(cmd *cobra.Command, args []string) {
			// show current time
			fmt.Println(time.Now())
		},
	}
	```

3. 修改 help 命令

	help 命令有两个，一个是 short，一个是 lang，很明显 short 命令用来定义简短的说明，lang 命令用来定义详细说明，下面我们修改 show 命令的 help:

	```go
	var showCmd = &cobra.Command{
		Use:   "show",
		Short: "Displays the current time",
		Long: `You can use the time show command to view the current time. For example:

	$ ./cobra-demo show
	2021-03-19 14:34:20.9320241 +0800 CST m=+0.378845301`,
		Run: func(cmd *cobra.Command, args []string) {
			// show current time
			fmt.Println(time.Now())
		},
	}
	```

4. 运行

	执行 show 命令：

	```sh
	xcbeyond@xcbeyonddeMacBook-Pro cobra-demo % go run main.go show
	2021-07-31 14:49:27.3582836 +0800 CST m=+0.176660901
	```

	执行 show --help 命令：

	```sh
	xcbeyond@xcbeyonddeMacBook-Pro cobra-demo % go run main.go show --help
	You can use the time show command to view the current time. For example:

	$ ./cobra-demo show
	2021-07-31 14:34:20.9320241 +0800 CST m=+0.378845301

	Usage:
	cobra-demo show [flags]

	Flags:
	-h, --help   help for show

	Global Flags:
			--config string   config file (default is $HOME/.cobra-demo.yaml)
	```

## 总结

Cobra-demo 只是简单的阐述了由几部分组成，在实际项目中要比这复杂的很多，一般都是拥有多个子命令，但核心内容都是类似的。
