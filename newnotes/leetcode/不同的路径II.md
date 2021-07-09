
#  不同的路径 II · Unique Paths II

## 题目链接

[不同的路径 II · Unique Paths II](https://www.jiuzhang.com/problem/unique-paths-ii/)

## 题目描述

#### 描述中文

"[不同的路径](http://www.lintcode.com/problem/unique-paths/)" 的跟进问题：

现在考虑网格中有障碍物，那样将会有多少条不同的路径？

网格中的障碍和空位置分别用 1 和 0 来表示。

m 和 n 均不超过100

#### 样例

**样例 1：**

输入：

```
obstacleGrid = [[0]]
```

输出：

```
1
```

解释：

只有一个点

**样例 2：**

输入：

```
obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
```

输出：

```
2
```

解释：

只有 2 种不同的路径

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  动态规划法 | 坐标型  | $O(1)$|$(1)$|

此题由[不同的路径](newnotes/leetcode/不同的路径)拓展而来，机器人每一时刻只能选择向下或者向右移动一步，

网格中存在障碍物

对于障碍物我们需要分两种情况考虑，分别是处于网格边界和网格中央时的情况，根据题意很容易发现处于边界的障碍物，

- 左边界的第一个障碍物下面的所有边界点无法到达，
- 上边界的第一障碍物右边的所有边界点无法到达。

对于网格中的障碍物，到达此点的路径条数默认为`0`。

用数组`dp`表示可到达每个点的路径数

- 通过所有到达`(i-1,j)`这个点的路径往下走一步可到达`(i,j)`, 这路径数总共有`dp[i-1][j]`条
- 通过所有到达`(i,j-1)`这个点的路径往右走一步可到达`(i,j)`, 这路径数总共有`dp[i][-1j]`条
- 由此可以推出递推式`dp[i][j] = dp[i-1][j]+dp[i][j-1]`
- 如果数组长宽都为`0`的话返回`0`
- 设`dp`数组的大小与`obstacleGrid`数组大小一致
- 对于左边界，在第一个障碍物前面（或者到达边界）的所有点皆可到达
- 对于上边界，在第一个障碍物左边（或者到达边界）的所有点皆可到达
- 从`dp[1][1]`开始遍历网格，根据递推式`dp[i][j] = dp[i-1][j]+dp[i][j-1]`更新当前点可到达路径数
- 结果存在网格右下角，即`dp[n-1][m-1]`

## 复杂度分析

- 时间复杂度

  ```
  O(n*m)
  ```

  - 遍历一遍网格，复杂度即网格大小

- 空间复杂度

  ```
  O(n*m)
  ```

  - 需要开一个数组记录当前路径数量

## 代码实现

```java
public class Solution {
    /**
     * @param obstacleGrid: A list of lists of integers
     * @return: An integer
     */
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        //获取网格的长宽
        int n = obstacleGrid.length,m = obstacleGrid[0].length;
        if (n == 0 || m == 0) {
            return 0;
        }
        int[][] dp = new int[n][m];
        //对于左边界，第一个障碍物或边界前的所有边界点皆可到达
        if (obstacleGrid[0][0] == 0) {
            dp[0][0] = 1;
        }
        for (int i = 0;i < n;i++) {
            for (int j = 0;j < m;j++){  
                if (i==0 && j==0) {  
                    continue;
                }
                //若遇到障碍物，则跳过
                if (obstacleGrid[i][j] == 1) {
                    continue;
                }
                //对于上边界，第一个障碍物或边界左边的所有边界点皆可到达
                if (i == 0) {
                    dp[i][j] = dp[i][j-1];
                    continue;
                }
                //对于左边界，第一个障碍物或边界前的所有边界点皆可到达
                if (j == 0) {
                    dp[i][j] = dp[i-1][j];
                    continue;
                }
                //到达当前点的路径数等于能到达此点上面的点和左边点的路径数之和
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[n-1][m-1];
    }
}


```