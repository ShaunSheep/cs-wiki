
#  分割回文串 II

## 题目链接

[分割回文串 II](https://leetcode-cn.com/problems/palindrome-partitioning-ii/)

## 题目描述



给你一个字符串 s，请你将 s 分割成一些子串，使每个子串都是回文。

返回符合要求的 最少分割次数 。

 

示例 1：

输入：s = "aab"
输出：1
解释：只需一次分割就可将 s 分割成 ["aa","b"] 这样两个回文子串。
示例 2：

输入：s = "a"
输出：0
示例 3：

输入：s = "ab"
输出：1


提示：

1 <= s.length <= 2000
s 仅由小写英文字母组成



## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  dp动态规划法划分型 | 下文代码实现  | $O(N^2)$ |$O(N^2)$|

状态：dp[i] 表示 字符串S 前i 个字符S[0,..i-1]最少可以划分成几个回文串
转移方程：$dp[i] =min_{j=0,...i-1}{{(dp[j]+1|S[j...j-1]是回文串)}}$ ,`dp[j]+1`+1表示最后一段是回文串

初始化：空串可以分成0个，`dp[0] = 0;`

计算顺序：dp[i]

补充：回文串判断方法

- 奇数长度，从中间字符往两边扩展判断，找到回文串
- 偶数长度，从中间线往两边扩展判断

问题，判断偶数长度的时候，为什么i的值域位0...n-2而不是0...n-1？

答：偶数是以分割线进行划分的，n个数只有n-1条线，可访问的下标为n-2,所以要小于n-1

## 代码实现


```java
/*
 * @lc app=leetcode.cn id=132 lang=java
 *
 * [132] 分割回文串 II
 */

// @lc code=start
class Solution {
    private boolean[][] isPalindrome(char[] data){
        int n = data.length;
        boolean[][] dp = new boolean[n][n];
        // false all
        for(int i = 0;i<n;i++){
            for(int j = 0;j<n;j++){
                dp[i][j] = false;
            }
        }
        int left,right;
        // old
        // center character
        // a a      c   b       b
        //   left   i   right
        // 寻找以字符串中每一个字符为中点，向两边扩展,可访问下标是0...n-1
        for(int i = 0;i<n;i++){
            left = right = i;
            while(left>=0 && right <n && data[left] == data[right]){
                dp[left][right] = true;
                // 自减的才有下界
                left --;
                // 自增的才有上界
                right ++;
            }
        }

        // even
        // center line
        // a     a               b       b
        //      left    right
        // 偶数，寻找对称轴，一共有n个点，线只有n-1个对称轴线，可访问的下标是0..n-2
        for(int i = 0;i<n-1;i++){
            left = i;
            right = i+1;
            while(left>=0 && right <n && data[left] == data[right]){
                dp[left][right] = true;
                // 自减的才有下界
                left --;
                // 自增的才有上界
                right ++;
            }
        }
        return dp;
        
    }
    public int minCut(String s) {
        char[] data = s.toCharArray();
        int n = data.length;
        if(n == 0){
            return 0;
        }
        // 计算子串是否是回文串
        boolean[][] isPalindrome = isPalindrome(data);
        // 状态
        int[] dp = new int[n+1];
        dp[0] = 0;
        for(int i = 1;i<=n;i++){
            dp[i] = Integer.MAX_VALUE;
            // s[0,...i-1]
            for(int j = 0 ;j<i ;j++){
                // s[j...i-1],j={0,...i}
                // 判断j到i-1是否是回文串，如果是
                if(isPalindrome[j][i-1]){
                    // 判断dp[i]小还是dp[i]+1小
                    // dp[j]表示前j个字符是回文串，+1表示最后一段
                    dp[i] = Math.min(dp[i],dp[j]+1);
                }
            }
        }
        // 个数-1就是划分的次数
        return dp[n]-1;
    }
}
// @lc code=end




```