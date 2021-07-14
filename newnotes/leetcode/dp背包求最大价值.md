
#  背包问题（二）

## 题目链接

[背包问题（二）](https://www.lintcode.com/problem/125/)

## 题目描述

描述

有 `n` 个物品和一个大小为 `m` 的背包. 给定数组 `A` 表示每个物品的大小和数组 `V` 表示每个物品的价值.

问最多能装入背包的总价值是多大?



`A[i], V[i], n, m` 均为整数 你不能将物品进行切分 你所挑选的要装入背包的物品的总大小不能超过 `m` 每个物品只能取一次 m<=1000m <= 1000m<=1000\ len(A),len(V)<=100len(A),len(V)<=100len(A),len(V)<=100

样例

**样例 1：**

输入：

```
m = 10
A = [2, 3, 5, 7]
V = [1, 5, 2, 4]
```

输出：

```
9
```

解释：

装入 A[1] 和 A[3] 可以得到最大价值, V[1] + V[3] = 9

**样例 2：**

输入：

```
m = 10
A = [2, 3, 8]
V = [2, 5, 8]
```

输出：

```
10
```

解释：

装入 A[0] 和 A[2] 可以得到最大价值, V[0] + V[2] = 10

挑战

O(nm) 空间复杂度可以通过, 你能把空间复杂度优化为O(m)吗？

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  动态规划法 | 下文代码实现  | $O(1)$|$(1)$|

## 背包问题拆解

假设有4个物品，它的价值设为V，重量设为W，可以抽象为如下图表示


<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://cdn.yangchaofan.cn/typora1043143-20190502083921233-1760426237.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图1-01背包抽象思考坐标系
  	</div>
</center>



背包总容量为10，现在要从中选择物品装入背包中，要求物品的重量不能超过背包的容量，并且最后放在背包中物品的总价值最大。

emmm，等等，为什么叫做`0/1背包`呢？为什么不叫`1/2背包`，`2/3背包`？？？

仔细想想，这里每个物品只有一个，对于每个物品而言，只有两种选择，盘它或者不盘，盘它记为1，不盘记为0，我们不能将物品进行分割，比如只拿半个是不允许的。这就是这个问题被称为`0/1背包`问题的原因。

所以究竟选还是不选，这是个问题。

**首先**将这个问题**转为二维数组**

![背包的抽象思考过程](http://cdn.yangchaofan.cn/typora/背包的抽象思考过程.jpg)

表格中，每一个格子代表一个子问题。

**接着**我们为问题建模

1. 设物品的重量为$W_i,i=(1,2,3,...)$,物品的价值为$V_i,i=(1,2,3,...)$,物品取出或不取出的状态为$X_i,i=(1,2,3,...)$，i代表第几个物品，$w_1$表示第一个物品的重量，$v_1$表示第一个物品的价值，$x_1$表示第一个物品选择或不选择的状态,物品总量为`n`，背包总容量为`c`，可以放下最大重量为`c`的物品；

2. 背包问题即**求组合数的最值**$max(x_1 \times v_1+x_2 \times v_2+x_3 \times v_3...+x_n \times v_n)$

3. 背包问题的**约束条件**为$x_1 \times w_1+x_2 \times w_2+...x_n \times w_n < c$

4. 定义**状态**为$KS(i,j)$,$i$表示前`i`个物品；$j$表示背包剩余容量为`j`

5. 定义**状态转移方程**之前，先思考一下递推关系式，当我们询问第`i`个物品，关于背包剩余容量$j$与物品重量$w_i$的大小比较关系，此时会面临两种结果

   1. 若背包剩余容量不足以容纳该物品$j<w_i$,此时背包无法存入物品`i`，由此条件导致，背包此时的价值与访问前`i-1`个物品的价值是一样的，$KS(i,j)=KS(i-1,j)$

   2. 若背包剩余容量可以容纳该物品$j>w_i$，此时要不要将物品i装入背包$x_i$，又产生了两种结果,我们取两种结果的最大值,赋值给$KS(i,j)$

      1. 不装该物品i，此时背包的价值不变，$KS(i,j)=KS(i-1,j)$
      2. 装该物品i，$KS(i,j)=KS(i-1,j-w_i)+v_i$

      求两者最值$KS(i,j)=max[KS(i-1,j),KS(i-1,j-w_i)]+v_i $

6. 由第5条可知**方程**为

   1. $j>w_i$,$KS(i,j)=max[KS(i-1,j),KS(i-1,j-w_i)]+v_i $
   2. $j<w_i$,$KS(i,j)=KS(i-1,j)$

其次我们将数组**初始化**，初始化其0行0列的最大价值为0:



![背包的抽象思考过程-初始化](http://cdn.yangchaofan.cn/typora/背包的抽象思考过程-初始化.jpg)

最后我们将数组**填充满**：

**填充第一行**



![01背包抽象过程1行](http://cdn.yangchaofan.cn/typora/01背包抽象过程1行.jpg)





**填充第二行**

![背包的抽象思考过程第二行](http://cdn.yangchaofan.cn/typora/背包的抽象思考过程第二行.jpg)



**填充第三行**



![背包的抽象思考过程第三行](http://cdn.yangchaofan.cn/typora/背包的抽象思考过程第三行.jpg)

**填充第四行**：

![01背包抽象过程4行](http://cdn.yangchaofan.cn/typora/01背包抽象过程4行.jpg)

**动态四要素为：**

**状态：**`dp[i][j]`表示前i个物品，最大价值为j

**方程：**

1. `  results[m][n] = results[m-1][n];`
2. ` results[m][n] = results[m-1][n-ws[m]] + vs[m];`

**初始化：**

```java
for (int m = 0; m <= i; m++){
    results[m][0] = 0;
}
for (int m = 0; m <= j; m++){
    results[0][m] = 0;
}
```

**答案：**`results[i][j];`

```java
public class Solution{
    int[] vs = {0,2,4,3,7};
    int[] ws = {0,2,3,5,5};
    Integer[][] results = new Integer[5][11];
    
    @Test
    public void testKnapsack3() {
        int result = ks3(4,10);
        System.out.println(result);
    }

    private int ks3(int i, int j){
        // 初始化
        for (int m = 0; m <= i; m++){
            results[m][0] = 0;
        }
        for (int m = 0; m <= j; m++){
            results[0][m] = 0;
        }
        // 开始填表
        for (int m = 1; m <= i; m++){
            for (int n = 1; n <= j; n++){
                if (n < ws[m]){
                    // 装不进去
                    results[m][n] = results[m-1][n];
                } else {
                    // 容量足够
                    if (results[m-1][n] > results[m-1][n-ws[m]] + vs[m]){
                        // 不装该珠宝，最优价值更大
                        results[m][n] = results[m-1][n];
                    } else {
                        results[m][n] = results[m-1][n-ws[m]] + vs[m];
                    }
                }
            }
        }
        return results[i][j];
    }
}
```

## 代码实现

```java
public class Solution {
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A & V: Given n items with size A[i] and value V[i]
     * @return: The maximum value
     */

    public int backPackII(int m, int[] A, int V[]) {
        // write your code here
        int[][] dp = new int[A.length + 1][m + 1];
        for (int i = 0; i <= A.length; i++) {
            for (int j = 0; j <= m; j++) {
                if (i == 0 || j == 0) {
                    dp[i][j] = 0;
                } else if (A[i - 1] > j) {
                    dp[i][j] = dp[(i - 1)][j];
                } else {
                    dp[i][j] = Math.max(dp[(i - 1)][j], dp[(i - 1)][j - A[i - 1]] + V[i - 1]);
                }
            }
        }
        return dp[A.length][m];
    }
}

```