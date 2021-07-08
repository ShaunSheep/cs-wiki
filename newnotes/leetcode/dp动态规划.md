
# 动态规划

## 实现四要素

?>更详细解释参考下面面两幅图笔记

**DP状态**
- 用$f[i]$ 或者$f[i][j]$代表某些特定条件下规模更小问题的答案
- 规模更小的问题，用参数i，j之类的来划定

?> 1. `f[i]`是什么？<br>
2. `f[i][j]`是什么？答：例子1：`dp[i][j]`点`(i,j)`走到最底层叶节点的最短路径<br>
3. 矩阵是如何抽象出来的？举几个具体的题目，描述一下抽象成矩阵的过程。<br>
4. 状态这一要素有什么用途？答：个人猜测是，方便描述某一层的状态


**DP方程**
- 大问题拆解为对小问题的依赖
- $f[i][j]=$ 通过规模更小的一些状态,对这些状态求$max、min、sum、or$来进行推导

?> 1. 常见的大问题有哪些？ 答：最短路径<br>
2. 常见大问题拆成小问题的例子？<br>
3. 常见推导DP方程的过程，文字描述出来。<br>

**DP初始化**
- 设定无法再拆解的极限小的状态下的值
- 这些值无法用方程、通项公式来表示
- 如$f[i][0]$或者$f[0][i]$

**DP答案**
- 最后的答案是什么，答案的最后一步是什么
- 如$f[i][j]$或者$f[0][0]、min(f[n][1],f[n][2]...)$



接下来我们以动态规划四要素为例，学习一下[数字三角形](newnotes/leetcode/数字三角形)动态规划的解法。

# 数字三角形

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

## 自底向上-数字三角形

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


?> 1. 自底向上是指什么？<br>
2. 如何理解贪心追求的是当前利益。答：我的理解是贪心只关注当前这一步的最佳值（最大、最小）
3. 如何理解DP追求的是长远利益。答：我的理解是DP关注的是所有走法的最佳值（最大、最小）
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

初始化：底部那一层的叶节点，`i = n - 2`

起始点：底部那一层的叶节点

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





## 自顶向下-数字三角形

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

答：是指两个方向，一是点可能来自于的方向，皆来自于顶部；二是指遍历的方向，遍历的方向是从根节点向下至底部的叶节点。

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




四要素分别为：

状态是`dp[i][j]`，`dp[i][j]`是指从`(0,0）`点走到`(i,j)`点的最短路径

答案是`min(dp[n-1])`,`dp[n-1]`是最底一层的叶节点，`min(dp[n-1])`是指最底一层叶节点的最小值，即`dp[n][i]`使`dp[n]`最小的值

动态方程 `dp[i][j] = Math.min(dp[i - 1][j], dp[i - 1][j - 1]) + triangle[i][j];`

初始化是两条边`dp[i][0]`、`dp[i][i]`，因为这聊天边没有左上角的店和正上方的点

起始点是`(0，0)`

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





# 参考
?> <br>
[九章算法2020](https://www.lintcode.com/ladder/161/) <br>
[【labuladong】动态规划核心套路详解](https://www.bilibili.com/video/BV1XV411Y7oE?from=search&seid=15811028411645469585) <br>
[动态规划解题套路框架](https://labuladong.gitbook.io/algo/mu-lu-ye-2/mu-lu-ye/dong-tai-gui-hua-xiang-jie-jin-jie) <br>
[【动态规划专题班】ACM总冠军、清华+斯坦福大神带你入门动态规划算法](https://www.bilibili.com/video/BV1xb411e7ww?from=search&seid=15749315408323378438) <br>