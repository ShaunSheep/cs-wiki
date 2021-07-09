
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