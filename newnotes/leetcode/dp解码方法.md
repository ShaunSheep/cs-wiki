# 解码方法

## 题目链接

#### [解码方法](https://leetcode-cn.com/problems/decode-ways/)

## 题目描述

一条包含字母 A-Z 的消息通过以下映射进行了 编码 ：

'A' -> 1
'B' -> 2
...
'Z' -> 26
要 解码 已编码的消息，所有数字必须基于上述映射的方法，反向映射回字母（可能有多种方法）。例如，"11106" 可以映射为：

"AAJF" ，将消息分组为 (1 1 10 6)
"KJF" ，将消息分组为 (11 10 6)
注意，消息不能分组为  (1 11 06) ，因为 "06" 不能映射为 "F" ，这是由于 "6" 和 "06" 在映射中并不等价。

给你一个只含数字的 非空 字符串 s ，请计算并返回 解码 方法的 总数 。

题目数据保证答案肯定是一个 32 位 的整数。

 

示例 1：

输入：s = "12"
输出：2
解释：它可以解码为 "AB"（1 2）或者 "L"（12）。
示例 2：

输入：s = "226"
输出：3
解释：它可以解码为 "BZ" (2 26), "VF" (22 6), 或者 "BBF" (2 2 6) 。
示例 3：

输入：s = "0"
输出：0
解释：没有字符映射到以 0 开头的数字。
含有 0 的有效映射是 'J' -> "10" 和 'T'-> "20" 。
由于没有字符，因此没有有效的方法对此进行解码，因为所有数字都需要映射。
示例 4：

输入：s = "06"
输出：0
解释："06" 不能映射到 "F" ，因为字符串含有前导 0（"6" 和 "06" 在映射中并不等价）。


提示：

1 <= s.length <= 100
s 只包含数字，并且可能包含前导零。



## 解题思路

| 方法 | 描述           | 时间复杂度 | 空间复杂度 |
| :--- | :------------- | :--------- | :--------- |
| dp法 | 划分型动态规划 | O(n)       | O(n)       |

题目审题：出现”划分““总数”，可以尝试用划分动态规划

状态定义：`dp[i]`表示长度位`i`的字符串，有多少种解码方式

转移方程：`dp[i]=dp[i-1]|表示的含义是取a[i-1]共1个数字解析成字母+dp[i-2]|表示的含义是取a[i-1]a[i-2]共两位数字解析成字母`

初始化：`dp[0]=1空串有1种解码方式`,`dp[i]=0初始化位0`

答案：`dp[n]`

此题的难度在于如何证明转移方程式正确的，即`dp[i]`为什么可以由`dp[i-1]+dp[i-2]`表示出来？

此题的难度2在于dp[i-2]的计算方式，需要用到字符数组与'0' '1' '10' '26'的差值计算；需要用到字符串S后两位$S_{i-1},S_{i-2}$,并且一个表示个位数，一个表示十位数；需要判断十位数的有效性。十位数的有效性是根据 `26>=v>=10`来得出结论的。

## 代码实现

```java
/*
 * @lc app=leetcode.cn id=91 lang=java
 *
 * [91] 解码方法
 */

// @lc code=start
class Solution {
    public int numDecodings(String s) {
        if(!(s instanceof String)){
            return 0;
        }
        char[] data = s.toCharArray();
        int n = data.length;
        if(n == 0){
            return 1;
        }
        int[] dp = new int[n+1];
        // 空串等于1
        dp[0] = 1;
        for(int i = 1;i <= n;i++){
            dp[i] = 0;
            // a[i]一位拼凑的个位数
            if(data[i-1] >= '1' && data[i-1] <= '9'){
               // 划分一位凑数
                dp[i] +=  dp[i-1];
            }

            // a[i-2]a[i-1]拼凑一个两位数
            if(i>1){
                // 必须满足i>1,保证10位数有值
                int j = (data[i-2]-'0')*10 +(data[i-1]-'0');
                if(j >=  10 && j <=  26){
                    // 划分两位凑数
                    dp[i] += dp[i-2];
                }
            }
          
        }
        return dp[n];
    }
}
// @lc code=end


```

第二遍，仍然出错了

- 字符串转数组，结果写错了，写成了`int[] `，应该是`char[]`
- 忘记判断`i>1`
- 判断1位数的边界，写错了，写成了`i>0 && i< 9` ，正确写法为`i>=1 && i<=9 `
- j的判断条件写错了，写成了j>='10'，正确写法是j>=10



```java
/*
 * @lc app=leetcode.cn id=91 lang=java
 *
 * [91] 解码方法
 */

// @lc code=start
class Solution {
    public int numDecodings(String s) {
        if(!(s instanceof String)){
            return 0;
        }

        char[] data = s.toCharArray();
        int n = data.length ;
        if(n == 0){
            return 1;
        }
        int[] dp = new int[n+1];
        // 空串的解码方式有1种
        dp[0] = 1;
        for(int i = 1; i <= n; i++){
            print(dp[0]+"");
            print(dp[1]+"");

            // 刚开始解码，只有0种
            dp[i] = 0;
            // 1位数解法
            if(data[i-1] >= '1' && data[i-1] <= '9'){
                // 是否可以与‘0’ ‘9’ 相等
                dp[i] += dp[i-1];
            }
            // 2位数解法
            if(i>1){
                
                // 务必判断位数大于1
                int j = 10 * (data[i-2]-'0') + (data[i-1]-'0');
                if(j >= 10 && j <= 26){
                    dp[i] += dp[i-2];
                }
            }
        }
        return dp[n];
    }
    private void print(String s){
        System.out.println(s);
    }
}
// @lc code=end

```

