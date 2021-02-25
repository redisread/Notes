# Go

参考：[优质外文翻译 | Go 技术论坛](https://learnku.com/go/c/translations?page=2)

[首页 - Go语言中文网 - Golang中文社区](https://studygolang.com/)

[简介 · Go语言标准库](https://books.studygolang.com/The-Golang-Standard-Library-by-Example/)

[shockerli/go-awesome: Go 语言优秀资源整理，为项目落地加速🏃](https://github.com/shockerli/go-awesome)

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

Go 语言的取地址符是 &，放到一个变量前使用就会返回相应变量的内存地址。没有->操作符号。

```go
func main() {
	var a int = 10
	fmt.Printf("变量的地址: %x\n", &a)
}
```

```
变量的地址: 4000086010
```

**传递数组指针**

在传参的时候避免数组的拷贝，传切片也是类似的

```go
func test(x *[2]int) {
	...
}
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

假如想要while循环的效果，可以这样写

```go
for a < n {
	...
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



### switch语句

switch 语句用于基于不同条件执行不同动作，每一个 case 分支都是唯一的，从上至下逐一测试，直到匹配为止。

switch 语句执行的过程从上至下，直到找到匹配项，匹配项后面也不需要再加 break。

switch 默认情况下 case 最后自带 break 语句，匹配成功后就不会执行其他 case，如果我们需要执行后面的 case，可以使用 **fallthrough** 

```go
    switch carType {
        case 1: 
            if this.big <= 0 {
                return false
            } else {
                this.big--;
                return true
            }
        case 2:
            if this.medium <= 0 {
                return false
            } else {
                this.medium--;
                return true
            }
        case 3:
            if this.small <= 0 {
                return false
            } else {
                this.small--;
                return true
            }
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





### 动态数组

在go中，也是必须声明常量大小的数组的。要是想申请动态数组，可以这样：

```go
a := make([]int,nums)
```

[Go 语言实现动态数组 - SegmentFault 思否](https://segmentfault.com/a/1190000015680429)

切片和数组的类型有什么不一样，我们可以打印一下，就可以知道两者的区别了，数组是容量的，所以中括号中有容量，切片的动态数组，是没有容量，这是数组和切片最大的区别

```go
test_array := [20]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
test_dar := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
fmt.Println(reflect.TypeOf(test_array), reflect.TypeOf(test_dar))
```

```
[20]int []int
```

声明数组与声明切片

```go
var A [20]int	// 申明数组
var B []int		// 申明切片
s2 := []int{}	// 申明空切片
var s3 []int = make([]int,0)	// 申明空切片
```

也可以指定容量，其中capacity为可选参数

```go
make([]T, length, capacity)
```

添加元素

使用append添加元素，但是，添加之后返回一个临时的切片，对原来的切片赋值才能够覆盖：

```go
test_dar := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
test_dar = append(test_dar, 10)
fmt.Println(test_dar)
```

```
[0 1 2 3 4 5 6 7 8 9 10]
```

删除元素

[Go语言从切片中删除元素](http://c.biancheng.net/view/30.html)



len() 和 cap() 函数

切片是可索引的，并且可以由 len() 方法获取长度。

切片提供了计算容量的方法 cap() 可以测量切片最长可以达到多少

```go
test_dar := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
test_dar = append(test_dar, 10)
fmt.Println(test_dar, len(test_dar), cap(test_dar))
```

```
[0 1 2 3 4 5 6 7 8 9 10] 11 20
```



拷贝

```go
copy(numbers1,numbers)	// 拷贝 numbers 的内容到 numbers1
```



### 结构体

结构体内部的变量声明不需要加var关键字。

```go
type Books struct {
   title string
   author string
   subject string
   book_id int
}
```

Go语言的类型或结构体没有构造函数的功能，但是我们可以使用结构体初始化的过程来模拟实现构造函数。

[Go语言构造函数](http://c.biancheng.net/view/68.html)







### 深入使用

#### 闭包

```go
package main
import "fmt"

func outerFunc() {
    a := 1
    // innerFunc := func innerFuncTest(b int) int { //error
    innerFunc := func (b int) int {
        a = 2
        return a + b
    }
    fmt.Printf("ans:%d, a:%d", innerFunc(3), a) //ans:5, a:2
}

func main(){
    outerFunc()
}
```





#### channel

参考：[总结了才知道，原来channel有这么多用法！ - SegmentFault 思否](https://segmentfault.com/a/1190000017958702)





**使用场景**

把channel用在**数据流动的地方**：

1. 消息传递、消息过滤
2. 信号广播
3. 事件订阅与广播
4. 请求、响应转发
5. 任务分发
6. 结果汇总
7. 并发控制
8. 同步与异步

### 相关结构



#### slice

[深入解析 Go 中 Slice 底层实现](https://halfrost.com/go_slice/)

声明二维数组,x和y必须是常量

```go
var arrayName [ x ][ y ] variable_type
```

[Go 二维切片初始化_jiang_mingyi的博客-CSDN博客_golang 二维数组 初始化](https://blog.csdn.net/jiang_mingyi/article/details/81567740)

与C++类似的动态创建二维动态数组的方法类似。

```go
f := make([][]int, n)
    for i := 0; i < len(matrix); i++ {
        f[i] = make([]int, n)
    }
```



#### map



#### interface





### 排序

[【go语言】基本类型排序和 slice 排序 | iTimeTraveler](https://itimetraveler.github.io/2016/09/07/%E3%80%90Go%E8%AF%AD%E8%A8%80%E3%80%91%E5%9F%BA%E6%9C%AC%E7%B1%BB%E5%9E%8B%E6%8E%92%E5%BA%8F%E5%92%8C%20slice%20%E6%8E%92%E5%BA%8F/)

Go 的排序思路和 C 和 C++ 有些差别。 C 默认是对数组进行排序， C++ 是对一个序列进行排序， Go 则更宽泛一些，待排序的可以是**`任何对象`**， 虽然很多情况下是一个 `slice` (分片， 类似于数组)，或是包含 slice 的一个对象。

基本类型排序

默认为升序排序

```go
    intList := [] int {2, 4, 3, 5, 7, 6, 9, 8, 1, 0}
    float8List := [] float64 {4.2, 5.9, 12.3, 10.0, 50.4, 99.9, 31.4, 27.81828, 3.14}
    stringList := [] string {"a", "c", "b", "d", "f", "i", "z", "x", "w", "y"}
   
    sort.Ints(intList)
    sort.Float64s(float8List)
    sort.Strings(stringList)
```



降序排序

```go
    intList := [] int {2, 4, 3, 5, 7, 6, 9, 8, 1, 0}
    float8List := [] float64 {4.2, 5.9, 12.3, 10.0, 50.4, 99.9, 31.4, 27.81828, 3.14}
    stringList := [] string {"a", "c", "b", "d", "f", "i", "z", "x", "w", "y"}
   
    sort.Sort(sort.Reverse(sort.IntSlice(intList)))
    sort.Sort(sort.Reverse(sort.Float64Slice(float8List)))p' hgk
    sort.Sort(sort.Reverse(sort.StringSlice(stringList)))
```

记住，还需要导入排序的库

```go
import "sort"
```





## Tips



- go语言没有引用，但是有指针；可以使用指针达到引用的效果



Go关键字



- fallthrough 
- 