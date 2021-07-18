
# 动态规划

## 动态规划定义

**动态规划**（Dynamic Programming，DP）是运筹学的一个分支，是求解[决策过程](https://baike.baidu.com/item/决策过程/6714639)最优化的过程。

简而言之，动态规划是一种算法设计思想，简称dp，不是具体的算法

在计算机程序设计领域，为了判断一个问题是否适用动态规划思想，需要判断两个**关键属性**。为了学习动态规划思想，也需要关注这两个**关键属性**。

这两个关键属性就是：最优子结构、重叠子问题。其中最优子结构也是分而治之策略的基础。

?>如果一个问题可以通过组合*非重叠*子问题的最优解来解决，则该策略被称为“[分而治之](https://en.wikipedia.org/wiki/Divide_and_conquer_algorithm)”。[[1\]](https://en.wikipedia.org/wiki/Dynamic_programming#cite_note-:0-1) 这就是[归并排序](https://en.wikipedia.org/wiki/Mergesort)和[快速排序](https://en.wikipedia.org/wiki/Quicksort)不属于动态规划问题的原因。

*最优子结构*是指通过组合其子问题的最优解，可以得到给定优化问题的解。这种最优子结构通常通过[递归](https://en.wikipedia.org/wiki/Recursion)来描述。例如，给定一个图*G=(V,E)*，从顶点*u*到顶点*v*的最短路径*p*表现出最佳子结构：在这条最短路径*p*上取任何中间顶点*w*。如果*p*是真正的最短路径，那么它可以被分成子路径*p* *1*从*ù*到*瓦特*和*p* *2*从*w*到*v*使得这些反过来确实是相应顶点之间的最短路径（通过*[算法简介中](https://en.wikipedia.org/wiki/Introduction_to_Algorithms)*描述的简单剪切和粘贴参数）。因此，人们可以轻松地制定以递归方式寻找最短路径的解决方案，这就是[Bellman-Ford 算法](https://en.wikipedia.org/wiki/Bellman–Ford_algorithm)或[Floyd-Warshall 算法](https://en.wikipedia.org/wiki/Floyd–Warshall_algorithm)所做的。

*重叠的*子问题意味着子问题的空间必须很小，即任何解决问题的递归算法都应该一遍又一遍地解决相同的子问题，而不是产生新的子问题。例如，考虑生成斐波那契数列的递归公式：*F* *i* = *F* *i* −1 + *F* *i* −2，基本情况*F* 1 = *F* 2 = 1。然后*F* 43 = *F* 42 + *F* 41和*F* 42 = *F* 41 + *F*40 . 现在*F* 41正在*F* 43和*F* 42的递归子树中求解。尽管子问题的总数实际上很小（只有 43 个），但如果我们采用这样的朴素递归解决方案，我们最终会一遍又一遍地解决相同的问题。动态规划考虑了这个事实，并且每个子问题只解决一次。

这可以通过以下两种方式之一实现：[*[需要引用](https://en.wikipedia.org/wiki/Wikipedia:Citation_needed)*]

- *[自上而下的方法](https://en.wikipedia.org/wiki/Top-down_and_bottom-up_design)*：这是任何问题的递归公式的直接后果。如果任何问题的解决方案都可以使用其子问题的解决方案递归地制定，并且如果其子问题重叠，那么人们可以轻松地将子问题的解决方案[记忆](https://en.wikipedia.org/wiki/Memoize)或存储在表格中。每当我们试图解决一个新的子问题时，我们首先检查表格以查看它是否已经解决。如果已经记录了一个解决方案，我们可以直接使用它，否则我们解决子问题并将其解决方案添加到表中。
- *[自下而上的方法](https://en.wikipedia.org/wiki/Top-down_and_bottom-up_design)*：一旦我们根据子问题递归地制定问题的解决方案，我们可以尝试以自下而上的方式重新制定问题：首先尝试解决子问题，然后使用它们的解决方案来构建-找到更大的子问题的解决方案。这通常也以表格形式通过使用小子问题的解决方案迭代生成越来越大的子问题的解决方案来完成。例如，如果我们已经知道*F* 41和*F* 40的值，我们可以直接计算*F* 42的值。

?>动态规划与递归、分治法同属于大问题化小问题的思想，都是解决大问题的有效解题思想。通常表现为大问题的结果依赖于小问题的结果

!>遗留任务有兴趣列出动态规划与贪心、分治、递归的区别。

## 动态规划的由来

贝尔曼在他的自传*《飓风之眼：自传》中*解释了术语*动态规划*背后的原因：

> 我在[RAND](https://en.wikipedia.org/wiki/RAND_Corporation)度过了（1950 年）秋季[学期](https://en.wikipedia.org/wiki/RAND_Corporation)。我的第一个任务是为多阶段决策过程找到一个名称。一个有趣的问题是，“动态规划这个名字从何而来？” 1950 年代不是数学研究的好年头。我们在华盛顿有一位非常有趣的绅士，名叫[威尔逊](https://en.wikipedia.org/wiki/Charles_Erwin_Wilson). 他是国防部长，其实对“研究”这个词有病态的恐惧和仇恨。我不会轻易使用这个词。我正在使用它。如果人们在他面前使用“研究”这个词，他的脸会泛滥，他会变红，而且他会变得暴力。你可以想象他对数学这个词的感受。兰德公司受雇于空军，而空军基本上以威尔逊为上司。因此，我觉得我必须做点什么来保护威尔逊和空军免受我在兰德公司内部真正做数学的事实的影响。我可以选择什么头衔，什么名字？首先，我对计划、决策和思考感兴趣。但由于种种原因，计划并不是一个好词。因此我决定使用“编程”这个词。我想了解这是动态的，这是多阶段的，这是随时间变化的。我想，让我们用一块石头杀死两只鸟。让我们举一个在经典物理意义上具有绝对精确含义的词，即动态。作为形容词，它还有一个非常有趣的特性，那就是不可能在贬义上使用动态这个词。尝试考虑一些可能会给它带来贬义的组合。不可能。因此，我认为动态规划是个好名字。这是连国会议员都无法反对的事情。所以我用它作为我活动的伞。
>
> — 理查德·贝尔曼，*《飓风之眼：自传》*（1984 年，第 159 页）

贝尔曼选择*动态*这个词来捕捉问题随时间变化的方面，因为它听起来令人印象深刻。[[11\]](https://en.wikipedia.org/wiki/Dynamic_programming#cite_note-Eddy-11)*编程*一词指的是使用该方法找到最佳*程序*，就训练或后勤的军事计划而言。这种用法与*[线性规划](https://en.wikipedia.org/wiki/Linear_programming)*和*数学规划*（[数学优化](https://en.wikipedia.org/wiki/Mathematical_optimization)的同义词）短语中的用法相同。[[17\]](https://en.wikipedia.org/wiki/Dynamic_programming#cite_note-17)

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
- 又称为状态转移方程
- 大问题拆解为对小问题的依赖，体现了状态转移的规律，描述了一种正确的穷举思路
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

这道题目的动态规划解法-迭代版实现在(如本文所示)[/newnotes/leetcode/dp动态规划#id=四要素入门例题数字三角形]
这道题目的动态规划解法-递归版实现就在(这里)[/newnotes/leetcode/数字三角形]

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

不同类型的题目，四要素状态、方程、初始化、答案皆略有不同，其中最难想到的是`状态`的定义。

做题的过程中，最为难的一点是识别出题目属于哪一种类型，只有识别出题目类型，就可以轻松找到状态的定义了。接下来就是其他三要素的定义。问题便能迎刃而解。

!>	注意以下三点：

1. 动态规划的题必须是求最优值/可行性/方案数这三种情况之一 <br>

2. 动态规划的状态依赖必须有方向性，不可以有循环依赖 坐<br>
3. 标型动态规划的状态：坐标 坐标型动态规划的方程：上一步坐标<br>

### 按场景分类

![dp相关场景](https://cdn.yangchaofan.cn/typoradp%E7%9B%B8%E5%85%B3%E5%9C%BA%E6%99%AF.svg)

最值还有其他扩展：最长、最短

求方案总数还有其他扩展：不同路径数

### 按题型分类

?>坐标、前缀、背包的出现频率是最高的三类，其他类型的题目出现频率很低。

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

!>划分的动态与坐标的动态定义不同

划分的动态定义是：前j个加起来的数值

坐标的动态定义是：走到当前i的位置，i代表的数值

### 前缀之匹配

`dp[i][j]`表示第一个字符串的前i个字符匹配上第二个字符串 的前`j`个字符的最优值/方案数/可行性

代表题:LongestCommonSubsequence,WildcardMatching 



### 区间型

`dp[i][j]`表示区间i~j的最优值/方案数/可行性 

代表题:StoneGame,BurstBalloons

### 背包型

`dp[i][j]`表示前i个物品里选出一些物品组成和为j的大小的最优 值/方案数/可行性 

代表题:Backpack系列

!>不要求挑选的数量，只要求挑选的数之和、某种运算之值

## 坐标型题目

!>做这些题目有什么目的？

1. 训练题目的审题能力，从题目中提取条件的能力，快速定位该题目是不是坐标型题目，是否属于其他类型题目
2. 训练提炼动态规划四要素的能力
3. 训练抽象描述矩阵的能力
4. 训练题目举一反三的能力：不同路径（I，II，III），骑士的最短路径(I，II，III)

### 不同的路径 II

**题目链接**：[不同的路径 II 笔记地址](newnotes/leetcode/不同的路径II.md)

**题目短述**：在矩阵中放置一些障碍后，求左上到右下的路径数 

**题目审题：**

1. 题目描述中提到“路径数”，“路径数”属于动态规划的三个场景之一的方案总数；
2. 提到每一次的走法为正下，正右，“状态之间无依赖，走法具有方向性”，属于动态规划的必要条件
3. 存在网格、棋盘、矩阵、二维数组的字样描述，属于高频的三个题型之一：坐标型题型

**题目对比**：与[不同的路径](newnotes/leetcode/不同的路径.md)一题不同之处在于，这道题目的坐标系上存在障碍，障碍对四要素的`状态`有影响，对于`方程`即向其他点移动也存在影响

**四要素分析：**

- 状态：`dp[i][j]`代表`(0，0)`走到`(i,j)`的不同路径总数

- 方程：`paths[i][j] = paths[i - 1][j] + paths[i][j - 1];`，增加了障碍点判断 

  ```java
   if (obstacleGrid[i][j] == 1) {
      continue;
   } 
  ```

  

- 初始化： `paths[i][0] = 1;  paths[0][i] = 1; `,增加了障碍点跳跃

  ```java
  if (obstacleGrid[0][i] == 1) {
      break; 
  } 
  if (obstacleGrid[i][0] == 1) {
      break;
  }  
  ```

- 答案：`dp[n-1][m-1]`

```java
public class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0 || obstacleGrid[0].length == 0) {
            return 0;
        }
        int n = obstacleGrid.length;
        int m = obstacleGrid[0].length;
        // dp[i][j]代表(0，0)走到(i,j)的不同路径总数
        int[][] paths = new int[n][m];
        // 初始化纵轴
        for (int i = 0; i < n; i++) {
            // 遇到障碍，后面的点皆为默认值0
            if (obstacleGrid[i][0] == 1) {
                break;
            } 
            paths[i][0] = 1;
        }
        // 初始化横轴
        for (int i = 0; i < m; i++) {
             // 遇到障碍，后面的点皆为默认值0
            if (obstacleGrid[0][i] == 1) {
                break; 
            }    
            paths[0][i] = 1; 
        }
        // 自顶向下
        for (int i = 1; i < n; i++) {
            for (int j = 1; j < m; j++) {
                // 遇到障碍，跳过障碍点，继续往下一个方向移动
                if (obstacleGrid[i][j] == 1) {
                    continue;
                } 
                paths[i][j] = paths[i - 1][j] + paths[i][j - 1];
            }
        }
        // 自顶向下，答案是右下角的点
        return paths[n - 1][m - 1];
    }
}
```



### 骑士的最短路径II

**题目链接：**[骑士的最短路径II 笔记地址](newnotes/leetcode/骑士的最短路径II.md)

**题目短述：**只能向右面的4个方向跳，求左上到右下最短路径 

**题目审题：**

1. 题目提到“最短”，"最短"属于动态规划的三个场景之一最小值问题
2. 题目提到骑士向4个方向跳，由这种跳法可知，不会循环依赖，因此满足动态规划的必要条件“存在方向，且无循环依赖”
3. 题目出现了棋盘、网格、矩阵、二维数组的描述，属于动态规划的高频题型之一坐标型题目

**题目对比：** [骑士的最短路线](newnotes/leetcode/骑士的最短路线.md)一题中，骑士可以走向8个点，骑士的最短路径II只可以走4个点。因为这一特性，本题既可以使用bfs，也可以使用动态规划；本题我们先用动态规划做

**坐标抽象过程：**

![骑士最短路径坐标表示02](http://cdn.yangchaofan.cn/typora/骑士最短路径坐标表示02.jpg)



!>注意：本题目骑士只有四种走法即7、3、1、5共4个方向。图示为骑士的所有走法。

**四要素分析：**

**状态：** `dp[i][j]`,从`(0，0)`至`(i，j)`的最短距离

**方程：**迭代四个方向，最小的路径赋值到当前点,`  dp[i][j] = Math.min(dp[i][j],dp[x][y] + 1);`

**初始化：**` dp[i][j] = Integer.MAX_VALUE;`

**答案：**`dp[n-1][m -1]`,终点的位置

写法1和写法2是同一个思想，只是从编码角度节省了迭代四个方向的代码。

写法1：

```java
public class Solution {
    private static int[] deltaX = {1,-1,2,-2};
    private static int[] deltxY = {-2,-2,-1,-1}; 
    /**
     * @param grid: a chessboard included 0 and 1
     * @return: the shortest path
     */
    public int shortestPath2(boolean[][] grid) {
        // write your code here
        if(grid == null || grid.length == 0){
            return -1;
        }
        int n = grid.length;
        int m = grid[0].length;

        // 状态dp[i][j]，从0，0至i，j的最短距离
        int[][] dp = new int[n][m];

        // 初始化所有点为证书最大值
        for(int i = 0; i < n;i++){
            for(int j = 0;j < m;j++){
                dp[i][j] = Integer.MAX_VALUE;
            }
        }
        dp[0][0] = 0;

        // 方程
        for(int j = 0;j < m;j ++){
            for(int i = 0;i < n;i++){
                if(grid[i][j]){
                    // 等于默认值，整数最大值
                    continue;
                }
                for(int direction = 0; direction <4;direction++){
                    int x = i + deltaX[direction];
                    int y = j + deltxY[direction];
                    if(x < 0 || x >=n || y < 0 ||y >= m){
                        // 避免整数溢出
                        continue;
                    }
                    if(dp[x][y] == Integer.MAX_VALUE){
                        // 有障碍物，跳过这一方向，避免整数溢出
                        continue;
                    }
                    // 允许数值+1，已经在上面俩个判断判断了溢出情况
                    dp[i][j] = Math.min(dp[i][j],dp[x][y] + 1);
                }
            }
        }
        if(dp[n - 1][m - 1] == Integer.MAX_VALUE){
            return -1;
        }
        return dp[n-1][m -1];
    }
}
```



写法2：

```java
public class Solution {
    /**
     * @param grid a chessboard included 0 and 1
     * @return the shortest path
     */
    public int shortestPath2(boolean[][] grid) {
        // Write your code here
        int n = grid.length;
        if (n == 0)
            return -1;
        int m = grid[0].length;
        if (m == 0)
            return -1;

        int[][] f = new int[n][m];
        for (int i = 0; i < n; ++i)
            for (int j = 0; j < m; ++j)
                f[i][j] = Integer.MAX_VALUE;

        f[0][0] = 0;
        for (int j = 1; j < m; ++j)
          for (int i = 0; i < n; ++i)
            if (!grid[i][j]) {
                if (i >= 1 && j >= 2 && f[i - 1][j - 2] != Integer.MAX_VALUE)
                    f[i][j] = Math.min(f[i][j], f[i - 1][j - 2] + 1);
                if (i + 1 < n && j >= 2 && f[i + 1][j - 2] != Integer.MAX_VALUE)
                    f[i][j] = Math.min(f[i][j], f[i + 1][j - 2] + 1);
                if (i >= 2 && j >= 1 && f[i - 2][j - 1] != Integer.MAX_VALUE)
                    f[i][j] = Math.min(f[i][j], f[i - 2][j - 1] + 1);
                if (i + 2 < n && j >= 1 && f[i + 2][j - 1] != Integer.MAX_VALUE)
                    f[i][j] = Math.min(f[i][j], f[i + 2][j - 1] + 1);
            }

        if (f[n - 1][m - 1] == Integer.MAX_VALUE)
            return -1;

        return f[n - 1][m - 1];
    }
}
```



### 跳跃游戏

https://www.lintcode.com/problem/jump-game/

https://www.jiuzhang.com/solutions/jump-game/ 

**题目链接：**[骑士的最短路径II 笔记地址](newnotes/leetcode/dp跳跃游戏.md)

**题目短述：**给一个一维数组，一开始站在下标0 数组中每个值代表可以向右跳跃的最大距离 问是否能跳到最右边的下标

**题目审题：**

1. 题干描述中提到了"一维数组"，属于动态规划高频题型的坐标型
2. 题干中提到了"向右跳"，由跳法可知不存在循环依赖、走法具有方向性，满足动态规划的两个必要条件
3. 题干中提到了“能否跳至某个位置”，属于动态规划三个场景之一的“求可行性”

**题目对比：**
动态规划做法，时间复杂度$O(n^2)$,空间复杂度$O(n)$，n为数组长度。建立的`dp[]`长度为`n`

贪心做法，时间复杂度$O(n)$,n为数组长度。一次遍历。，空间复杂度$O(1)$

**坐标抽象过程：**

输入元素为一维数组：`A = [3,2,1,0,4]`

一维数组的值代表了该位置的点最远可以跳至的距离。如图所示。数组长度为`5`，数组下标可访问的位置有`0，1，2，3，4`；

`A[0] = 2`，代表了位置为`0`的点，最远可以跳下标为`2`（0+2）的位置，对应`A[2]`;

`A[1] = 3` , 代表了位置为`1`的点，最远可以跳至下标为`4`（1+3=4）的位置，对应`A[4]`

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/9a4cf33f7896c29f8e80776492e63b9034a820e3eb0db3e6ad19ffbe857c9b27-1.jpg" width = "30%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图7-每个点的跳法如下
      </div>
</center>



<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://pic.leetcode-cn.com/cf2bbf35f5bcee5a6bd7d15c859e822497734c328932c1628d6fd4c59052d150-6.jpg" width = "30%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图8-最远到达的距离
      </div>
</center>




<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/typora/跳跃游戏dp思考过程.jpg" width = "55%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图9-坐标抽象过程
      </div>
</center>

**四要素分析：**

**状态：**`can[i]`表示能否跳到坐标i，`can[i] = true`表示可以跳转坐标`i`，反之则不能
**方程：**  如果从`0`可以跳到坐标`i`，则将dp数组的`i`位置的值置为`true`,`  can[i] = true;`

方程问题1：如何判断能否从`0`跳转到坐标`i`？

答：`can[j] && j + A[j] >= i`

方程问题2：方程中`i`和`j`是什么意思？

- `i`代表dp数组的填充位置；按顺序依次填充true或false
- `j`代表输入的A数组的访问位置，依次从A数组从0至末尾访问是否最远距离包含位置`i`，如果包含，填充dp数组的值为true，反之为false

`can[j]`表示是否可以从0跳到坐标`j`
`j`是A数组位置下标，`A[j]`的值等同于以0为起点，最远能跳到的距离
` j + A[j] >= i`是以第一个点为起点，最远能跳到的距离,包含了dp数组的位置
**初始化：**第一个点是可以到达的，所以初始化为`true`，`can[0] = true`
**答案：**最后一个点的的布尔值，代表了答案`can[A.length - 1]`

```java
public class Solution {
    public boolean canJump(int[] A) {
        boolean[] can = new boolean[A.length];
        can[0] = true;
        
        for (int i = 1; i < A.length; i++) {
            for (int j = 0; j < i; j++) {
                if (can[j] && j + A[j] >= i) {
                    can[i] = true;
                    break;
                }
            }
        }
        
        return can[A.length - 1];
    }
}
```

## 背包型题目

!>九章视频这一部分讲解的不太好，遂找了一些参考资料看
资料列表：
https://www.cnblogs.com/mfrank/p/10533701.html

通常是二维的状态数组，**前i个**组成**和为j** 

状态数组的大小需要开`(n+1)*(m+1) `

题目中通常有**“和”与“差”**的概念，数值会被放到状态中 

每个数存在**选或者不选**两种状态（01背包） 

每个数可以选任意多个（多重背包） 数的**顺序无关**

### 背包简介

### 以一个带价值背包场景举例

假设有4个物品，它的价值设为V，重量设为W，背包容量C，可以抽象为如下图表示


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

#### 矩阵化抽象思考

**首先**将这个问题**转为二维数组**

![背包的抽象思考过程](http://cdn.yangchaofan.cn/typora/背包的抽象思考过程.jpg)

表格中，每一个格子代表一个子问题。

#### 问题建模递推公式

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

#### 初始化矩阵

其次我们将数组**初始化**，初始化其0行0列的最大价值为0:



![背包的抽象思考过程-初始化](http://cdn.yangchaofan.cn/typora/背包的抽象思考过程-初始化.jpg)

#### 填充矩阵

最后我们将数组**填充满**：

**填充第一行**



![01背包抽象过程1行](http://cdn.yangchaofan.cn/typora/01背包抽象过程1行.jpg)





**填充第二行**

![背包的抽象思考过程第二行](http://cdn.yangchaofan.cn/typora/背包的抽象思考过程第二行.jpg)



**填充第三行**



![背包的抽象思考过程第三行](http://cdn.yangchaofan.cn/typora/背包的抽象思考过程第三行.jpg)

**填充第四行**：

![01背包抽象过程4行](http://cdn.yangchaofan.cn/typora/01背包抽象过程4行.jpg)

#### 价值背包的四要素

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





### 背包的由来01背包问题

https://www.lintcode.com/problem/backpack 

https://www.jiuzhang.com/solutions/backpack

[dp背包问题笔记](newnotes/leetcode/dp背包问题)

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

### 第一种表示方法：

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



### 第二种表示方法：

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



### 背包的动态解法与搜索解法的区别

使用组合型深度优先搜索，时间复杂度$O(n \times 2^n)$

而动态规划的时间复杂度是$O(n*m)$ 

动态规划是否一定更好？

答：两者在极端场景下性能有区别

### 背包的极端场景

场景1：

[1,2,4,8,16],m=31 这种情况下,0~31的所有和都可以被凑出来 

时间复杂度O(n*m)=O(n*2^n)

场景2：

[1,1000,1000000],m=1001001 m>>2^n，反而搜索更快

结论：背包容量越小，dp越快；背包容量越大，搜索越快

### 背包问题的重复子问题是什么

[1,2,3,4,5,5,6,7,8,9,10000] 

比如要凑出和为10010， 前10个数里组合出和为10的方法有5种 ，这5种组合方式就是重复子问题，因为他们凑出来的和都是10 对于后面+10000之后组成10010的影响是一样的 ，我们不关心10是怎么凑的，只关心10能不能被凑出来

### 背包进阶最小划分

https://www.lintcode.com/problem/minimum-partition 

https://www.jiuzhang.com/solutions/minimum-partition 

将数组划分为两组，两组分别求和之后的差最小 狗家真题

划分扩展，题目变换：

设整个数组所有数之和为SUM,其中一个较小的数组之和为X 问题变为求$|SUM-X-X|=|SUM-2X|$的最小值 即求使得X尽可能接近SUM/2的最大值 =数组中挑出若干数尽可能的填满一个大小为SUM/2的背包

### 背包进阶外卖优惠卷

你有一张满X元减10元的满减券和每个菜品的价格（整数），每 个菜最多只买一份。问最少买多少钱可以用得上这张外卖券？ 

背包问题：物品组合小于等于X时组合之和的最大值

原问题：物品组合大于等于X时组合之和的最小值

问题Q：挑选若干菜品价格（加入购物车）之和 大于等于 X，且若干数之和最小

问题P：挑选出不加入购物车的菜品价格之和，尽可能填满一个背包容量为SUM-X的背包，菜品之和即小于等于SUM-X时，菜品之和的最大值；

问题Q等价于问题P

### 背包进阶石头碰撞

给出N个石头及其大小数组 每次选2个石头进行碰撞，大小分别为x,y

碰撞之后会变成一个石头，大小变为|x-y|

直到石头个数<2为止 问最后剩下来的石头最小是多少？

本质上这个题的答案=最小划分那一题的答案

### 背包进阶带价值的01背包

https://www.lintcode.com/problem/backpack-ii/ 

https://www.jiuzhang.com/solutions/backpack-ii/ 

[dp背包代价值](newnotes/leetcode/dp背包求最大价值)

每个物品有体积A[i]和价值V[i] 

问能装进一个m大小的包里的最大“价值”总和是多少 Example:A=[2,3,5,6],V=[1,5,2,4],m=10

**四要素分析**

状态:`dp[i][j]`表示前i个物品挑出一些放到j的背包里的最大价值和 

方程:`dp[i][j]=max(dp[i-1][j],dp[i-1][j-A[i-1]]+V[i-1]) `

初始化:`dp[0][0..m]=0 `

答案:`dp[n][m]`

### 背包进阶多重背包

https://www.lintcode.com/problem/backpack-iii/ 

https://www.jiuzhang.com/solutions/backpack-iii/ 

相比上一个题，这个题的物品可以取任意个数 Example:A=[2,3,5,6],V=[1,5,2,4],m=10

**四要素分析**

状态:`dp[i][j]`表示前i个物品挑出一些放到`j`的背包里的最大价值和 

方程:`dp[i][j]=max(dp[i-1][j-count*A[i-1]]+count*V[i-1])` 其中0<=count<=j/A[i-1] 

初始化:`dp[0][0..m]=0 `

答案:`dp[n][m]`

```java
// 2D version, 如果你无法理解一维的solution, 可以从二维的solution入手,然后思考空间的优化
public class Solution {
    /**
     * @param A an integer array
     * @param V an integer array
     * @param m an integer
     * @return an array
     */
    public int backPackIII(int[] A, int[] V, int m) {
        // Write your code here
        int n = A.length;
        int[][] f = new int[n + 1][m + 1];
        for (int i = 1; i <= n; ++i)
            for (int j = 0; j <= m; ++j) {
                f[i][j] = f[i - 1][j];
                if (j >= A[i - 1])
                    f[i][j] = Math.max(f[i][j - A[i - 1]] + V[i - 1], f[i][j]);
            }
        return f[n][m];
    }
}
```





?> <br>
[九章算法2020](https://www.lintcode.com/ladder/161/) <br>
[九章算法动态规划专题班](https://www.jiuzhang.com/course/36/) <br>

[【labuladong】动态规划核心套路详解](https://www.bilibili.com/video/BV1XV411Y7oE?from=search&seid=15811028411645469585) <br>
[动态规划解题套路框架](https://labuladong.gitbook.io/algo/mu-lu-ye-2/mu-lu-ye/dong-tai-gui-hua-xiang-jie-jin-jie) <br>
[【动态规划专题班】ACM总冠军、清华+斯坦福大神带你入门动态规划算法](https://www.bilibili.com/video/BV1xb411e7ww?from=search&seid=15749315408323378438) <br>

[动态规划_百度百科 <br>](https://baike.baidu.com/item/动态规划/529408?fr=aladdin)

[如何理解动态规划？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/39948290) <br>

[Dynamic_programming 维基百科](https://en.wikipedia.org/wiki/Dynamic_programming#History)

[learning-algorithms-with-leetcode](https://www.yuque.com/liweiwei1419/algo/qf2bw6 )