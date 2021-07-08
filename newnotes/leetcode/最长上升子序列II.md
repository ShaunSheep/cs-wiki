
#  最长上升连续子序列 II

## 题目链接

[最长上升连续子序列 II](https://www.lintcode.com/problem/398/?_from=collection&fromId=161)

## 题目描述

描述

给定一个整数矩阵. 找出矩阵中的最长连续上升子序列, 返回它的长度.

最长连续上升子序列可以从任意位置开始, 向上/下/左/右移动.

样例

**样例 1:**

```
输入: 
    [
      [1, 2, 3, 4, 5],
      [16,17,24,23,6],
      [15,18,25,22,7],
      [14,19,20,21,8],
      [13,12,11,10,9]
    ]
输出: 25
解释: 1 -> 2 -> 3 -> 4 -> 5 -> ... -> 25 (由外向内螺旋)
```

**样例 2:**

```
输入: 
    [
      [1, 2],
      [5, 3]
    ]
输出: 4
解释: 1 -> 2 -> 3 -> 5
```

挑战

假定这是一个 N x M 的矩阵. 在 O(NM) 的时间复杂度和空间复杂度内解决这个问题.



## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 动态规划 | 下文代码实现  | $O(1)$|$(1)$|

动态规划, 设定状态 `f[i][j] 表示矩阵中坐标 (i, j) 的点开始的最长上升子序列`

状态转移方程:

```
int dx[4] = {0, 1, -1, 0};
int dy[4] = {1, 0, 0, -1};

f[i][j] = max{ f[i + dx[k]][j + dy[k]] + 1 }

k = 0, 1, 2, 3, matrix[i + dx[k]][j + dy[k]] > matrix[i][j]
```

这道题目可以向四个方向走, 所以推荐使用记忆化搜索(递归)的写法.

(当然, 也可以反过来设定: f[i](https://www.jiuzhang.com/problem/longest-continuous-increasing-subsequence-ii/) 表示走到 (i, j) 的最长上升子序列, 相应的状态转移方程做一点点改变即可)

## 代码实现

```java
// 九章算法强化班版本：
public class Solution {
    /**
     * @param matrix: A 2D-array of integers
     * @return: an integer
     */

    int[][] dp;
    int n, m;

    public int longestContinuousIncreasingSubsequence2(int[][] A) {
        if (A.length == 0) {
            return 0;
        }

        n = A.length;
        m = A[0].length;
        int ans = 0;
        dp = new int[n][m]; // dp[i][j] means the longest continuous increasing path from (i,j)
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                dp[i][j] = -1; // dp[i][j] has not been calculated yet
            }
        }

        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                search(i, j, A);
                ans = Math.max(ans, dp[i][j]);
            }
        }

        return ans;
    }

    int[] dx = { 1, -1, 0, 0 };
    int[] dy = { 0, 0, 1, -1 };

    void search(int x, int y, int[][] A) {
        if (dp[x][y] != -1) { // if dp[i][j] has been calculated, return directly
            return;
        }

        int nx, ny;
        dp[x][y] = 1;
        for (int i = 0; i < 4; ++i) {
            nx = x + dx[i];
            ny = y + dy[i];
            if (nx >= 0 && nx < n && ny >= 0 && ny < m) {
                if (A[nx][ny] > A[x][y]) {
                    search(nx, ny, A); // dp[nx][ny] must be calcuted
                    dp[x][y] = Math.max(dp[x][y], dp[nx][ny] + 1);
                }
            }
        }
    }
}
```

