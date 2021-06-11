

#  实现strStr()

又称 字符串查找 II 、strStrII

## 题目链接

[实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)

## 题目描述

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1:
```shell
输入: haystack = "hello", needle = "ll"
输出: 2
```
示例 2:
```shell
输入: haystack = "aaaaa", needle = "bba"
输出: -1
```
?>
说明:<br>
当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。<br>
对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。



## 解题思路

| 方法  |描述 |时间复杂度 |空间复杂度|
|---|---|---|---|
|  哈希算法 | 下文代码实现  | $O(M+N)$|$(1)$|
|  第一步 | 给定一个字符串S,对于一个字符c我们规定$id(c)=c-'a'+1$ | $O(M+N)$|$(1)$|
|  第二步 | 哈希编码字符串target和source$hash[i]=(hash[i-1]*p+id(s[i]))%MOD$ <br> p和MOD均为质数，并且要尽量大 | $O(M+N)$|$(1)$|
|  第二步 | 遍历source的hash值过程中，每次都计算targetLen个位数的hash值 :<br>假设 target长度为2，source"abcd",<br>$hash("cd") = (hash("bc + d") - hash("b")*2 ) % BASE$| $O(M+N)$|$(1)$|


## 代码实现

```java
// @lc code=start
class Solution {
    public static final Integer BASE = 100007;

    public int strStr(String sourString, String targeString) {
        // back conditions
        if (sourString == null || targeString == null) {
            return -1;
        }
        // back conditions
        // fix bug targeString == "" is error
        if (targeString.equals("")) {
            return 0;
        }

        // work code
        // circle times
        int targetLength = targeString.length();
        int p = 1;
        int targetCode = 0;
        int sourceCode = 0;
        for (int i = 0; i < targetLength; i++) {
            // hash code power
            p = (p * 31) % BASE;
        }

        // hash code target
        for (int i = 0; i < targetLength; i++) {
            // todo why need charAt(i)?
            targetCode = (targetCode * 31 + targeString.charAt(i)) % BASE;
        }

        for (int i = 0; i < sourString.length(); i++) {
            // first hash code
            sourceCode = (sourceCode * 31 + sourString.charAt(i)) % BASE;

            // splits the hash value of the target's length
            if (i < targetLength - 1) {
                continue;
            }

            // when splits finsh
            if (i >= targetLength) {
                sourceCode = (sourceCode - p * sourString.charAt(i - targetLength)) % BASE;
            }

            // todo why < 0
            if (sourceCode < 0) {
                sourceCode += BASE;
            }
            // todo why ==
            if (sourceCode == targetCode) {
                return i - targetLength + 1;
            }
        }

        // back conditions
        return -1;

    }
}
// @lc code=end
```