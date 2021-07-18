
#  爬楼梯II

## 题目链接

[爬楼梯II](https://www.lintcode.com/problem/272/?_from=collection&fromId=161)

## 题目描述

描述

一个小孩爬一个 n 层台阶的楼梯。他可以每次跳 1 步， 2 步 或者 3 步。实现一个方法来统计总共有多少种不同的方式爬到最顶层的台阶。

对于`n=0`，我们认为答案是1。

样例

**样例1：**

```
输入: 3
输出: 4
解释: 1 + 1 + 1 = 2 + 1 = 1 + 2 = 3 = 3 , 一共4种方法。
```

**样例2:**

```
输入: 4
输出: 7
解释: 1 + 1 + 1 + 1 = 1 + 1 + 2 = 1 + 2 + 1 = 2 + 1 + 1 = 2 + 2 = 1 + 3 = 3 + 1 = 4 , 一共7种方法
```

## 滚动数组解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 记忆化搜索 | 滚动数组法 | $O(n)$|$(1)$|

我们使用动态规划解决本题。

- 爬到第i阶可能有3种情况：
  - 在第`i-1`阶后爬1阶
  - 在第`i-2`阶后爬2阶
  - 在第`i-3`阶后爬3阶
- 所以到达第`i`阶的方法数是这三种情况之和。
- 令 `dp[i]` 表示能到达第`i`阶的方法总数，那么有： $dp[i]=dp[i−1]+dp[i−2]+dp[i−3]$dp[i]=dp[i−1]+dp[i−2]+dp[i−3]$
- 为了节省空间，可以循环利用变量，计算第`i`位时，只保存该位数的前三位数字。

## 滚动数组算法流程

- 处理corner case：如果n为0,1,2，分别返回1,1,2
- 建立变量`a`，`b`和`c`，分别表示我们上述的`dp[i-3]`, `dp[i-2]` 和`dp[i-1]`
- 进行动态规划，对台阶数遍历，当有`i`个台阶时，方法数有`next = a + b + c`个，然后我们循环移动`a`,`b`和`c`，继续遍历。

图解过程，参考[爬楼梯](newnotes/leetcode/爬楼梯#滚动数组画法)

![滚动数组-爬楼梯](https://cdn.yangchaofan.cn/typora%E6%BB%9A%E5%8A%A8%E6%95%B0%E7%BB%84-%E7%88%AC%E6%A5%BC%E6%A2%AF.gif)

## 滚动数组代码实现

```java
public class Solution {
    /**
     * @param n: An integer
     * @return: An Integer
     */
    public int climbStairs2(int n) {
        if (n <= 1){
            return 1;
        }
        if (n == 2){
            return 2;
        }
        int a = 1, b = 1, c = 2;
        for (int i = 3; i < n + 1; i ++){
            int next = a + b + c;
            a = b;
            b = c;
            c = next;
        }
        return c;
    }
}
```
## dp解题代码实现

```java
public class Solution {
    /**
     * @param n: An integer
     * @return: An Integer
     */
    public int climbStairs2(int n) {
        if (n == 0) {
            return 1;
        } 
        if (n == 1 || n == 2) {
            return n;
        }
        
        // State: dp[i] 表示从起点0到终点i的方案数
        int[] dp = new int[n + 1];
        
        // Initialization
        dp[0] = 1;
        dp[1] = 1;
        dp[2] = 2;
        
        // Function
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2] + dp[i - 3];
        }
        
        // Answer
        return dp[n];
    }
}

```

## 递归解题代码实现

```java
public class Solution {
    /**
     * @param n: An integer
     * @return: An Integer
     */
    public int climbStairs2(int n) {
        return dfs(n, new HashMap<>());  
    }
    
    // 递归的定义：返回从起点0到终点i的方案数
    private int dfs(int i, Map<Integer, Integer> memo) {
        // 递归的出口
        if (i == 0 || i == 1) {
            return 1;
        }
        
        if (i == 2) {
            return 2;
        }
        
        if (memo.containsKey(i)) {
            return memo.get(i);
        }
        
        // 递归的拆解
        int num = dfs(i - 1, memo) + dfs(i - 2, memo) + dfs(i - 3, memo);
        memo.put(i, num);
        
        return num;
    }
}

```