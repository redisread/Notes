## 【leetcode】省份数量  C++/Go（并查集）

**问题描述**

有 `n` 个城市，其中一些彼此相连，另一些没有相连。如果城市 `a` 与城市 `b` 直接相连，且城市 `b` 与城市 `c` 直接相连，那么城市 `a` 与城市 `c` 间接相连。

**省份** 是一组直接或间接相连的城市，组内不含其他没有相连的城市。

给你一个 `n x n` 的矩阵 `isConnected` ，其中 `isConnected[i][j] = 1` 表示第 `i` 个城市和第 `j` 个城市直接相连，而 `isConnected[i][j] = 0` 表示二者不直接相连。

返回矩阵中 **省份** 的数量。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/12/24/graph1.jpg)

```
输入：isConnected = [[1,1,0],[1,1,0],[0,0,1]]
输出：2
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/12/24/graph2.jpg)

```
输入：isConnected = [[1,0,0],[0,1,0],[0,0,1]]
输出：3
```

 

**提示：**

- `1 <= n <= 200`
- `n == isConnected.length`
- `n == isConnected[i].length`
- `isConnected[i][j]` 为 `1` 或 `0`
- `isConnected[i][i] == 1`
- `isConnected[i][j] == isConnected[j][i]`



### 并查集

#### 主要步骤

**初始化**

准备n个节点表示n个元素，在初始化时，彼此之间无关联，即均单独成树

**合并**

合并即为合并两个树,由于每个树表示一个集合(即相同类别，无顺序之分)，故只需要让一个树的根节点指向另一个树即可。合并的例子：

**查询**

查询即查询两个元素是否是同一集合中，即查询对应的两个节点是否在同一个树上，因此只需要查询两个节点所对应的树的根节点是否一致即可。

#### 优化

主要优化思路为尽可能使树的高度降低，从而尽可能减少查询的时间复杂度

**路径压缩**

![p1](https://oi-wiki.org/ds/images/dsu1.png)

**rank排序合并(使得树的深度小)**

![img](https://pic2.zhimg.com/80/v2-96fbb25365b43f0a109bec6d55b3b899_1440w.jpg)

#### 代码实现

C++实现：

```cpp
class Solution {
public:
    // 内部维护一个父亲节点的数组parent和节点深度的数组rank
    vector<int> parent;
    vector<int> rank;

    // 初始化并查集的数据，全部节点的深度为0，父节点为本身
    void init(int N)
    {
        parent = vector<int>(N);
        rank = vector<int>(N,0);
        for(int i = 0;i < N;++i) parent[i] = i;
    }
    
    // 使用递归的方式查找根节点
    int find(int x)
    {
        if(parent[x] == x) return x;
        //  //这一步很巧妙，既通过递归找到根节点，又同时完成了路径的压缩
        return parent[x] = find(parent[x]);
    }

    // 合并
    void union_it(int x,int y)
    {
        x = find(x);
        y = find(y);
        // //在同一集合，不需操作
        if(x == y) return ;
        // //rank大的节点作为合并后的根节点 
        if(rank[x] < rank[y]) parent[x] = y;
        else {
            // //如果两个树高度一样，则合并后树的高度加1
            if(rank[x] == rank[y]) ++rank[x];
            parent[y] = x;
        }
    }
    int findCircleNum(vector<vector<int>>& isConnected) {
        int n = isConnected.size();
        init(n);
        for(int i = 0;i < n;++i)
        {
            for(int j = i+1;j < n;++j)
            {
                // 合并两个不同的节点
                if(isConnected[i][j] == 1) union_it(i,j);
            }
        }
        int sum = 0;
        // 统计数量
        for(int i = 0;i < parent.size();++i)
            if(parent[i] == i) ++sum;
        return sum;
    }
};
```



Go实现

```go
func init_ufs(parent []int,rank []int,n int) {
    for i := 0;i < n;i++ {
        parent[i] = i
        rank[i] = 0
    }
}

func find(parent []int,x int) int {
    if parent[x] == x {
        return x
    }
    parent[x] = find(parent,parent[x])
    return parent[x]
}

func union(parent []int,rank []int,x int,y int) {
    x = find(parent,x)
    y = find(parent,y)
    if x == y {
        return 
    }

    if rank[x] < rank[y] {
        parent[x] = y
    } else {
        if rank[x] == rank[y] {
            rank[x]++
        }
        parent[y] = x
    }
}
func findCircleNum(isConnected [][]int) int {
    n := len(isConnected)
    parent := make([]int,n)
    rank := make([]int,n)
    // init
    init_ufs(parent,rank,n)

    for i := 0; i < n; i++ {
        for j := i+1; j < n; j++ {
            if isConnected[i][j] == 1 {
                union(parent,rank,i,j)
            }
        }
    }
    sum := 0
    for i := 0; i < n; i++ {
        if parent[i]==i {
            sum++
        }
    }
    
    return sum
}
```



---

参考：

- [547. 省份数量 - 力扣（LeetCode）](https://leetcode-cn.com/problems/number-of-provinces/)
- [一文搞定并查集_Ding 的博客-CSDN博客](https://blog.csdn.net/dingdingdodo/article/details/104290972)

