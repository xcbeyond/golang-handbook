# 标准输入输出fmt包

在程序的任何阶段，我们可能都会需要输入/输出一些数据，以及通过输入/输出记录各种数据以进行程序调试，这种基本的输入/输出将有助于我们编写更好的代码。然而 `fmt` 包就很好的提供了标准输入输出，便于进行打印等，本文将针对 `fmt` 包进行讨论。

## 1、fmt 包

`fmt` 包实现了格式化的**输入输出**，这与 C 语言中的 `printf` 和 `scanf` 类似，它属于Go 的内置包，在安装 Go 的时候会自动安装到系统中，位于 `$GOROOT\src\pkg\fmt`目录中，其中包括以下源代码：

```sh
doc.go
errors.go
format.go
print.go
scan.go
```

### 1.1 常用函数

该包主要实现了字符串格式支持的读写功能，常用的函数有：

* `Fprint`，`Fprintf`和 `Fprintln`
* `Sprint`，`Sprintf` 和 `Sprintln`
* `Print`，`Printf` 和 `Println`
* `Fscan`，`Fscanf` 和 `Fscanln`
* `Sscan`，`Sscanf` 和`Sscanln`

上述函数长的都很相似，很容易混淆、用错，在这里介绍一些方法方便区分它们。

* `F` 开头的：都是将字符串作为输入或输出参数，并且该参数都是实现 `io.Writer` 或 `io.Reader` 接口。
* `S` 开头的：都是以格式化占位符创建的字符串返回。
* `f` 结尾的：都是支持格式化占位符。
* `ln` 结尾的：都是在输入或输出完添加了换行符`\n`。

### 1.2 格式化占位符

`fmt` 包在进行输入、输出时，都可采用格式化占位符完成格式化的处理，支持的格式化占位符如下：

（对应代码：[https://github.com/xcbeyond/golangLearning/blob/main/ch3/fmt/format/formatTest.go](https://github.com/xcbeyond/golangLearning/blob/main/ch3/fmt/format/formatTest.go)）

**普通占位符**

定义一个结构体Person，并赋值：

```go
type Person struct {
	Name string
}
var p = new(Person)
p.Name = "xcbeyond"
```

| 占位符 | 说明                       | 示例                         | 输出                            |
| ------ | -------------------------- | ---------------------------- | ------------------------------- |
| `%v`   | 相应值的默认格式           | `fmt.Printf("%v", p.Name)`   | `xcbeyond`                      |
| `%+v ` | 打印结构体时，会添加字段名 | `fmt.Printf("%+v\n", p)`     | `&{Name:xcbeyond}`              |
| `%#v ` | 相应值的Go语法表示         | `fmt.Printf("%#v\n", p)`     | `&main.Person{Name:"xcbeyond"}` |
| `%T`   | 相应值的类型               | `fmt.Printf("%T\n", p.Name)` | `string`                        |
| `%%`   | 字面上的百分号             | `fmt.Printf("%%\n")`         | `%`                             |

**布尔占位符**

| `占位符` | 说明              | 示例                      | 输出   |
| -------- | ----------------- | ------------------------- | ------ |
| `%t `    | `true` 或 `false` | `fmt.Printf("%t",1 == 1)` | `true` |

**整数占位符**

| 占位符 | 说明                                       | 示例                       | 输出     |
| ------ | ------------------------------------------ | -------------------------- | -------- |
| `%b `  | 二进制表示                                 | `fmt.Printf("%b", 5)`      | 101      |
| `%c`   | 相应 `Unicode` 码所表示的字符              | `fmt.Printf("%c", 0x4E2D)` | 中       |
| `%d`   | 十进制表示                                 | `fmt.Printf("%d", 0x12)`   | 18       |
| `%o`   | 八进制表示                                 | `fmt.Printf("%d", 10)`     | 12       |
| `%q `  | 单引号围绕的字符字面值，由Go语法安全地转义 | `fmt.Printf("%q", 0x4E2D)` | 中       |
| `%x`   | 十六进制表示，字母形式为小写 a-f           | `fmt.Printf("%x", 13)`     | d        |
| `%X`   | 十六进制表示，字母形式为大写 A-F           | `fmt.Printf("%x", 13)`     | D        |
| `%U`   | `Unicode` 格式：`U+1234`等同于 `U+%04X`    | `fmt.Printf("%U", 0x4E2D)` | `U+4E2D` |

**复数(实部、虚部)占位符**

| 占位符 | 说明                                                      | 示例                             | 输出           |
| :----- | :-------------------------------------------------------- | :------------------------------- | -------------- |
| `%e`   | 科学计数法，可保留几位小数，如：`%.3e`表示保留三位小数    | `fmt.Printf("%.3e\n", math.Pi)`  | `3.142e+00`    |
| `%E`   | 科学计数法                                                | `fmt.Printf("%E\n", math.Pi)`    | `3.141593E+00` |
| `%f`   | 科学计数法，可保留几位小数而无指数                        | `fmt.Printf("%.3f\n", math.Pi)`  | `3.142`        |
| `%g`   | 根据状况选择 `%e` 或 `%f` 以产生更紧凑的（无末尾的0）输出 | `fmt.Printf("%.3g\n", math.Pi)`  | `3.14`         |
| `%G`   | 根据状况选择 `%E` 或 `%f `以产生更紧凑的（无末尾的0）输出 | `fmt.Printf("%.3G\n", 10.20+5i)` | `(10.2+5i)`    |

**字符串占位符**

> □：表示一个空格

| 占位符    | 说明                                             | 示例                                  | 输出               |
| :-------- | :----------------------------------------------- | :------------------------------------ | ------------------ |
| `%s`      | 输出字符串表示                                   | `fmt.Printf("%s\n", "xcbeyond")`      | `xcbeyond`         |
| `%10s`    | 输出字符串最小宽度为10(右对齐)                   | `fmt.Printf("%10s\n", "xcbeyond")`    | `□□xcbeyond`       |
| `%-10s`   | 输出字符串最小宽度为10(左对齐)                   | `fmt.Printf("%-10s\n", "xcbeyond")`   | `xcbeyond□□`       |
| `%.5s`    | 输出字符串最大宽度为5                            | `fmt.Printf("%.5s\n", "xcbeyond")`    | `xcbey`            |
| `%5.10s`  | 输出字符串最小宽度为5，最大宽度为10              | `fmt.Printf("%5.10s\n", "xcbeyond")`  | `xcbeyond`         |
| `%-5.10s` | 输出字符串最小宽度为5，最大宽度为10(左对齐)      | `fmt.Printf("%-5.10s\n", "xcbeyond")` | `xcbeyond`         |
| `%5.3s`   | 输出字符串宽度为5，若是原字符串宽度大于3，则截断 | `fmt.Printf("%5.3s\n", "xcbeyond")`   | `□□xcb`            |
| `%010s`   | 若是宽度小于10，就会在字符串前面补零             | `fmt.Printf("%010s\n", "xcbeyond")`   | `00xcbeyond`       |
| `%q`      | 双引号围绕的字符串，由Go语法安全地转义           | `fmt.Printf("%q\n", "xcbeyond")`      | `"xcbeyond"`       |
| `%x`      | 十六进制，小写字母，每字节两个字符               | `fmt.Printf("%x\n", "xcbeyond")`      | `78636265796f6e64` |
| `%X`      | 十六进制，大写字母，每字节两个字符               | `fmt.Printf("%X\n", "xcbeyond")`      | `78636265796F6E64` |

**指针占位符**

| 占位符 | 说明                    | 示例                      | 输出           |
| :----- | :---------------------- | :------------------------ | -------------- |
| `%p`   | 十六进制表示，前缀 `0x` | `fmt.Printf("%p\n", &p)`  | `0xc00011a020` |
| `%#p`  | 不带前缀 `0x`           | `fmt.Printf("%#p\n", &p)` | `c00011a020`   |

## 2、输出

Go 语言的标准输出是指将输出内容直接输出到控制台上，根据不同的输出需求选择不同的函数，这些函数基本都位于 `$GOROOT\src\fmt\print.go` 中，各个函数具体使用如下。

### 2.1 fmt.Print

`fmt.Print` 使用默认格式输出，其函数定义如下：

```go
// Print formats using the default formats for its operands and writes to standard output.
// Spaces are added between operands when neither is a string.
// It returns the number of bytes written and any write error encountered.
func Print(a ...interface{}) (n int, err error) {
	return Fprint(os.Stdout, a...)
}
```

示例如下：

```go
name := "xcbeyond"
fmt.Print(name)	// xcbeyond
```

### 2.2 fmt.Printf

`fmt.Printf` 根据格式化占位符进行格式化，并标准输出，其函数定义如下：

```go
// Printf formats according to a format specifier and writes to standard output.
// It returns the number of bytes written and any write error encountered.
func Printf(format string, a ...interface{}) (n int, err error) {
	return Fprintf(os.Stdout, format, a...)
}
```

示例如下：

```go
name := "xcbeyond"
fmt.Printf("%.5s", name)	// xcbey
```

### 2.3 fmt.Println

`fmt.Println` 使用默认格式输出，并换行，其函数定义如下：

```go
// Println formats using the default formats for its operands and writes to standard output.
// Spaces are always added between operands and a newline is appended.
// It returns the number of bytes written and any write error encountered.
func Println(a ...interface{}) (n int, err error) {
	return Fprintln(os.Stdout, a...)
}
```

示例如下：

```go
name := "xcbeyond"
fmt.Println(name)	// xcbeyond 换行
```

### 2.4 fmt.Sprint

`fmt.Sprint`使用默认格式返回结果字符串，其函数定义如下：

```go
// Sprint formats using the default formats for its operands and returns the resulting string.
// Spaces are added between operands when neither is a string.
func Sprint(a ...interface{}) string {
	p := newPrinter()
	p.doPrint(a)
	s := string(p.buf)
	p.free()
	return s
}
```

示例如下：

```go
name := "xcbeyond"
retStr := fmt.Sprint(name)
fmt.Print(retStr)	// xcbeyond
```

### 2.5 fmt.Sprintf

`fmt.Sprintf` 根据格式化占位符进行格式化，返回结果字符串，其函数定义如下：

```go
// Sprintf formats according to a format specifier and returns the resulting string.
func Sprintf(format string, a ...interface{}) string {
	p := newPrinter()
	p.doPrintf(format, a)
	s := string(p.buf)
	p.free()
	return s
}
```

示例如下：

```go
name := "xcbeyond"
retStr2 := fmt.Sprintf("%.5s", name)
fmt.Print(retStr2) // xcbey
```

### 2.6 fmt.Sprintln

`fmt.Sprintln` 使用默认格式，返回结果字符串，其函数定义如下：

```go
// Sprintln formats using the default formats for its operands and returns the resulting string.
// Spaces are always added between operands and a newline is appended.
func Sprintln(a ...interface{}) string {
	p := newPrinter()
	p.doPrintln(a)
	s := string(p.buf)
	p.free()
	return s
}
```

示例如下：

```go
name := "xcbeyond"
retStr3 := fmt.Sprintln(name)
fmt.Print(retStr3) // xcbeyond  换行
```

### 2.7 fmt.Fprint

```go
// Fprint formats using the default formats for its operands and writes to w.
// Spaces are added between operands when neither is a string.
// It returns the number of bytes written and any write error encountered.
func Fprint(w io.Writer, a ...interface{}) (n int, err error) {
	p := newPrinter()
	p.doPrint(a)
	n, err = w.Write(p.buf)
	p.free()
	return
}
```

### 2.8 fmt.Fprintf

```go
// Fprintf formats according to a format specifier and writes to w.
// It returns the number of bytes written and any write error encountered.
func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error) {
	p := newPrinter()
	p.doPrintf(format, a)
	n, err = w.Write(p.buf)
	p.free()
	return
}
```

### 2.9 fmt.Fprintln

```go
// Fprintln formats using the default formats for its operands and writes to w.
// Spaces are always added between operands and a newline is appended.
// It returns the number of bytes written and any write error encountered.
func Fprintln(w io.Writer, a ...interface{}) (n int, err error) {
	p := newPrinter()
	p.doPrintln(a)
	n, err = w.Write(p.buf)
	p.free()
	return
}
```

## 3、输入

### 3.1 fmt.Fscan



### 3.2 fmt.Fscanf



### 3.3 fmt.Fscanln



### 3.4 fmt.Scan



### 3.5 fmt.Scanf



### 3.6 fmt.Scanln



### 3.7 fmt.Sscan



### 3.8 fmt.Sscanf



### 3.9 fmt.Sscanln

参考资料：

1. https://golang.org/pkg/fmt/
2. https://golangdocs.com/fmt-package-golang
3. https://yourbasic.org/golang/fmt-printf-reference-cheat-sheet/