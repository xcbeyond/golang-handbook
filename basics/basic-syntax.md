# 基础语法

与其他编程语言一样，Go 语言项目（大程序）都是从基本组件和基本语法编写而成。上一章节我们已经了解了 Go 语言的样子，本章节我们将学习 Go 语言的基础语法，也将是接下来所有 Go 程序中不可或缺的部分。

## 1、命名规则

Go 中类型、变量、常量、语句标签、包和函数的命名都遵循一个简单的规则：**命名的开头是一个字母或下划线而不能是数字，后面可以跟任意数量的字符、数字和下划线，并区分大小写。** 如, xcbeyond 和 xcBeyond 是不同的标识符。

以下是有效的标识符：

```go
mahesh   kumar   abc   move_name   a_123
myname50   _temp   j   a23b9   retVal
```

以下是无效的标识符：

- `1ab`：以数字开头
- `case`：Go 语言的关键字
- `a+b`：运算符是不允许的

## 2、关键字

Go 语言和其他语言一样，也有一些关键词供程序使用。Go 中有 25 个关键字或保留字：

```go
 break     default      func    interface  select
 case      defer        go      map        struct
 chan      else         goto    package    switch
 const     fallthrough  if      range      type
 continue  for          import  return     var
```

除了以上介绍的这些关键字，Go 语言还有 36 个预定义标识符：

```go
append	bool	byte	cap	    close	complex	complex64	complex128	uint16
copy	false	float32	float64	imag	int	    int8	    int16	    uint32
int32	int64	iota	len	    make	new	    nil	        panic	    uint64
print	println	real	recover	string	true	uint	    uint8	    uintptr
```

## 3、注释

注释，是为了增强代码的可读性，但不参与程序的一切功能，不会被编译。每个程序都应该在适当的位置添加注释，用于功能、实现、标注等说明，如果你不想不其他人唾弃，请一定养成编写注释的习惯，注释和代码同样重要。

Go 语言的注释主要分为两类：

* 单行注释：你可以在任何地方使用以 `//` 开头的单行注释，是最常见的注释形式。
* 多行注释：也叫块注释，以 `/*` 开头，并以 `*/` 结尾。

如下所示：

```go
package main

import "fmt"

func main() {
    // "Hello World!" Go语言程序
    fmt.Println("Hello World!")

    // 这是一个单行注释

    /*
       这是一个多行注释
       by xcbeyond
    */
}
```

## 4、行分隔符

在 Go 程序中，一行代表一个语句结束。每个语句不需要像 Java 等其它语言一样以分号 `;` 结尾，因为这些工作都将由 Go 编译器自动完成。

如果你打算将多个语句写在同一行，则必须使用 `;` 人为区分，但在实际开发中我们并不鼓励这种做法。

以下为两个语句：

```go
fmt.Println("Hello, World!")
fmt.Println("Hello, xcbeyond!")
```

## 5、字符串输出

Go 语言的字符串输出可以通过函数 `fmt.Println` 或 `fmt.Print` 实现，`fmt.Println` 会输出后换行，类似于 Java 语言中的 `System.out.println` 和 `System.out.print`。

如下所示：

```go
package main

import "fmt"

func main() {
	// 输出后换行
	fmt.Println("xcbeyond，帅么？")
	// 输出后不换行
	fmt.Print("帅!")
	fmt.Print("太帅了!")
}
```

输出结果：

```
xcbeyond，帅么？
帅!太帅了!
```

此外，字符串输出还可以进行格式化输出，后续章节会进行介绍。

## 6、字符串拼接

Go 语言的字符串拼接最常用的方法是通过 `+` 实现：

```go
package main

import "fmt"

func main() {
    fmt.Println("xc" + "beyond")
}
```

输出结果：

```sh
xcbeyond
```

> `+` 实现拼接，会产生一个新的字符串对效率有所影响。除此之外，还可以借助函数 `fmt.Sprintf`、`strings.Join`、`buffer.WriteString` 等实现，后续字符串章节会详细介绍。
