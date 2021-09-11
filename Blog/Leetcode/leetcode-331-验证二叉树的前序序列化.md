## 【leetcode】验证二叉树的前序序列化  C++/Go（栈/递归）



**问题描述：**

序列化二叉树的一种方法是使用前序遍历。当我们遇到一个非空节点时，我们可以记录下这个节点的值。如果它是一个空节点，我们可以使用一个标记值记录，例如 `#`。

```
     _9_
    /   \
   3     2
  / \   / \
 4   1  #  6
/ \ / \   / \
# # # #   # #
```

例如，上面的二叉树可以被序列化为字符串 `"9,3,4,#,#,1,#,#,2,#,6,#,#"`，其中 `#` 代表一个空节点。

给定一串以逗号分隔的序列，验证它是否是正确的二叉树的前序序列化。编写一个在不重构树的条件下的可行算法。

每个以逗号分隔的字符或为一个整数或为一个表示 `null` 指针的 `'#'` 。

你可以认为输入格式总是有效的，例如它永远不会包含两个连续的逗号，比如 `"1,,3"` 。

**示例 1:**

```
输入: "9,3,4,#,#,1,#,#,2,#,6,#,#"
输出: true
```

**示例 2:**

```
输入: "1,#"
输出: false
```

**示例 3:**

```
输入: "9,#,#,1"
输出: false
```





### 递归思想

前序遍历其实就是深度优点遍历，向一直往左节点向下走，知道走不动了再走右节点。那么我们可以考虑下面这种基本的情况：

![基本情况](https://i.loli.net/2021/03/12/ifuHanEQWdTP63R.png)

将两个叶子去掉之后，可以消除0节点下面的所有子树，那么就相当于我们可以删除0以下的子树。所以，这个时候，我们删除两个叶子节点，并且将0节点也设置为叶子节点，那么，有如下的转换：

![删除子树](https://i.loli.net/2021/03/12/59WtkNIMsb6rS4x.png)

我们注意到，当我们不断的这样删除子树的时候，节点不断减少，只要是满足二叉树的前序遍历的序列，最终都会变成下面这种情况

![最终情况](https://i.loli.net/2021/03/12/dzcJMOySDNaCb3m.png)



而假如最终情况不是上面这种情况的，就不满足二叉树的前序遍历。

> 例如：
>
> 字符串"1,#"的二叉树表示如下，就不能够消除
>
> ![不符合的情况](https://i.loli.net/2021/03/12/wTo58blgkrhRyiW.png)



**代码实现分析:**

要编写这个迭代遍历的过程，使用栈思想比较容易实现，就是当遇到后面两个都是"#"的时候("#"符号我们使用-1表示)，我们可以删除栈顶的3个元素，然后将一个“#”(也就是-1)压入栈顶。不断的循环这个操作，直到结尾。



**C++实现：**

```cpp
class Solution {
public:
    bool isValidSerialization(string preorder) {
        int n = preorder.length();
        vector<int> stk;
        int num = 0;
        for(int i = 0;i < n;++i){
            if(isdigit(preorder[i])){
                num = num * 10 + (preorder[i] - '0');
                // 结尾情况也需要将数字入栈
                if(i == n -1) stk.push_back(num);
            }
            else if(preorder[i] == ',' && preorder[i-1] != '#'){
                stk.push_back(num);
                num = 0;
            }
            else if(preorder[i] == '#'){
                stk.push_back(-1);
                int len = stk.size();
                while(len >= 3 && stk[len-1] == -1 && stk[len-2] == -1){
                    stk.pop_back();
                    stk.pop_back();
                    stk.back() = -1;
                    len = stk.size();
                }
                // 提前出现最终结果，错误
                if(stk.size() == 1 && stk[0] == -1 && i != n-1){
                    return false;
                }
            }
        }
        // 判断最终情况
        if(stk.size() == 1 && stk[0] == -1) return true;
        return false;
    }
};
```

**Go实现：**

```go
func isValidSerialization(preorder string) bool {
    n := len(preorder)
    stk := make([]int,0)
    num := 0
    
    for i := 0; i < n; i++ {
        isdigit := (preorder[i] >= '0' && preorder[i] <= '9')
        if isdigit {
            num = num * 10 + int(preorder[i]-'0')
            if i == n - 1 {// 结尾是数字的情况
                stk = append(stk,num)
            }
        } else if preorder[i] == '#' {
            stk = append(stk,-1)
            snum := len(stk)
            for snum >= 3 && stk[snum-1] == -1 && stk[snum-2] == -1{
                stk = stk[0:snum-3]
                stk = append(stk,-1)
                snum = len(stk)
            }
            // 这里提前出现了最终的情况，也是错误的
            if len(stk) == 1 && stk[0] == -1 && i != n-1 {
                return false
            }
        } else if preorder[i] == ',' && preorder[i-1] != '#' {
            stk = append(stk,num)
            num = 0
        } 
    }
    // 判断是否符合最终情况
    if len(stk) == 1 && stk[0] == -1 {
        return true
    }
    return false
}
```



---

参考：

- [331. 验证二叉树的前序序列化 - 力扣（LeetCode）](https://leetcode-cn.com/problems/verify-preorder-serialization-of-a-binary-tree/)