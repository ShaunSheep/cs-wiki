
#  硬币排成线

## 题目链接

[硬币排成线](https://www.lintcode.com/problem/394/)

## 题目描述
描述
有 n 个硬币排成一条线。两个参赛者轮流从右边依次拿走 1 或 2 个硬币，直到没有硬币为止。拿到最后一枚硬币的人获胜。

请判定 先手玩家 必胜还是必败?

若必胜, 返回 true, 否则返回 false.

样例
样例 1:

输入: 1
输出: true
样例 2:

输入: 4
输出: true
解释: 
先手玩家第一轮拿走一个硬币, 此时还剩三个.
这时无论后手玩家拿一个还是两个, 下一次先手玩家都可以把剩下的硬币拿完.
挑战
O(1) 时间复杂度且O(1) 存储。
## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  dp动态规划 | 博弈型动态规划  | ||

状态：`dp[i]`表示当前这个人面对剩下的i个石头，这个人先手必胜还是必败

**转移方程：**我的输赢取决于下一个人是否存在必败的情况，一旦存在必败的情况，我必胜。（假设每个人都会取对自己最有利的情况）。类比自底向上转移方程

`dp[i] = True` 有三种情况

- `dp[i-1]== false && dp[i-2]== false`
- `dp[i-1]== false && dp[i-2] == true`
- `dp[i-1] == true && dp[i-2] == false`

**知识点辅助理解：**

1. 如果我先手取1个或2个石子后，能让下一个人，出现先手必败的情况，则我先手必胜。
2. 如果下一个人，无论怎么取，先手都必胜，则我先手必败
3. 每个人都会取对自己最有利的情况

## 代码实现

```java
public class Solution {
    /**
     * @param n: An integer
     * @return: A boolean which equals to true if the first player will win
     */
    public boolean firstWillWin(int n) {
        // write your code here
        if(n == 0){
            return false;
        }

        if(n == 1 || n == 2){
            return true;
        }
        boolean[] dp = new boolean[n+1];
        dp[0] = false;
        dp[1] = true;
        dp[2] = true;
        for(int i = 3;i<=n;i++){
            dp[i] = (!dp[i-1]) || (!dp[i-2]);
        }
        return dp[n];
    }
}

```