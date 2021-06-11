
# 动态规划

## 实现四要素

?>更详细解释参考下面面两幅图笔记

**DP状态**
- 用$f[i]$ 或者$f[i][j]$代表某些特定条件下规模更小问题的答案
- 规模更小的问题，用参数i，j之类的来划定

**DP方程**
- 大问题拆解为对小问题的依赖
- $f[i][j]=$ 通过规模更小的一些状态,对这些状态求$max、min、sum、or$来进行推导

**DP初始化**
- 设定无法再拆解的极限小的状态下的值
- 这些值无法用方程、通项公式来表示
- 如$f[i][0]$或者$f[0][i]$

**DP答案**
- 最后的答案是什么，答案的最后一步是什么
- 如$f[i][j]$或者$f[0][0]、min(f[n][1],f[n][2]...)$


<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/BlogGifRes/20210326/wk9himKSUree.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图1-动态规划DP-自底向上
      </div>
</center>


<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="http://cdn.yangchaofan.cn/BlogGifRes/20210326/tmuGWQCAX1yG.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图2-动态规划DP-自顶向下
      </div>


</center>

# 参考
?> <br>
[九章算法2020](https://www.lintcode.com/ladder/161/) <br>
[【labuladong】动态规划核心套路详解](https://www.bilibili.com/video/BV1XV411Y7oE?from=search&seid=15811028411645469585) <br>
[动态规划解题套路框架](https://labuladong.gitbook.io/algo/dong-tai-gui-hua-xi-lie/dong-tai-gui-hua-ji-ben-ji-qiao/dong-tai-gui-hua-xiang-jie-jin-jie) <br>
[【动态规划专题班】ACM总冠军、清华+斯坦福大神带你入门动态规划算法](https://www.bilibili.com/video/BV1xb411e7ww?from=search&seid=15749315408323378438) <br>
