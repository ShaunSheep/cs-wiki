



#  完全平方数

## 题目链接

[完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

## 题目描述

给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。

给你一个整数 n ，返回和为 n 的完全平方数的 最少数量 。

完全平方数 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，1、4、9 和 16 都是完全平方数，而 3 和 11 不是。

 

示例 1：

输入：n = 12
输出：3 
解释：12 = 4 + 4 + 4
示例 2：

输入：n = 13
输出：2
解释：13 = 4 + 9

提示：

1 <= n <= 104





## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| dp动态规划法 | 下文代码实现  | $O(1)$|$(1)$|

一个n，代表n个格子，n个格子可以划分成 $1^2+2^2+3^2....i^2$

## 代码实现

```java
/*
 * @lc app=leetcode.cn id=279 lang=java
 *
 * [279] 完全平方数
 */

// @lc code=start
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n+1];
        dp[0] = 0;
        for(int i = 1;i <=n ;i++){
            // 初始化最大数
            dp[i] = Integer.MAX_VALUE;
            // i - j*j 有效
            for(int j = 1;j*j <= i;j++){
              // j*j本身计数+1
              dp [i] = Math.min(dp[i],dp[i-j*j]+1) ; 
            }
        }
        return dp[n];
    }
}
// @lc code=end


```