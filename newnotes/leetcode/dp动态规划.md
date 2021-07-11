
# 动态规划

## 动态规划定义

一种算法思想，简称dp，不是具体的算法

?>动态规划与递归、分治法同属于大问题化小问题的思想，都是解决大问题的有效解题思想。通常表现为大问题的结果依赖于小问题的结果

!>有兴趣可以列出动态规划与贪心、分治、递归的区别。

## 实现思路

实现方式有两种：
1.记忆化搜索，递归实现
2.迭代循环，多重循环

## 实现四要素

实现dp通常需要搞清楚四个要素：状态、方程、初始化、答案

### DP状态

状态的介绍：
- 用$f[i]$ 或者$f[i][j]$代表某些特定条件下规模更小问题的答案
- 规模更小的问题，用参数i，j之类的来划定

状态的提问：
1. `f[i]`是什么？
答：代表矩阵上的某一层结果
2. `f[i][j]`是什么？
答：不同的dp方程，表示的含义不同，
针对不同路径的题目
如自底向上的方程，`dp[i][j]`表示的含义是点`(i,j)`走到最底层叶节点的不同路径和
如自顶向下的方程，`dp[i][j]`表示的含义是点`(0,0)`走到`(i,j)`的不同路径和
3. 矩阵是如何抽象出来的？
可以参考[三角形的矩阵抽象过程](#三角形的矩阵抽象过程)
4. 状态这一要素有什么用途？
答：个人猜测是，方便描述矩阵上某一层所有节点的状态，或者描述递归搜索树上某一节点的状态


### DP方程
- 大问题拆解为对小问题的依赖
- $f[i][j]=$ 通过规模更小的一些状态,对这些状态求$max、min、sum、or$来进行推导

1. 常见的大问题有哪些？
答：不同路径、最短路径，详情参考dp的题目类型
2. 常见大问题拆成小问题的例子？
答：数字三角形，骑士最短路径
3. 常见推导DP方程的过程，文字描述出来。<br>
答：参考dp的题目列表

### DP初始化
- 设定无法再拆解的极限小的状态下的值
- 这些值无法用方程、通项公式来表示
- 如$f[i][0]$或者$f[0][i]$

### DP答案
- 最后的答案是什么，答案的最后一步是什么
- 如$f[i][j]$或者$f[0][0]、min(f[n][1],f[n][2]...)$



接下来我们以动态规划四要素为例，学习一下[数字三角形](newnotes/leetcode/数字三角形)动态规划的解法。

## 四要素入门例题数字三角形

### 三角形的矩阵抽象过程

回忆一下[数字三角形](newnotes/leetcode/数字三角形)的矩阵抽象过程

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://cdn.yangchaofan.cn/typora%E8%AE%B0%E5%BF%86%E5%8C%96%E6%B3%95%E5%81%9A%E6%95%B0%E5%AD%97%E4%B8%89%E8%A7%92%E5%BD%A2-%E6%95%B0%E6%8D%AE%E7%9A%84%E6%8A%BD%E8%B1%A1%E8%BF%87%E7%A8%8B.svg" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图1-记忆化法做数字三角形-数据的抽象过程
      </div>
</center>

### 三角形的矩阵坐标表示法
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/矩阵坐标的两种抽象方式.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图2-两种不同的坐标抽象方式
      </div>
</center>

### 自底向上-数字三角形

时间复杂度：$O(n^2)$

空间复杂度：$O(n^2)$

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/BlogGifRes/20210326/wk9himKSUree.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图2-动态规划DP-自底向上
      </div>
</center>


1. 自底向上是指什么？
答：是指两个方向，一是点的依赖，当前点可能来自于哪些方向，对于 自底向上来说，当前点皆来自于底部；二是指行走的方向，对于矩阵遍历的方向，遍历的方向是从叶节点向上至根节点。
2. 如何理解贪心追求的是当前利益。
答：我的理解是贪心只关注当前这一步的最佳值（最大、最小）
3. 如何理解DP追求的是长远利益。
答：我的理解是DP关注的是所有走法的最佳值（最大、最小）
4. 贪心算法常见的题目是什么？贪心算法的定义是什么？

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/动态规划自底向上.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图3-动态规划DP-自底向上
      </div>
</center>

递归四要素为：

状态：`f[i][j]`代表从`（i，j）`点走到最底层的最短路径

动态方程:` f[i][j] = Math.min(f[i + 1][j], f[i + 1][j + 1]) + triangle[i][j];`

初始化：起始点是底部那一层的叶节点，底部那一层的叶节点也将作为初始化的第一批点，`i = n - 2`

终点：`(0,0)`

```java
public class Solution {
    /**
     * @param triangle: a list of lists of integers.
     * @return: An integer, minimum path sum.
     */
    public int minimumTotal(int[][] triangle) {
        if (triangle == null || triangle.length == 0) {
            return -1;
        }
        if (triangle[0] == null || triangle[0].length == 0) {
            return -1;
        }
        
        // state: dp[i][j] 代表从 i,j  走到最底层的最短路径值
        int n = triangle.length;
        int[][] f = new int[n][n];
        
        // initialize: 初始化终点（最后一层）
        for (int i = 0; i < n; i++) {
            f[n - 1][i] = triangle[n - 1][i];
        }
        
        // 从下往上倒过来推导，计算每个坐标到哪儿去
        for (int i = n - 2; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                f[i][j] = Math.min(f[i + 1][j], f[i + 1][j + 1]) + triangle[i][j];
            }
        }
        
        // answer: 起点就是答案
        return f[0][0];
    }
}
```





### 自顶向下-数字三角形

时间复杂度：$O(n^2)$

空间复杂度：$O(n^2)$



<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/image-20210707112700307.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图4-动态规划DP-自顶向下
      </div>
</center>

?> 自顶向下是指什么？

答：是指两个方向，一是点的依赖，当前点可能来自于哪些方向，对于自顶向下来说，当前点皆来自于顶部；二是指行走的方向，对于矩阵遍历的方向，遍历的方向是从根节点向下至底部的叶节点。

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/动态规划自顶向下.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图4-动态规划DP-自顶向下
      </div>
</center>


接下来，我们看一下自顶向下的四要素

四要素分别为：

状态：`dp[i][j]`，`dp[i][j]`是指从`(0,0）`点走到`(i,j)`点的最短路径

答案：`min(dp[n-1])`,`dp[n-1]`是最底一层的叶节点，`min(dp[n-1])`是指最底一层叶节点的最小值，即`dp[n][i]`使`dp[n]`最小的值

动态方程： `dp[i][j] = Math.min(dp[i - 1][j], dp[i - 1][j - 1]) + triangle[i][j];`

初始化：`(0，0)`为起始点，初始化的点是两条边`dp[i][0]`、`dp[i][i]`，因为这两条边没有左上角的店和正上方的点。


```java
public class Solution {
    /**
     * @param triangle: a list of lists of integers
     * @return: An integer, minimum path sum
     */
    public int minimumTotal(int[][] triangle) {
        if (triangle == null || triangle.length == 0) {
            return -1;
        }
        if (triangle[0] == null || triangle[0].length == 0) {
            return -1;
        }
        
        // state: dp[x][y] = minimum path value from 0,0 to x,y
        int n = triangle.length;
        int[][] dp = new int[n][n];
        
        // initialize 
        dp[0][0] = triangle[0][0];
        for (int i = 1; i < n; i++) {
            dp[i][0] = dp[i - 1][0] + triangle[i][0];
            dp[i][i] = dp[i - 1][i - 1] + triangle[i][i];
        }
        
        // top down
        for (int i = 1; i < n; i++) {
            for (int j = 1; j < i; j++) {
                dp[i][j] = Math.min(dp[i - 1][j], dp[i - 1][j - 1]) + triangle[i][j];
            }
        }
        
        // answer
        int best = dp[n - 1][0];
        for (int i = 1; i < n; i++) {
            best = Math.min(best, dp[n - 1][i]);
        }
        return best;
    }
}
    
        
```


## 四要素入门例题不同路径
回忆一下[不同路径](newnotes/leetcode/不同的路径)的矩阵抽象过程

### 方格的矩阵抽象过程
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/不同路径自顶向下抽象过程.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图5-不同路径自顶向下抽象过程
      </div>
</center>

### 方格的矩阵坐标表示法
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/矩阵坐标的两种抽象方式.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图6-两种不同的坐标抽象方式
      </div>
</center>

### 自顶向下-不同路径

```java

public class Solution {
    /**
     * @param m: positive integer (1 <= m <= 100)
     * @param n: positive integer (1 <= n <= 100)
     * @return: An integer
     */
    public int uniquePaths(int m, int n) {
        // write your code here
        int[][] f = new int[m][n];
        int i, j;
        for (i = 0; i < m; ++i) {
            for (j = 0; j < n; ++j) {
                if (i == 0 || j == 0) {
                    f[i][j] = 1;
                }
                else {
                    f[i][j] = f[i-1][j] + f[i][j-1];
                }
            }
        }
        return f[m-1][n-1];
    }
}
```
递归四要素为：

状态：`f[i][j]`,代表`(i,j)`从`(0,0)`到达`(i,j)`的不同路径

初始化：`f[i][0]`、`f[0][j]`都只有1种不同的路径，其中`f[i][0]`只能从其上方走过来；`f[0][j]`都只能从其左侧走过来,初始化代码如下

```java
for (int i = 0; i < m; i++) dp[i][0] = 1;
for (int j = 0; j < n; j++) dp[0][j] = 1;
```
方程：一个点的路径之和依赖为其正上方的点、正左侧的点路径之和，因为两个点的路径走法是相互独立的，所以可以相加，`f[i][j] = f[i-1][j] + f[i][j-1];`

答案：右下角的坐标为`(m-1,n-1)`，所以答案就是该点的值`f[m-1][n-1]`

## 动态规划的题目分类

### 为什么要分类讨论动态规划

不同类型的题目，四要素状态、方程、初始化、答案皆略有不同，其中最为不同的是`状态`的定义。

做题的过程中，最为难的一点是识别出题目属于哪一种类型，只有识别出题目类型，就可以轻松找到状态的定义了。接下来就是其他三要素的定义。问题便能迎刃而解。

!>	注意以下三点：

1. 动态规划的题必须是求最优值/可行性/方案数这三种情况之一 <br>

2. 动态规划的状态依赖必须有方向性，不可以有循环依赖 坐<br>
3. 标型动态规划的状态：坐标 坐标型动态规划的方程：上一步坐标<br>

### 按场景分类

![dp相关场景](https://cdn.yangchaofan.cn/typoradp%E7%9B%B8%E5%85%B3%E5%9C%BA%E6%99%AF.svg)

### 按题型分类

![dp常见题型](https://cdn.yangchaofan.cn/typoradp%E5%B8%B8%E8%A7%81%E9%A2%98%E5%9E%8B.svg)

### 坐标型

坐标型的`状态`有两类写法

`dp[i]`表示从起点到坐标i的最优值/方案数/是否存在可行性

`dp[i][j]`表示从起点到坐标i，j的最优值/方案数/是否存在可行性

坐标的值可以是整数，也可以是布尔值

代表题目：[数字三角形]()、[不同路径](newnotes/leetcode/不同的路径)

### 前缀型之划分

`dp[i]`表示前**i个字符**的最优值/方案数/是否存在可行性

`dp[i][j]`表示**前i个字符划分为j个部分的**的最优值/方案数/是否存在可行性

代表题目：word break,word break III



### 前缀之匹配

`dp[i][j]`表示第一个字符串的前i个字符匹配上第二个字符串 的前`j`个字符的最优值/方案数/可行性

代表题:LongestCommonSubsequence,WildcardMatching 

### 区间型

`dp[i][j]`表示区间i~j的最优值/方案数/可行性 

代表题:StoneGame,BurstBalloons

### 背包型

`dp[i][j]`表示前i个物品里选出一些物品组成和为j的大小的最优 值/方案数/可行性 

代表题:Backpack系列

## 进阶典型题目

### 不同的路径 II

https://www.lintcode.com/problem/unique-paths-ii https://www.jiuzhang.com/solutions/unique-paths-ii 

在矩阵中放置一些障碍后，求左上到右下的路径数 

坐标型动态规划

### 骑士的最短路径II



https://www.lintcode.com/problem/knight-shortest-path-ii/ 

https://www.jiuzhang.com/problem/knight-shortest-path-ii/ 

只能向右面的4个方向跳，求左上到右下最短路径 

坐标型动态规划

### 跳跃游戏

https://www.lintcode.com/problem/jump-game/

https://www.jiuzhang.com/solutions/jump-game/ 

给一个一维数组，一开始站在下标0 数组中每个值代表可以向右跳跃的最大距离 问是否能跳到最右边的下标

能够用动态规划的几个因素： 1.问可行性 2.一维数组 3.有方向性

## 背包型题目

通常是二维的状态数组，**前i个**组成**和为j** 

状态数组的大小需要开`(n+1)*(m+1) `

题目中通常有**“和”与“差”**的概念，数值会被放到状态中 

每个数存在**选或者不选**两种状态（01背包） 

每个数可以选任意多个（多重背包） 数的**顺序无关**

### 背包的由来01背包问题

https://www.lintcode.com/problem/backpack 

https://www.jiuzhang.com/solutions/backpack

 n个物品,m大小的背包，问最多能装多满

给出n个物品及其大小 

问是否能挑选出一些物品装满大小为m的背包

**问题：属于动态规划哪一场景**

答：属于动态规划求解可行性的场景

**问题：01是什么意思**

答：每个物品要么挑0个(不挑)要么挑1个，所以叫01 如果一个物品可以被分割，就不是01背包 如果一个物品可以选多份，就叫多重背包

**问题：背包的状态如何表示**

`dp[i][j]`表示前i个物品里挑出若干物品组成和为j的大小是否可行 !>背包两个关键点**:前&和**

**问题：背包有几种状态定义**

答：两种

第一种表示方法：

`dp[i][j]`表示前i个数里是否能凑出j的和,true/false 

状态：dp[i][j]表示前i个数里挑若干个数是否能组成和为j

方程：

- dp[i][j]=dp[i-1][j]ordp[i-1][j-A[i-1]]如果`j>=A[i-1]`

- dp[i][j]=dp[i-1][j]`如果`j<A[i-1]`

- 第i个数的下标是i-1，所以用的是`A[i-1]`而不是`A[i]`

初始化：

`dp[0][0]=true` 

`dp[0][1...m]=false`

答案：使得`dp[n][v]`,0s<=v<=m为true的最大v



第二种表示方法：

`dp[i][j]`表示前i个数能否凑出的<=j的最大和是多少

状态：`dp[i][j]`表示前i个数里挑出若干个数总和<=j的最大和是多少

方程：

- `dp[i][j]`=max(`dp[i-1][j]`,`dp[i-1][j-A[i-1]]`+`A[i-1]`)如果`j>=A[i-1]`

- `dp[i][j]`=`dp[i-1][j]`如果`j<A[i-1]`

- 第i个数的下标是`i-1`，所以用的是`A[i-1]`而不是`A[i]`

初始化：`dp[0][0..m]=0`

答案：`dp[n][m]`

### 背包的动态解法与搜索解法

使用组合型深度优先搜索，时间复杂度$O(n \times 2^n)$

而动态规划的时间复杂度是$O(n*m)$ 

动态规划是否一定更好？

### 背包的极端场景

场景1：

[1,2,4,8,16],m=31 这种情况下,0~31的所有和都可以被凑出来 

时间复杂度O(n*m)=O(n*2^n)

场景2：

[1,1000,1000000],m=1001001 m>>2^n，反而搜索更快

### 背包问题的重复子问题是什么

[1,2,3,4,5,5,6,7,8,9,10000] 比如要凑出和为10010， 前10个数里组合出和为10的方法有5种 ，这5种组合方式就是重复子问题，因为他们凑出来的和都是10 对于后面+10000之后组成10010的影响是一样的 ，我们不关心10是怎么凑的，只关心10能不能被凑出来

### 背包进阶最小划分

https://www.lintcode.com/problem/minimum-partition https://www.jiuzhang.com/solutions/minimum-partition 

将数组划分为两组，两组分别求和之后的差最小 狗家真题

划分扩展，题目变换：

设整个数组所有数之和为SUM,其中一个较小的数组之和为X 问题变为求|SUM-X-X|=|SUM-2X|的最小值 即求使得X尽可能接近SUM/2的最大值 =数组中挑出若干数尽可能的填满一个大小为SUM/2的背包

### 背包进阶外卖优惠卷

你有一张满X元减10元的满减券和每个菜品的价格（整数），每 个菜最多只买一份。问最少买多少钱可以用得上这张外卖券？ 

挑选若干数之和>=X且和最小 =挑选出一些“不加入购物车”的菜品 尽可能填满一个SUM-X的背包

### 背包进阶石头碰撞

给出N个石头及其大小数组 每次选2个石头进行碰撞，大小分别为x,y

碰撞之后会变成一个石头，大小变为|x-y|

直到石头个数<2为止 问最后剩下来的石头最小是多少？

本质上这个题的答案=最小划分那一题的答案

### 背包进阶带价值的01背包

https://www.lintcode.com/problem/backpack-ii/ https://www.jiuzhang.com/solutions/backpack-ii/ 

每个物品有体积A[i]和价值V[i] 

问能装进一个m大小的包里的最大“价值”总和是多少 Example:A=[2,3,5,6],V=[1,5,2,4],m=10

**四要素分析**

状态:`dp[i][j]`表示前i个物品挑出一些放到j的背包里的最大价值和 

方程:`dp[i][j]=max(dp[i-1][j],dp[i-1][j-A[i-1]]+V[i-1]) `

初始化:`dp[0][0..m]=0 `

答案:`dp[n][m]`

### 背包进阶多重背包

https://www.lintcode.com/problem/backpack-iii/ https://www.jiuzhang.com/solutions/backpack-iii/ 

相比上一个题，这个题的物品可以取任意个数 Example:A=[2,3,5,6],V=[1,5,2,4],m=10

**四要素分析**

状态:dp[i][j]表示前i个物品挑出一些放到j的背包里的最大价值和 

方程:`dp[i][j]=max(dp[i-1][j-count*A[i-1]]+count*V[i-1])` 其中0<=count<=j/A[i-1] 

初始化:`dp[0][0..m]=0 `

答案:`dp[n][m]`

?> <br>
[九章算法2020](https://www.lintcode.com/ladder/161/) <br>
[【labuladong】动态规划核心套路详解](https://www.bilibili.com/video/BV1XV411Y7oE?from=search&seid=15811028411645469585) <br>
[动态规划解题套路框架](https://labuladong.gitbook.io/algo/mu-lu-ye-2/mu-lu-ye/dong-tai-gui-hua-xiang-jie-jin-jie) <br>
[【动态规划专题班】ACM总冠军、清华+斯坦福大神带你入门动态规划算法](https://www.bilibili.com/video/BV1xb411e7ww?from=search&seid=15749315408323378438) <br>