
#  最小路径和

## 题目链接

[最小路径和](https://www.lintcode.com/problem/110/?_from=collection&fromId=161)

## 题目描述

描述

给定一个只含非负整数的m*n*m*∗*n*网格，找到一条从左上角到右下角的可以使数字和最小的路径。

?>你在同一时间只能向下或者向右移动一步

样例

**样例 1：**

输入：

```
grid = [[1,3,1],[1,5,1],[4,2,1]]
```

输出：

```
7
```

解释：

路线为： 1 -> 3 -> 1 -> 1 -> 1

**样例 2：**

输入：

```
grid = [[1,3,2]]
```

输出：

```
6
```

解释：

路线是： 1 -> 3 -> 2

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  动态规划法 | 下文代码实现  | $O(1)$|$(1)$|

`Dpi` 存储从`（0， 0）` 到`（i, j）`的最短路径。 `Dpi = min(Dpi-1), Dpi) + gridi`;

## 代码实现

```java
public class Solution {
    public int minPathSum(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }

        int M = grid.length;
        int N = grid[0].length;
        int[][] sum = new int[M][N];

        sum[0][0] = grid[0][0];

        for (int i = 1; i < M; i++) {
            sum[i][0] = sum[i - 1][0] + grid[i][0];
        }

        for (int i = 1; i < N; i++) {
            sum[0][i] = sum[0][i - 1] + grid[0][i];
        }

        for (int i = 1; i < M; i++) {
            for (int j = 1; j < N; j++) {
                sum[i][j] = Math.min(sum[i - 1][j], sum[i][j - 1]) + grid[i][j];
            }
        }

        return sum[M - 1][N - 1];
    }
}
```



## 二刷代码

状态：`dp[i][j]`从原点至当前点最短路径之和

转移方程:`dp[i][j]=min{dp[i-1][j],dp[i][j-1]}+grid[i][j]`

初始化：`dp[i][j]=grid[i][j]`,`i=0 | j  =0 dp[i][j] = 1`

答案：`dp[m-1][n-1]`

此做法的难点在于：

1. 行数或列数如果为0，返回0
2. 状态在原点的值为数组的值`     dp[i][j] = grid[i][j];`
3. 分别判断`i>0`、`j>0`

```java
// @lc code=start
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length;
        if (m == 0) {
            return 0;
        }
        int n = grid[0].length;
        if (n == 0) {
            return 0;
        }
        int[][] dp = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int minFrom = Integer.MAX_VALUE;
                if (i == 0 && j == 0) {
                    // 原点的值等于grid[i][j]
                    dp[i][j] = grid[i][j];
                    // 原点continue
                    continue;
                }
                // i>0
                if (i > 0) {
                    // 打擂台，存储最小路径和
                    minFrom = Math.min(minFrom, dp[i - 1][j]);
                }
                // j>0
                if (j > 0) {
                    // 打擂台，存储最小路径和
                    minFrom = Math.min(minFrom, dp[i][j-1]);
                }
                // 到达当前点的最小路径和 = 之前的最短路径+当前点的长度
                dp[i][j] = minFrom + grid[i][j];
            }
        }
        return dp[m-1][n-1];
    }
}
// @lc code=end

```

