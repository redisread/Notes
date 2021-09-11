## 螺螺旋矩阵



螺旋矩阵I

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> res;
        int m = matrix.size();
        int n = matrix[0].size();
        int L = 0,R = n-1,U = 0,D = m-1;
        while(true)
        {
            for(int i = L;i <= R;++i) res.push_back(matrix[U][i]);
            if(++U > D) break;
            for(int i = U;i <= D;++i) res.push_back(matrix[i][R]);
            if(--R < L) break;
            for(int i = R;i >= L;--i) res.push_back(matrix[D][i]);
            if(--D < U) break;
            for(int i = D;i >= U;--i) res.push_back(matrix[i][L]);
            if(++L > R) break;
        }
        return res;
    }
};
```



螺旋矩阵II

```cpp
class Solution
{
public:
    vector<vector<int>> generateMatrix(int n)
    {
        vector<vector<int>> matrix(n,vector<int>(n));
        int end = n * n;
        int index = 1;
        int L = 0,U = 0,D = n-1,R = n-1;
        while(true)
        {
            for(int i = L;i <= R;++i) matrix[U][i] = index++;
            if(++U > D) break;
            for(int i = U;i <= D;++i) matrix[i][R] = index++;
            if(--R < L) break;
            for(int i = R;i >= L;--i) matrix[D][i] = index++;
            if(--D < U) break;
            for(int i = D;i >= U;--i) matrix[i][L] = index++;
            if(++L > R) break;
        }
        return matrix;
    }
};
```



螺旋矩阵III

```cpp
class Solution
{
public:
    // 判断是否合法
    bool isValid(int r0, int c0, int R, int C)
    {
        if (!(r0 >= 0 && r0 < R && c0 >= 0 && c0 < C))
            return false;
        return true;
    }

    vector<vector<int>> spiralMatrixIII(int R, int C, int r0, int c0)
    {
        vector<vector<int>> res;
        res.push_back({r0, c0});
        // 操作的最大步数
        int maxSteps = R * C;
        int index = 1;
        while (true)
        {
            for (int i = 0; i < index; ++i)
            {
                ++c0;
                if (isValid(r0, c0, R, C))
                {
                    res.push_back({r0, c0});
                    --maxSteps;
                }
            }
            if (maxSteps <= 1)
                break;
            for (int i = 0; i < index; ++i)
            {
                ++r0;
                if (isValid(r0, c0, R, C))
                {
                    res.push_back({r0, c0});
                    --maxSteps;
                }
            }
            if (maxSteps <= 1)
                break;
            index++;
            for (int i = 0; i < index; ++i)
            {
                --c0;
                if (isValid(r0, c0, R, C))
                {
                    res.push_back({r0, c0});
                    --maxSteps;
                }
            }
            if (maxSteps <= 1)
                break;
            for (int i = 0; i < index; ++i)
            {
                --r0;
                if (isValid(r0, c0, R, C))
                {
                    res.push_back({r0, c0});
                    --maxSteps;
                }
            }
            if (maxSteps <= 1)
                break;
            index++;
        }
        return res;
    }
};
```



