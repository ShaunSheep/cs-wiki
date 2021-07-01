
#  巴什博弈

## 题目链接

[巴什博弈](https://www.lintcode.com/problem/1300/)

## 题目描述

描述

你正在和朋友玩一个游戏：桌子上有一堆石头，每一次你们都会从中拿出1到3个石头。拿走最后一个石头的人赢得游戏。游戏开始时，你是先手。

假设两个人都绝对理性，都会做出最优决策。给定石头的数量，判断你是否会赢得比赛。

举例：有四个石头，那么你永远不会赢得游戏。不管拿几个，最后一个石头一定会被你的朋友拿走。

样例

**样例 1：**

```java
输入：n = 4 
输出：False
解析：先手取走1,2或者3，对方都会取走最后一个
```

**样例 2：**

```java
输入：n = 5 
输出：True
解析：先手拿1个，必胜
```

## 数学归纳法解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 数学归纳法 | 下文代码实现  | $O(1)$|$(1)$|

虽然记忆化搜索可以解决该问题，但当n相当大的时候，$O(n)$的空间复杂度会导致栈空间无法承受，进而爆栈。因而该方法需要进一步优化

我们可以尝试去用少的石头寻找答案的规律。

- 当剩余石头为$0$时，为必败态；

- 当剩余石头为$1 ,2, 3$时,为必胜态，因为此前存在必败态0。

- 当剩余石头为$4$时，为必败态；

- 当剩余石头为$5,6,7$时,为必胜态，因为此前存在必败态$4$。
- 此时局面可以看成又回到仅剩余$0,1,2,3$个石头情形的局面。

通过数学归纳法可以发现，在必败态、必胜态、必败态、必胜态的循环将会不断出现。换而言之，当前状态为必败态，当且仅当石头个数为4的整数倍。

所以只要判断石头的数量模4的余数即可。

## 数学归纳法代码实现

```java
public class Solution {
    /**
     * @param n: an integer
     * @return: whether you can win the game given the number of stones in the heap
     */
    public boolean canWinBash(int n) {
        // Write your code here
        return !(n%4 == 0);
    }
}
```





## 记忆化搜索思路





## 记忆化搜索代码实现

```java
public class Solution {
    /**
     * @param n: an integer
     * @return: whether you can win the game given the number of stones in the heap
     */
    public boolean canWinBash(int n) {
        return memoSearch(n, new HashMap<Integer, Boolean>());
    }
    private boolean memoSearch(int n, Map<Integer, Boolean> memo) {
        if (n <= 3) {
            return true;
        }
        if (memo.containsKey(n)) {
            return memo.get(n);
        }
        for (int i = 1; i <= 3; i++) {
            // 若后续状态有必败态，则当前为必胜态
            if (!memoSearch(n -i, memo)) {
                memo.put(n, true);
                return true;
            }
        }
        // 若后续状态无必败态，则当前为必败态
        return false;
    }
}
```
理解递归函数的定义很重要：

memoSearch返回true，表明这一层先手会赢
- memoSearc(4,...) return false表明这一层，剩余4个，先手会输
- memoSearc(1,...) return true表明这一层，剩余1个，先手会赢
- memoSearc(n,...) return ture表明这一层，剩余n个，先手会赢

`!memoSearch(n -i, memo)` true true 表明，剩余n-i个，先手会赢，上一层（剩余n个）先手会输
`!memoSearch(n -i, memo)` true false 表明，剩余n-i个，先手会输，上一层（剩余n个）先手会赢