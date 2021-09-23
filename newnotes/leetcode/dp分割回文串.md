
#  分割回文串 II

## 题目链接

[分割回文串 II]()

## 题目描述

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
        for(int i = 0;i<n;i++){
            left = right = i;
            while(left>=0 && right <n && data[left] == data[right]){
                dp[left][right] = true;
                left --;
                right ++;
            }
        }

        // even
        // center line
        // a     a               b       b
        //      left    right
        for(int i = 0;i<n-1;i++){
            left = i;
            right = i+1;
            while(left>=0 && right <n && data[left] == data[right]){
                dp[left][right] = true;
                left --;
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
                    // dp[i]+1代表当前这一段也是回文串
                    dp[i] = Math.min(dp[i],dp[j]+1);
                }
            }
        }
        return dp[n]-1;
    }
}
// @lc code=end




```