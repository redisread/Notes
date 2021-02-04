### Go

![go](https://i.loli.net/2021/02/04/2Sz5l1m3NnQWywG.jpg)

[Go 国内加速镜像](https://learnku.com/go/wikis/38122)

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



包

每一段 Go 程序都 必须 属于一个包。正如 Getting started with Go 中所说的，一个标准的可执行的 Go 程序必须有 package main 的声明。如果一段程序是属于 main 包的，那么当执行 go install 的时候就会将其生成二进制文件，当执行这个文件时，就会调用 main 函数。如果一段程序是属于 main 以外的其他包，那么当执行 go install 的时候，就会创建一个 包管理 文件。别着急，接下来我会对这些做详细的讲解。



导入包

```go
import (
	"fmt"
	"math/rand"
)
```



注释

Go 的注释规则跟 `JavaScript` 或 `c++` 一样。单行注释用 `//comment`，多行注释用 `/*comment*/`



分号

go几乎不使用分号，只在循环或者switch语句使用

变量声明

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



类型转换

Go 不支持不同类型变量之间的加减法等任何算数运算。要想进行算数运算，必须先转成同样的数据类型。Go 支持这种数据类型转换， 语法格式为 `type(var)`.

```
var1 := 10 // int
var2 := 10.5 // float

// 非法的 
// var3 := var1 + var2

// 合法的
var3 := var1 + int(var2) // var3 == 20
```



类型别名(类似C中的define和typedef)

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



常量

格式

```go
const const_name [data_type] = fixed_value
```



枚举

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

循环

```go
for i := 0; i < 10; i++ {
	fmt.Println(i)
}
```



