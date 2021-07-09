
#  计算最大值II

## 题目链接

[计算最大值II](https://www.lintcode.com/problem/741/?_from=collection&fromId=161)

## 题目描述

描述

给定一串数字，编写一个函数来计算字符串通过一系列操作能得到的最大值，你可以在任意两个数字之间添加一个加号或乘号。注意，这题和计算最大值（Calculate Maximum Value）”原题不同，在这题中你可以在任何地方插入括号。

样例

**样例1**

```
输入: str = 01231
输出: 12
解释:
(0 + 1 + 2) * (3 + 1) = 12 得到最大值为 12
```

**样例2**

```
输入: str = 891
输出: 80
解释:
8 * (9 + 1) = 80, 最大值为 80.
```

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 动态规划法 | 区间dp  | $O(1)$|$(1)$|

dp[i][j]表示第i個到第j個substring之最大值

初始化

dp[i][i] = str[i]

dp[i][i + 1] = max(str[i] * str[i + 1], str[i] + str[i + 1])

方程式

dp[i][j] = max(dp[i][k] + dp[k + 1][j], dp[i][k] * dp[k + 1][j]), i <= k < j

## 代码实现

```java
public class Solution {
    /**
     * @param str: a string of numbers
     * @return: the maximum value
     */
    public int maxValue(String str) {
        // write your code here
        int n = str.length();
        int[][] dp = new int[n][n];
        for (int i = 0; i < n; i++) {
            dp[i][i] = (int)str.charAt(i) - (int)'0';
        }
        
        for (int l = 2; l <= n; l++) {
            for (int i = 0; i < n - l + 1; i++) {
                int j = i + l - 1;
                for (int k = i; k < j; k++) {
                    dp[i][j] = Math.max(dp[i][j], dp[i][k] + dp[k + 1][j]);
                    dp[i][j] = Math.max(dp[i][j], dp[i][k] * dp[k + 1][j]);
                }
            }
        }
        
        return dp[0][n - 1];
    }
}
```