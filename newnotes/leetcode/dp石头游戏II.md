
#  石头游戏 II

## 题目链接

[石头游戏 II](https://www.lintcode.com/problem/593/?_from=collection&fromId=161)

## 题目描述

## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  动态规划法 | 区间dp  | $O(1)$|$(1)$|

算法：区间dp
思路
区间DP通常用来求区间最值问题，通过合并小区间最优解来更新大区间，然后逐渐更新出答案。
要求解在一个区间上的最优解，那么就要把这个区间分割成一个个小区间，求解每个小区间的最优解，再合并小区间得到大区间。所以在代码实现上，第一层先枚举区间长度len为每次分割成的小区间长度（由短到长不断合并），第二层按照小区间长度，枚举起点和计算出终点，然后第三层循环在这个起点与终点之间枚举分割点，求解这段小区间在某个分割点下的最优解。
一句话总结：先枚举区间长度，再枚举左端点，最后枚举区间分割点进行状态转移。
代码模板：
```java
 for(int len=2;len<=n;len++){ //枚举长度
        for(int i=1;i+len-1<=n;i++){ //枚举起点
            int j=i+len-1;
            for(int k=i;k<j;k++){ //枚举分割点，更新小区间最优解
                dp[i][j]=min(dp[i][j],dp[i][k]+dp[k+1][j]+something);
            }
        }
    }
```
假设k∈[i,j)，dp[i][j]表示通过合并两个区间[i,k]和[k+1,j]的最优解，每次合并两个区间[i,k]和[k+1,j]的代价为区间[i,j]的所有元素之和。为了减小时间复杂度，我们可以提前做一次前缀和保存在数组prefixSum[]，这样就能用O(1)的时间查询区间[i,j]的所有元素之和了，区间[i,j]的所有元素之和=prefixSum[j]-prefixSum[i-1]。
边界值：dp[i][i]=0。
状态转移方程如下：
```java
dp[i][j]=min(dp[i][j],dp[i][k]+dp[k+1][j]+prefixSum[j]-prefixSum[i-1]);
```

因为这道题要求石子堆是环形的，所以A[n-1],A[0]也是一段可以合并的连续区间，因此我们可以把给定的石子堆按原顺序延长一倍。这样就避免了计算区间“左端点在尾部，右端点在头部”情况的麻烦。比如1，2，1可以扩张至1，2，1，1，2，1。这样原区间的所有集合都可以在新的数组中以连续下标的形式表示。
我们最后得到的dp[i][i+n-1]即为以第i堆石子堆作为起点的n堆石子堆合并的最小代价。最后比较每一种可能得到的最小值就是答案。

复杂度
假设有n堆石子堆；
空间复杂度为O(n^2)；
时间复杂度为O(n^3)。

## 代码实现
```java
public class Solution {
    /**
     * @param A: An integer array
     * @return: An integer
     */
    public int stoneGame2(int[] A) {
        int n = A.length;
        if (n == 0) {
            return 0;
        }
        int[] prefixSum = new int[2 * n + 1];
        int[][] dp = new int[2 * n + 1][2 * n + 1];
        prefixSum[0] = 0;
        for (int i = 1; i <= 2 * n; i++) {
            // 做一次前缀和，减少时间复杂度
            prefixSum[i] = prefixSum[i - 1] + A[(i - 1) % n];
            for (int j = 1; j <= 2 * n; j++) {
                if (i != j) {
                    dp[i][j] = Integer.MAX_VALUE;
                }
            }
        }
        // 枚举长度
        for (int len = 2; len <= n; len++) {
            // 枚举起点
            for (int i = 1; i + len - 1 <= 2 * n; i++) {
                // j为i为起点时的石子堆终点
                int j = i + len - 1;
                for (int k = i; k < j; k++) {
                    dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k + 1][j] + prefixSum[j] - prefixSum[i - 1]);
                }
            }
        }
        int ans = Integer.MAX_VALUE;
        for (int i = 1; i <= n; i++) {
            // 取最小的 以第i堆石子堆作为起点的n堆石子堆合并的最小代价
            ans = Math.min(ans, dp[i][i + n - 1]);
        }
        return ans;
    }
}
```
