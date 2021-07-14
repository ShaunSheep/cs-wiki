
#  背包问题

## 题目链接

[背包问题](https://www.jiuzhang.com/problem/backpack/)

## 题目描述

#### 描述

在`n`个物品中挑选若干物品装入背包，最多能装多满？假设背包的大小为`m`，每个物品的大小为Ai

Ai



你不可以将物品进行切割。

#### 样例

**样例 1：**

输入：

```
数组 = [3,4,8,5]
backpack size = 10
```

输出：

```
9
```

解释：

装4和5.

**样例 2：**

输入：

```
数组 = [2,3,5,7]
backpack size = 12
```

输出：

```
12
```

解释：

装5和7.

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 动态规划法 | 下文代码实现  | $O(1)$|$(1)$|

## 算法：DP

从已知的题目中，可以总结出以下两点：

- 每件物品只有一种
- 每件物品最多选择一次

那么考虑对于前i件的物品在容量为w的背包下，最大的装载量是多少，由此可以总结出对应的子结构，进行动态规划。

### 算法思路

设计`dp`数组`dp[n][m]`，用`dp[i][j]`表示第i个物品在容量为j的背包下，最大的装载量。

在这个问题中，若只考虑第i件物品的策略（放或不放），那么就可以转化为一个只牵扯前i−1件物品的问题：

- 如果不放第i件物品，可得`dp[i][j]=dp[i−1][j]`

- 如果放了第i件物品，可得`dp[i][j]=dp[i−1][j−A[i]]+A[i](j≥A[i])`

总结状态转义方程为：`dp[i][j]=max(dp[i−1][j],dp[i−1][j−A[i]]+A[i])`

### 复杂度分析

n表示物品件数，m表示背包容量

- 时间复杂度：O(nm)
- 空间复杂度：O(nm)

## 代码实现

```java
public class Solution {
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    public int backPack(int m, int[] A) {
        boolean f[][] = new boolean[A.length + 1][m + 1];
        for (int i = 0; i <= A.length; i++) {
            for (int j = 0; j <= m; j++) {
                f[i][j] = false;
            }
        }
        f[0][0] = true;
        for (int i = 1; i <= A.length; i++) {
            for (int j = 0; j <= m; j++) {
                f[i][j] = f[i - 1][j];
                if (j >= A[i-1] && f[i-1][j - A[i-1]]) {
                    f[i][j] = true;
                }
            } // for j
        } // for i
        
        for (int i = m; i >= 0; i--) {
            if (f[A.length][i]) {
                return i;
            }
        }
        
        return 0;
    }
}
```



