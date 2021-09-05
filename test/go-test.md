# go test

在 Java 中有 [Junit](https://junit.org/junit5/)，用于 Java 的单元测试，同理，在 Go 语言中也有类似的测试 testing，它是通过 go test 命令提供了对 Go 语言程序的测试能力。

## 1、go test 命令

`go test` 命令，会自动测试对应包目录下名为 `*_test.go` 的 go 源文件，并打印出测试结果，测试结果如下面的格式：

```sh
   ok   archive/tar   0.011s
   FAIL archive/zip   0.022s
   ok   compress/gzip 0.033s
   ...
```

例如，测试 test 包下的所有单元测试(`*_test.go`):

```sh
xcbeyond@xcbeyonddeMacBook-Pro test % go test
this is go test.
PASS
ok      golangLearning/test     0.373s
```

## 2、Test 函数

Go 语言中测试是基于 golang 标准库 testing 包来支持其自动化测试，并使用 `go test` 命令来执行。

go test 通过扫描 `*_test.go` 文件来识别 go 测试源文件，寻找特殊命名规则的函数，并生成一个临时的 main 包来调用它，然后编译和运行，并返回测试结果，最后清空临时文件。

在 `*_test.go` 测试源文件中，对函数命名有着特殊的要求，其中有三类函数：

- 功能测试函数：又叫做单元测试，以 Test 为前缀命名的函数，如，TestHelloworld 函数，常用来测试一些函数逻辑的正确性，go test 执行后以 PASS 还是 FAIL 返回测试结果。
- 基准测试函数：以 Benchmark 开头的函数，用来测试某些操作的性能，go test 执行后返回平均执行时间。
- 示例函数：以 Example 开头的函数，用来提供机器检查过的文档。

每一个测试文件必须导入 testing 包，其中测试函数格式如下：

```go
func TestFuncName(t *testing.T) {
   // ……
}
```

参数 t 提供了测试处理和日志记录的功能。

如，某个工具 utils.go 中存在一个两数函数 NumAdd，进行单元测试如下

```go
package test

import (
	"testing"
)

func TestNumAdd(t *testing.T) {
	got := NumAdd(1, 1)
	if got != 2 {
		t.Errorf("NumAdd(1,1) = %d, want 2", got)
	}
}

```

go test 执行结果为：

```sh
xcbeyond@xcbeyonddeMacBook-Pro test % go test
PASS
ok      golangLearning/test     0.538s
```
