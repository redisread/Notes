## 【leetcode】基本计算器  Go（栈）



**问题描述：**

给你一个字符串表达式 `s` ，请你实现一个基本计算器来计算并返回它的值。

**示例 1：**

```
输入：s = "1 + 1"
输出：2
```

**示例 2：**

```
输入：s = " 2-1 + 2 "
输出：3
```

**示例 3：**

```
输入：s = "(1+(4+5+2)-3)+(6+8)"
输出：23
```

 

**提示：**

- `1 <= s.length <= 3 * 105`
- `s` 由数字、`'+'`、`'-'`、`'('`、`')'`、和 `' '` 组成
- `s` 表示一个有效的表达式



### 栈思想

将符号作为栈内元素，使用1和-1表示当当前符号是否逆置。



Go语言没有栈这个数据结构，我们使用slice切片模拟栈的结构。

**Go实现：**

```go
func calculate(s string) int {
    // 因为只有+和-，所以使用栈记录当前数字是+还是-
    n := len(s)
    // 初始化操作符
    ops := make([]int,0)
    ops = append(ops,1)

    i := 0
    ret := 0
    sign := 1

    // 遍历寻找四种符号，加上空格作为判断
    for i < n {
        if s[i] == ' ' {
            i++
        } else if s[i] == '+' {
            sign = ops[len(ops) - 1]
            i++
        } else if s[i] == '-' {
            sign = -ops[len(ops) - 1]
            i++
        } else if s[i] == '(' {
            ops = append(ops,sign)
            i++
        } else if s[i] == ')' {
            ops = ops[0:len(ops)-1]
            i++
        } else {
            num := 0
            for i < n && s[i] >= '0' && s[i] <= '9' {
                num = num * 10 + int(s[i] - '0')
                i++
            }
            ret += num * sign
        }
    }

    return ret
}
```



---

参考：

- [224. 基本计算器 - 力扣（LeetCode）](https://leetcode-cn.com/problems/basic-calculator/)