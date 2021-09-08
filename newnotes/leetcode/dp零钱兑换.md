
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

## 侯卫东老师dp四个组成部分

| 组成部分 | 硬币兑换                                                     | 不同路径                                                     | 跳跃游戏                                                     | 乘积最大连续子数组                                           |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 确定状态 | `dp[i]`表示数值i可由最少多少枚硬币组成                       | `dp[i][j]` ：表示从`（0 ，0）`出发，到`(i, j)` 有`dp[i][j]`条不同的路径 | `can[i]`表示能否跳到坐标i                                    | 子问题，$f[i]$是以$a[i]$结尾最大乘积,$g[i]$是以$a[i]$结尾最小乘积 |
| 转移方程 | $dp[i] = min(dp[i-coins[0]]+1,dp[i-coins[1]]+1,...,dp[i-coinfs[n-1]]+1)$ | `dp[i][j] = dp[i - 1][j] + dp[i][j - 1]`                     | `can[j] && j + A[j] >= i`<br>j + A[j] >= i`表示从位置j可以调到位置i | $res=max(a_i*f[i],a_i*g[i],res)$，res用来打擂台<br>$f[i]=max(a[i],max(a[i]*f[i-1],a[i]*g[i-1]))$,<br>$g[i]=min(a[i],min(a[i]*f[i-1],a[i]*g[i-1]))$ |
| 初始化   | `dp[0] = 0`,无法由硬币组成的也表示为最大值，如硬币为2，5，7，则`dp[1]=dp[3]=Integer.MAX_VALUE` | `dp[i][0] = 1;dp[0][j] = 1;`                                 | `can[0]=true`                                                | $f[i]=nums[i],g[i]=nums[i]$,$res=Integer.MINVALUE$           |
| 计算顺序 | `dp[x]`                                                      | 第一行，第二行...第n-1行,答案`dp[m-`][n-1]`                  | `can[0],can[1],can[2]..can[n-1]`，答案`can[n-1]`             | $f[0],g[0],f[1],g[1]...f[n-1],g[n-1]$，答案是$max(f[0],f[1],f[2]...f[n-1])$ |

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  dp动态规划 | 下文代码实现  |  ||

状态定义：`dp[i]`表示数值i可由最少多少枚硬币组成

转移方程：$dp[i] = min(dp[i-coins[0]+1,dp[i-coins[1]]+1,...,dp[i-coinfs[n-1]]+1)$

初始化：`dp[0] = 0`,`dp[-1] = Integer.MAX_VALUE`,`dp[-2] = Integer.MAX_VALUE`,以及无法由硬币组成的也表示为最大值，如硬币为2，5，7，则`dp[1]=dp[3]=Integer.MAX_VALUE`

答案：`dp[x]`

抽象计算过程：

![dp硬币兑换](http://cdn.yangchaofan.cn/typora/dp硬币兑换.png)

!>dp[5]计算错了，表格里的值是对的，勘误。

此题的难点在于理解`dp[i]`：

1. $dp[i]$是指凑成面值总额为$i$的最少硬币数量,如dp[3]=3表示需要最少3枚硬币才能凑出总面值为3
2. $i$有两个含义，第一个含义是指dp数组的下标，表示即将填充数组的位置；第二个含义是指当前位置代表的总面值，如dp[3]代表的是面值总额为3的硬币最少数量

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
                // i-coins 表示符合转移方程的定义域,dp[i-coins[j]]有意义
                // dp[i-coins[j]] != MAX_VALUE 表示面值为i-coins[j]是能够凑出来的，如果凑不出来就是最大值
                // i>=coins[j]表示总面值有被coins凑出来的可能，如果总面值小于硬币面值，则不可能被该硬币凑出
                // i>=coins[j] 表示面值i是有可能被coins里面的元素出来的
                // dp[i-coins[j]]!= 表示面值i-coins[j]有可能被coins凑出来
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

