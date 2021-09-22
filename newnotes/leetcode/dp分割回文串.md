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
            while(i>=0 && right <n && data[i] == data[right]){
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
            while(i>=0 && right <n && data[i] == data[right]){
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
        boolean[][] isPalindrome = isPalindrome(data);
        int[] dp = new int[n+1];
        dp[0] = 0;
        for(int i = 1;i<=n;i++){
            dp[i] = Integer.MAX_VALUE;
            // s[0,...i-1]
            for(int j = 0 ;j<i ;j++){
                if(isPalindrome[i][j]){
                    dp[i] = Math.min(dp[i],dp[j]+1);
                }
            }
        }
        return dp[n]-1;
    }
}
// @lc code=end


```