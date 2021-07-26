
#  最长上升连续子序列

## 题目链接

[最长上升连续子序列](https://www.lintcode.com/problem/397/?_from=collection&fromId=161)

## 题目描述

描述

给定一个整数数组（下标从 0 到 n-1， n 表示整个数组的规模），请找出该数组中的最长上升连续子序列。（最长上升连续子序列可以定义为从右到左或从左到右的序列。）

样例

**样例 1：**

```
输入：[5, 4, 2, 1, 3]
输出：4
解释：
给定 [5, 4, 2, 1, 3]，其最长上升连续子序列（LICS）为 [5, 4, 2, 1]，返回 4。
```

**样例 2：**

```
输入：[5, 1, 2, 3, 4]
输出：4
解释：
给定 [5, 1, 2, 3, 4]，其最长上升连续子序列（LICS）为 [1, 2, 3, 4]，返回 4。
```

挑战

使用 O(n) 时间和 O(1) 额外空间来解决

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 动态规划 | 下文代码实现  | $O(1)$|$(1)$|
本题为直接去找一个数组的最长连续上升/下降子序列
- 先从正序找到一个最长的连续递增长度r1
- 再翻转数组，找到一个最长的连续递增长度r2
- 返回r1和r2的最大值


## 代码实现

```java
public class Solution {
    /**
     * @param A: An array of Integer
     * @return: an integer
     */
    public int longestIncreasingContinuousSubsequence(int[] A) {
        // write your code here
        // 回退条件
        int n = A.length;
        if(n == 0){
            return 0;
        }
        // 求从左到右的最长子序列长度
        int r1 = LIS(A,n);
        // 颠倒数组
        int i = 0;
        int j = n-1;
        int t = 0;
        while (i <= j){
            t = A[i];
            A[i] = A[j];
            A[j] = t;
            i++;
            j--;
        }
        // 求从右到左的最长子序列长度
        int r2 = LIS(A,n);
        return r1 > r2 ? r1 : r2;
    }
    public int LIS(int[] A, int n){
        int[] dp = new int[n];
        // max{dp[0],dp[1],dp[2]...dp[n-1]};
        int res = 0;
        for(int i = 0 ;i < n;i++){
            dp[i] = 1;
            if(i > 0&& A[i] > A[i-1]){
                dp[i] = dp[i-1] + 1;
            }
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```