
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

**两种问法:**

1. n个物品,m大小的背包，问最多能装多满

2. 给出n个物品及其大小 ,问是否能挑选出一些物品装满大小为m的背包

**问题：属于动态规划哪一场景**

答：属于动态规划求解可行性的场景

**问题：01是什么意思**

答：每个物品要么挑0个(不挑)要么挑1个，所以叫01 。如果一个物品可以被分割，就不是01背包 ；如果一个物品可以选多份，可以取1份，取0份，也可以取3份等，就叫多重背包

**问题：背包的状态如何表示**

`dp[i][j]`表示**前**`i`**个**物品里挑出若干物品**组成和**为`j`的大小是否可行

 !>背包两个关键点**:前&和**

**问题：背包有几种状态定义**

答：两种

## 第一种表示方法代码

`dp[i][j]`表示前`i`个数里是否能凑出`j`的和,`dp[i][j]`的值为true/false 

状态：`dp[i][j]`表示前i个数里挑若干个数是否能组成和为j

方程：

- 如果`j>=A[i-1]`时，`dp[i][j]=dp[i-1][j] or dp[i-1][j-A[i-1]]`
- `dp[i][j]`前i个数能否凑合为j的和，需要先判断第i个数
- `如果`j<A[i-1]`dp[i][j]=dp[i-1][j]`
- 第`i`个数的下标是`i-1`，所以用的是`A[i-1]`而不是`A[i]`

初始化：

`dp[0][0]=true` ,第0个数，和也为0，所以初始化为true

`dp[0][1...m]=false`，因为不存在0个数，凑成和为1...m，所以初始化为`false`

答案：使得`dp[n][v]`,$0<=v<=m$为true的最大v

```java
public class Solution {
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    public int backPack(int m, int[] A) {
        // write your code here
        int n = A.length;
        boolean[][] dp = new boolean[n+1][m+1];
        // 初始化第一个点
        dp[0][0] = true;
        for(int i = 1;i <= n ;i++){
            for(int j = 0;j <= m;j++){
                if(j >= A[i-1]){
                    dp[i][j] = dp[i-1][j]||dp[i-1][j-A[i-1]];
                }else{
                    dp[i][j] = dp[i-1][j];
                }
            }
        }

        for(int v = m;v >= 0;v--){
            if(dp[n][v]){
                return v;
            }
        }
        return -1;
    }
}
```



## 第二种表示方法代码

`dp[i][j]`表示前i个数能否凑出的<=j的最大和是多少

状态：`dp[i][j]`表示前i个数里挑出若干个数总和<=j的最大和是多少

方程：

- `dp[i][j] =max(dp[i-1][j],dp[i-1][j-A[i-1]]+A[i-1])`如果`j>=A[i-1]`

- `dp[i][j]`=`dp[i-1][j]`如果`j<A[i-1]`

- 第i个数的下标是`i-1`，所以用的是`A[i-1]`而不是`A[i]`

初始化：`dp[0][0..m]=0`

答案：`dp[n][m]`

```java
public class Solution {
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    public int backPack(int m, int[] A) {
        // write your code here
        if(A == null || A.length ==0){
            return 0;
        }

        int n = A.length;
        int[][] dp = new int[n+1][m+1];

        for(int i = 1;i <= n ;i++){
            for(int j = 0; j <= m;j++){
                if(j >= A[i-1]){
                    // 背包剩余空间放的下
                    dp[i][j] = Math.max(dp[i-1][j],dp[i-1][j-A[i-1]]+A[i-1]);
                }else{
                    // 背包剩余空间放不下
                    dp[i][j] = dp[i-1][j];
                }
            }
        }
        return dp[n][m];
    }
}
```

