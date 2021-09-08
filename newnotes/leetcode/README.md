# 算法首页

## 后记

刷算法题的感悟,每当有新收获都会写一下

<div style="font:24px '隶书';">2021/9/10</div>

从动态规划算法身上，深深的感受到数据结构的魅力——<a style="font-family:Arial,Helvetica,sans-serif;color:red;font:24px '隶书';">这种魅力在于使用新数据结构抽象表达某种规律，并且这个结构的每一个组成部分都能够用于描述这个规律</a>。<a style="color:gray">这一句话并不恰当，需要反复雕琢。</a>
> 魅力在于dp[i],数组本身有含义，数组下标i也有多个含义，下标i不再简单的代指数组索引，而可以表示出更多含义

比如硬币兑换的题目，我们关注的是题目规律和所涉及的数据结构.<br>
已知题目规律是，为了知道面值为i的最少硬币枚数，需要先求出面值为1，面值为2...面值为i-1的最少硬币枚数<br>
已知的数据结构有`coins`数组和`amount`整形变量<br>
你需要使用一个数组`dp[i]`，这个数组有三种用途：<br>

1. 存储面值为`i`的最少硬币枚数
2. 与`dp[i-foreach(item in coins)]`与`coins`数组反复打擂台
3. `dp[amount]`返回答案
我们来看看，为了解决本题目引入的数组,这个数组每个组成部分发挥了多少威力：
首先数组由字符`dp[i]`表示，`dp`抽象表示出一列格子，`i`既能表示处于`1`至`amount`之间的面值，又能根据`i-coins[item]`表示出最优解的子问题，dp[i-coins[item]]是最优解的子问题，dp[i]与dp[i-coins[item]]打擂台可以得到每一枚硬币的当前最优解并赋值给dp[i]。
一个简单的数组，它每个组成部分怎么会有这么多的用途？看，<a style="color:red">这就是数据结构的魅力！</a>

比如跳跃游戏的题目，我们关注的是题目规律和所涉及的数据结构.<br>
已知题目规律是:最后一个是否能跳达，取决于第i个是否能跳达，第i个能否跳达，取决于从第1个石头开始，至第i-1个石头，能否有跳达至第i个石头的石头。
已知的数据结构有nums,nums的下标表示石头的位置，nums的数值表示该石头可以跳的最远距离
你需要引入一个数组dp[i],这个数组有3个用途
1. 下标i表示当前石头距离第1个石头的位置，
2. 存储第i个石头能否被跳达，dp[i]=true表示第i个石头可以跳达，反之为不可达
3. 从第1个石头至第i-1个石头，依次判断能否跳达第i个石头，判断的依据是`[position foreach(1,i-1)],position+nums[position]>=i`
## 算法三问
1. 为什么要学算法？答曰：数据抽象，面试八股，功能优化，考试拿分。
2. 去哪里学算法？答曰：算法类网站平台、大学MOOC、书籍教材
3. 如何学算法？答曰：看视频、看教材学基本概念，做题目多总结举一反三。

## 做题之前的意识
做算法题目之前，我们需要具备“评估一个算法性能高低”的能力，同时需要具备写出“大厂规范代码”的能力，最后我们需要了解大厂面试的评价标准。在[第一阶段BugFree](newnotes/leetcode/bugfree.md)一章中会对这三项基础能力做一一解读。



## 做题之前的图解网站

- [美国旧金山大学数据结构算法可视化系统](https://www.cs.usfca.edu/~galles/visualization/Algorithms.html)
- [visualgo最有名算法可视化网站](https://visualgo.net/zh/)
- [Java版数据结构与算法实现-Github上最火的实现40kstar](https://github.com/TheAlgorithms/Java/blob/master/DIRECTORY.md)
## 做题之前的课程计划

- [X] [九章算法2020班]()
- [X] [九章动态规划算法班]()
- [X] [邓俊辉算法课2021](https://www.xuetangx.com/course/THU08091000384/7755489?channel=search_result)
- [ ] [wp-算法通关40讲](https://time.geekbang.org/course/intro/100019701)
- [ ] [wp-数据结构与算法之美](https://time.geekbang.org/column/intro/100017301)
- [ ] [sd-数据结构精讲：从原理到实战](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=20#/detail/pc?id=513)
- [ ] [sd-300分钟搞定数据结构与算法](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=3#/detail/pc?id=34)
- [ ] [微信-数重学数据结构与算法](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=185&sid=20-h5Url-0&buyFrom=2&pageId=1pz4#/content)
- [ ] [手机-数据结构与算法面试宝典](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=685&sid=20-h5Url-0&buyFrom=2&pageId=1pz4#/)

## 做题的策略

1. 阅读题目，读题+思考5分钟，回忆已有知识、已有题型，记录下思考的障碍点

2. 能否做出来

   1. 能做点，就写一点，最多写5分钟
   2. 能完全做出，就全写完，加上debug+用例调试，最多写10分钟
   3. 完全没思路，记录下思考的障碍点，不会的原因，直接看题解

3. 第一次看题解，找自己容易理解的，有已有知识能看懂的

4. 看着题解抄一遍答案，记住背诵点

5. 背诵5-10分钟

6. 默写一遍，如有问题回到步骤4-5-6重复，直至完全闭卷默写出来

7. 在[Leetcode](https://leetcode-cn.com/problemset/all/)看中文题解，10-15条，评论别人的题解，记录下震惊自己的，觉得优美的题解，给予评论和笔记

8. 就可以看到看英文题解,方法是在中文题解的浏览器上，删除域名的`-cn`部分，，10-15条，同上。如两数之和

   1. 英文链接是`https://leetcode.com/problems/two-sum/?utm_source=LCUS&utm_medium=ip_redirect&utm_campaign=transfer2china`

   2. 中文链接是`https://leetcode-cn.com/problems/two-sum/?utm_source=LCUS&utm_medium=ip_redirect&utm_campaign=transfer2china`

      可以看到差别就是`-cn`

9. 在其他渠道看题解，如[九章题解](https://www.jiuzhang.com/problem)，过程同上

?> 刷题注意事项
1. 不要纠结为什么自己想不到，很多算法思想都是得奖的作品，咱们是做题找应用场景的解决方案的，不是发明解法的<br>
2. 不要读题超过10分钟，5分钟内没思路，其实就可以记录下障碍点，放弃思考了<br>
3. 代码量少的题，不要纠结为什么理解不了，多做几遍<br>
4. 每道题目至少5遍<br>
5. 每道题目至少看30个题解<br>
6. 每道题目要总结思考流程<br>

## 做题的顺序
截止2021年，Leetcode上有2000+的题目，刷完它们是不现实的，因此需要采取一定的策略刷力扣、领扣等算法平台的题目。常见的策略有二：1. 按TAG；2. 按公司。
按TAG刷题，顾名思义，是指某一具体的标签，常用于代指一类具体算法或数据结构题目，也有可能是某些书籍的名称
按公司刷题，是指某些公司常见的题库，面试八股出题大多来自于这里，当然也有可能是平台圈钱刻意列举的类型，是否真能在面试碰上，全靠读者自己甄别。S

鄙人采取的按TAG刷题，选取了较为热门的算法和数据结构，再辅佐与这两年最热门的《剑指Offer》题库，作为基础阶段，刷这些想必是够用了。上面第一阶段的BugFree已经列出，就不再重复列举，下面就S依据九章算法2020班的阶梯训练，制定了我基础阶段的刷题顺序：
- [第二阶段双指针](newnotes/leetcode/双指针.md)
- [第三阶段二分法](newnotes/leetcode/二分法.md)
- [第四阶段宽度优先搜索](newnotes/leetcode/bfs总结.md)
- [第五阶段分治法](newnotes/leetcode/分治法.md)
- [第六阶段深度优先搜索](newnotes/leetcode/DFS.md)
- [第七阶段高频数据结构](newnotes/leetcode/数据结构之高频考点.md)
- [第八阶段记忆化搜索](newnotes/leetcode/记忆化搜索.md)
- [第九阶段动态规划](newnotes/leetcode/dp动态规划.md)