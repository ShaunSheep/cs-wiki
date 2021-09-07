
#  零钱兑换

## 题目链接

[零钱兑换](https://leetcode-cn.com/problems/coin-change/)

## 题目描述
给你一个整数数组 coins ，表示不同面额的硬币；以及一个整数 amount ，表示总金额。

计算并返回可以凑成总金额所需的 最少的硬币个数 。如果没有任何一种硬币组合能组成总金额，返回 -1 。

你可以认为每种硬币的数量是无限的。


示例 1：

输入：coins = [1, 2, 5], amount = 11
输出：3 
解释：11 = 5 + 5 + 1
示例 2：

输入：coins = [2], amount = 3
输出：-1
示例 3：

输入：coins = [1], amount = 0
输出：0
示例 4：

输入：coins = [1], amount = 1
输出：1
示例 5：

输入：coins = [1], amount = 2
输出：2

**提示：**

- `1 <= coins.length <= 12`
- `1 <= coins[i] <= 231 - 1`
- `0 <= amount <= 104`

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  dp动态规划 | 下文代码实现  |  ||

状态定义：`dp[i]`表示数值i可由最多多少枚硬币组成

转移方程：$dp[i] = min(dp[i-coins[0]+1,dp[i-coins[1]]+1,...,dp[i-coinfs[n-1]]+1)$

初始化：`dp[0] = 0`,`dp[-1] = Integer.MAX_VALUE`,`dp[-2] = Integer.MAX_VALUE`,以及无法由硬币组成的也表示为最大值，如硬币为2，5，7，则`dp[1]=dp[3]=Integer.MAX_VALUE`

答案：`dp[x]`

抽象计算过程：

![dp硬币兑换](http://cdn.yangchaofan.cn/typora/dp硬币兑换.png)

!>dp[5]计算错了，表格里的值是对的，勘误。

## 代码实现

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        return dp(coins,amount);
    }
    private int dp(int[] coins, int amount){
        // 拼不出来的值，硬币数默认为最大值
        final int MAX_VALUE = Integer.MAX_VALUE;
        int[] dp = new int[amount+1];
        dp[0] = 0;
        // 填充每一个格子的数值，每一个各自是一个状态
        // 为什么是[1,amount]?答：0默认有数值，amount为答案必须填充数值
        for(int i = 1; i <= amount;i++){
            dp[i] = MAX_VALUE;
            // 遍历所有的面值
            for(int j = 0;j < coins.length;j++){
                // i代表状态，状态必须大于硬币数值才有意义
                // i-coins 表示符合转移方程的定义域
                if(i >= coins[j] && dp[i-coins[j]] != MAX_VALUE){
                    dp[i] = Integer.min(dp[i],dp[i-coins[j]]+1);
                }
            }
        }
        // 填充完毕
        // 获取答案
        return dp[amount] == MAX_VALUE ? -1 :dp[amount];
    }
}
```

