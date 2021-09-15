
#  数 1

## 题目链接

[数 1](https://www.lintcode.com/problem/664/)

## 题目描述

描述

给出一个 **非负** 整数 num，对所有满足 `0 ≤ i ≤ num` 条件的数字 i 均需要计算其二进制表示中数字 1 的个数并以数组的形式返回。

样例

**样例1**

```
输入： 5
输出： [0,1,1,2,1,2]
解释：
0~5的二进制表示分别是：
000
001
010
011
100
101
每个数字中1的个数为： 0,1,1,2,1,2
```

**样例2**

```
输入： 3
输出： [0,1,1,2]
```

挑战

1.时间复杂度为 O(n * sizeof(integer))的解法很容易想到, 尝试挑战线性的时间复杂度 O(n) (只遍历一遍)。
2.空间复杂度应为 O(n).
3.你能完成这项挑战吗? 不借助任何内嵌的函数, 比如C++ 中的 __builtin_popcount 亦或是任何其他语言中的方法

## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 动态规划 | 下文代码实现  | $O(N)$ |$O(N)$|



## 代码实现

```jav
public class Solution {
    /**
     * @param num: a non negative integer number
     * @return: an array represent the number of 1's in their binary
     */
    public int[] countBits(int num) {
        // write your code here
        int[] dp = new int[num+1];
        dp[0] = 0;
        for(int i = 1;i<= num;i++){
            dp[i] = (i%2) + dp[i>>1];
        }
        return dp;
    }
}
```



# Leetcode版本

## 题目链接

[位1的个数](https://leetcode-cn.com/problems/number-of-1-bits/)

## 题目描述

编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为汉明重量）。

 

提示：

请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
在 Java 中，编译器使用二进制补码记法来表示有符号整数。因此，在上面的 示例 3 中，输入表示有符号整数 -3。


示例 1：

输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
示例 2：

输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。



## 解题思路

## 代码实现

```java
 public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int rest = 0;
        for(int i = 0;i< 32;i++){
            // n % 1 = n & 00000000000000000000000000000001
            // 1 << 1 = 00000000000000000000000000000010
            // 1 << 2 = 00000000000000000000000000000100
            if((n & (1<<i))!=0){
                rest ++;  
            }
        }
        return rest;
    }
}
```



