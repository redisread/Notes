# Go

参考：[优质外文翻译 | Go 技术论坛](https://learnku.com/go/c/translations?page=2)

[首页 - Go语言中文网 - Golang中文社区](https://studygolang.com/)

[简介 · Go语言标准库](https://books.studygolang.com/The-Golang-Standard-Library-by-Example/)

![go](https://i.loli.net/2021/02/04/2Sz5l1m3NnQWywG.jpg)



### 镜像加速

[Go 国内加速镜像](https://learnku.com/go/wikis/38122)



### 运行与构建

构建独立执行的go程序（创建二进制文件）

```
go build test.go
```

跑go程序：

```
go run test.go
```

果你想同时运行多个 Go 文件，你可以通过命令模板的形式，或者在一行命令中指定所有要执行的文件

```bash
$ go run dir/*.go
$ go run dir/**/*.go
$ go run file-a.go file-b.go file-c.go
```



设置GOPATH

```
export GOPATH=/tmp/gopkg:/tmp/gomain
export GOBIN=/tmp
```

使用下面的命令，可以将二进制文件部署到 `bin` 目录

```
go install hello.go
```



### 包(package)

每一段 Go 程序都 必须 属于一个包。正如 Getting started with Go 中所说的，一个标准的可执行的 Go 程序必须有 package main 的声明。如果一段程序是属于 main 包的，那么当执行 go install 的时候就会将其生成二进制文件，当执行这个文件时，就会调用 main 函数。如果一段程序是属于 main 以外的其他包，那么当执行 go install 的时候，就会创建一个 包管理 文件。别着急，接下来我会对这些做详细的讲解。

导入包

```go
import (
	"fmt"
	"math/rand"
)
```



### 注释

Go 的注释规则跟 `JavaScript` 或 `c++` 一样。单行注释用 `//comment`，多行注释用 `/*comment*/`



### 分号

go几乎不使用分号，只在循环或者switch语句使用



### 变量

以var作标志，然后变量名在前面，变量类型在后面，例如

```go
var a int = 5
var b int32
```

> 关于变量的命名，Go 建议用 `单个` 单词或者 `小驼峰` 的形式。用 `下划线` 不会产生语法错误，但是不推荐

像下面这样声明变量时，可以省去变量类型

```go
var integer1 = 52 //int
var string1 = "Hello World" //string
var boolean1 = false //bool
```

更简单的写法

```go
integer1 := 52 //int
string1 := "Hello World" //string
boolean1 := false //bool
```



**数组声明**

```go
var nums1, nums2 [26]int
```



### 常量

格式

```go
const const_name [data_type] = fixed_value
```



### 指针

![img](go/RtdOYak2DG.png!large)

Go 语言的取地址符是 &，放到一个变量前使用就会返回相应变量的内存地址。

```go
func main() {
	var a int = 10
	fmt.Printf("变量的地址: %x\n", &a)
}
```

```
变量的地址: 4000086010
```



**指针的声明**

```go
var var_name *var-type
```

例如：

```go
var ip *int        /* 指向整型*/
var fp *float32    /* 指向浮点型 */
```



**空指针**

当一个指针被定义后没有分配到任何变量时，它的值为 nil。

nil 指针也称为空指针。(未赋值的指针为空指针)

nil在概念上和其它语言的null、None、nil、NULL一样，都指代零值或空值。

```go
if(ptr != nil)     /* ptr 不是空指针 */
if(ptr == nil)    /* ptr 是空指针 */
```



### 类型转换

Go 不支持不同类型变量之间的加减法等任何算数运算。要想进行算数运算，必须先转成同样的数据类型。Go 支持这种数据类型转换， 语法格式为 `type(var)`.

```
var1 := 10 // int
var2 := 10.5 // float

// 非法的 
// var3 := var1 + var2

// 合法的
var3 := var1 + int(var2) // var3 == 20
```



### 类型别名(type)

(类似C中的define和typedef)

格式

```go
type aliasName aliasTo
```

例如

```go
type myint int
var vv myint = 5
fmt.Println(vv)
```



### 枚举

```go
const (
    z = iota + 1
    _
    x
    y
    )
fmt.Println(z, x, y)
```

```
1 3 4
```



### 循环

```go
for i := 0; i < 10; i++ {
	fmt.Println(i)
}
```



### 判断

注意，下面的else位置必须在与if语句的右花括号同一行！

```go
if 布尔表达式 {
   /* 在布尔表达式为 true 时执行 */
} else {
  /* 在布尔表达式为 false 时执行 */
}
```



### range关键字

Go 语言中 range 关键字用于 for 循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)的元素。在数组和切片中它返回元素的索引和索引对应的值，在集合中返回 key-value 对。

```go
func main() {
	nums := []int{3, 2, 1}
	for index, val := range nums {
		fmt.Printf("index = %d  val = %d\n", index, val)
}
```

```
index = 0  val = 3
index = 1  val = 2
index = 2  val = 1
```





### 构建动态数组

在go中，也是必须声明常量大小的数组的。要是想申请动态数组，可以这样：

```go
a := make([]int,nums)
```



### 相关结构



#### slice



#### map







Go关键字



- fallthrough 
- 