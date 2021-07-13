
#  跳跃游戏

## 题目链接

[跳跃游戏](https://www.lintcode.com/problem/116/)

## 题目描述

描述

给出一个非负整数数组，你最初定位在数组的第一个位置。

数组中的每个元素代表你在那个位置可以跳跃的最大长度。

判断你是否能到达数组的最后一个位置。

数组`A`的长度不超过5000，每个元素的大小不超过5000

样例

**样例 1：**

输入：

```
A = [2,3,1,1,4]
```

输出：

```
true
```

解释：

0 -> 1 -> 4（这里的数字为下标）是一种合理的方案。

**样例 2：**

输入：

```
A = [3,2,1,0,4]
```

输出：

```
false
```

解释：

不存在任何方案能够到达终点。

挑战

这个问题有两个方法，一个是`贪心`和 `动态规划`。

`贪心`方法时间复杂度为O(N)*O*(*N*)。

`动态规划`方法的时间复杂度为为O(n^2)*O*(*n*2)。

为能够让大家使用两种方法通过测试，我们设置的是小型数据集。这仅仅是为了让大家学会如何使用动态规划的方式解决此问题。如果您用动态规划的方式完成它，你可以尝试贪心法，以使其再次通过一次。

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 动态规划法 | 下文代码实现  | $O(1)$|$(1)$|

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

## 代码实现

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

