
#  标题模板

## 题目链接

[字模式 II · Word Pattern II](https://www.lintcode.com/problem/829/)

?>两个题目要求不一样
[面试题 16.18. 模式匹配](https://leetcode-cn.com/problems/pattern-matching-lcci/solution/mo-shi-pi-pei-by-leetcode-solution/)


## 题目描述

#### 描述中文

给定一个`pattern`和一个字符串`str`，查找`str`是否遵循相同的模式。
这里**遵循**的意思是一个完整的匹配，在一个字母的`模式`和一个**非空的**单词`str`之间有一个[双向连接](https://baike.baidu.com/item/双射/942799?fr=aladdin)的模式对应。(如果`a`对应`s`，那么`b`不对应`s`。例如，给定的模式= `"ab"`， str = `"ss"`，返回`false`）。

您可以假设`模式`和`str`只包含小写字母

#### 样例

**样例1**

```plain
输入:
pattern = "abab"
str = "redblueredblue"
输出: true
说明: "a"->"red","b"->"blue"
```

**样例2**

```plain
输入:
pattern = "aaaa"
str = "asdasdasdasd"
输出: true
说明: "a"->"asd"
```

**样例3**

```plain
输入:
pattern = "aabb"
str = "xyzabcxzyabc"
输出: false
```

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| dfs法 | 下文代码实现  | $O(1)$|$(1)$|

\1. 本题和字模式I不同,题干没有给出要配对的字符串,因此需要定义一个map类型dict来记录模板pattern中的字母对应配对的字符串,set类型used记录这个配对的字符串是否被枚举过。

\2. 对输入的字符串str进行深度优先搜索，传入的参数包括：模板pattern、字符串str、dict、used；

  a. 当pattern搜索到末尾且str也搜索到末尾即能完全匹配，返回true；

  b. 如果当前模板的字母已经有匹配过字符串word:

- 如果word和现应匹配的str不匹配，则返回false；（例如模板为：ABA，字符串为abc，则搜索到第三位时A已经匹配过a，但现在str中是c无法匹配；）
- 如果word和现应匹配的str匹配，则递归调用dfs并返回结果，步进为：pattern往后1位，str往后word的长度位数；

  c. 如果当前模板的字母未匹配过字符串：

- 遍历整个str，枚举字符串前缀word的作为匹配；
- 若当前的word在set中则证明其已经在b.步骤中完成，可以剪枝；
- 将word加入dict和used；
- 递归调用dfs并返回结果，步进为：pattern往后1位，str往后word长度位数；
- 将word从dict和used中删除；
- 若所有的word都无法匹配，返回false；

## 复杂度分析

- 时间复杂度：O(lengthStr^lengthPattern)
  - 每次递归有lengthStr种匹配串，一共有lengthPattern次，为指数级；
- 空间复杂度：O(lengthPattern)
  - 需要使用map和set进行存储记录；

## 代码实现

```java
public class Solution {
    /**
     * @param pattern: a string,denote pattern string
     * @param str:     a string, denote matching string
     * @return: a boolean
     */

    public boolean wordPatternMatch(String pattern, String str) {
        Map<Character, String> dict = new HashMap<>();
        Set<String> used = new HashSet<>();
        return match(pattern, str, dict, used);
    }

    private boolean match(String pattern, String str, Map<Character, String> dict, Set<String> used) {
        if (pattern.length() == 0) {
            return str.length() == 0;
        }

        Character ch = pattern.charAt(0);

        // 此前已匹配过字母ch
        if (dict.containsKey(ch)) {

            String word = dict.get(ch);

            // ch对应的word当前无法和str的前缀匹配
            if (!str.startsWith(word)) {
                return false;
            }

            // 下一步递归dfs
            return match(pattern.substring(1), str.substring(dict.get(ch).length()), dict, used);
        }

        for (int i = 0; i < str.length(); i++) {
            // 枚举匹配的字符串
            String word = str.substring(0, i + 1);
            // 字符串已经有出现过，则可以进行剪枝
            if (used.contains(word)) {
                continue;
            }
            // 下一步递归dfs
            dict.put(ch, word);
            used.add(word);

            if (match(pattern.substring(1), str.substring(i + 1), dict, used)) {
                return true;
            }
            used.remove(word);
            dict.remove(ch);
        }
        return false;
    }
}
```

