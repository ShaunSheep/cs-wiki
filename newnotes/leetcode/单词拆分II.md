
#  单词拆分II

## 题目链接

[单词拆分II](https://www.lintcode.com/problem/582/?_from=collection&fromId=161)

## 题目描述

#### 描述中文

给一字串s和单词的字典dict,在字串中增加空格来构建一个句子，并且所有单词都来自字典。
返回所有有可能的句子。

#### 样例

**样例 1:**

```
输入："lintcode"，["de","ding","co","code","lint"]
输出：["lint code", "lint co de"]
解释：
插入一个空格是"lint code"，插入两个空格是 "lint co de"
```

**样例 2:**

```
输入："a"，[]
输出：[]
解释：字典为空
```

## 记忆化解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 记忆化搜索法 | 下文代码实现  | $O(1)$|$(1)$|



## 记忆化代码实现

```java
public class Solution {
    public ArrayList<String> wordBreak(String s, Set<String> dict) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        Map<String, ArrayList<String>> memo = new HashMap<String, ArrayList<String>>();
        return wordBreakHelper(s, dict, memo);
    }

    public ArrayList<String> wordBreakHelper(String s,
                                             Set<String> dict,
                                             Map<String, ArrayList<String>> memo){
        if (memo.containsKey(s)) {
            return memo.get(s);
        }
        
        ArrayList<String> results = new ArrayList<String>();
        
        if (s.length() == 0) {
            return results;
        }
        
        if (dict.contains(s)) {
            results.add(s);
        }
        
        for (int len = 1; len < s.length(); ++len){
            String word = s.substring(0, len);
            if (!dict.contains(word)) {
                continue;
            }
            
            String suffix = s.substring(len);
            ArrayList<String> segmentations = wordBreakHelper(suffix, dict, memo);
            
            for (String segmentation: segmentations){
                results.add(word + " " + segmentation);
            }
        }
        
        memo.put(s, results);
        return results;
    }
}
```



## 记忆化搜索代码实现2

是[单词拆分#动态规划代码实现](newnotes/leetcode/单词拆分#动态规划代码实现)的进阶版本，多了一个map，map的作用是：

key：存储下标

value：存储下标开始的部分可以组成的句子列表

```java
class Solution {
    public List<String> wordBreak(String s, List<String> wordDict) {
        Map<Integer, List<List<String>>> map = new HashMap<Integer, List<List<String>>>();
        List<List<String>> wordBreaks = backtrack(s, s.length(), new HashSet<String>(wordDict), 0, map);
        List<String> breakList = new LinkedList<String>();
        for (List<String> wordBreak : wordBreaks) {
            breakList.add(String.join(" ", wordBreak));
        }
        return breakList;
    }

    public List<List<String>> backtrack(String s, int length, Set<String> wordSet, int index, Map<Integer, List<List<String>>> map) {
        if (!map.containsKey(index)) {
            List<List<String>> wordBreaks = new LinkedList<List<String>>();
            if (index == length) {
                wordBreaks.add(new LinkedList<String>());
            }
            for (int i = index + 1; i <= length; i++) {
                String word = s.substring(index, i);
                if (wordSet.contains(word)) {
                    List<List<String>> nextWordBreaks = backtrack(s, length, wordSet, i, map);
                    for (List<String> nextWordBreak : nextWordBreaks) {
                        LinkedList<String> wordBreak = new LinkedList<String>(nextWordBreak);
                        wordBreak.offerFirst(word);
                        wordBreaks.add(wordBreak);
                    }
                }
            }
            map.put(index, wordBreaks);
        }
        return map.get(index);
    }
}


```



## 记忆化搜索代码实现3

**题解链接：**[动态规划求是否有解、回溯算法求所有具体解（Java） - 单词拆分 II - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/word-break-ii/solution/dong-tai-gui-hua-hui-su-qiu-jie-ju-ti-zhi-python-d/)

![单词拆分II](http://cdn.yangchaofan.cn/typora/单词拆分II.png)

将 "catsanddog" 按照单词表拆分出单词，形成不同的句子。这个问题可以拆一下：

"c" 是不是单词，不是。
"ca" 是不是单词，不是。
"cat" 是不是单词，是，对剩余子串 "sanddog" 递归拆分。
"cats" 是不是单词，是，对剩余子串 "anddog" 递归拆分。
……以此类推。用DFS回溯，考察所有的拆分可能，如下图，指针从左往右扫描。

如果指针左侧部分是单词，则对右侧的剩余子串，递归考察。
如果指针左侧部分不是单词，不用往下递归，回溯，考察别的分支。

**难度在于如何定义递归函数：**

输入参数：一个`int`值`start`，存储了剩余子串开头的下标索引

返回参数：一个链表，存储多个字符串`["w w w","w w w",...]`

退出条件：数组越界

```java
class Solution {
  String s;
  // 存储相同的子问题
  Map<Integer, List<String>> memo = new HashMap<>();
  // 单词表的hashSet
  Set<String> dict;

  public List<String> wordBreak(String s, List<String> wordDict) {
    this.s = s;
    dict = new HashSet<>(wordDict);
    return dfs(0);
  }

  List<String> dfs(int start) {
    if (memo.containsKey(start)) 
        return memo.get(start);
    // 指针越界，剩余子串是空串，划分不出东西，返回[[]]
    if (start >= s.length()) {
      return new ArrayList<>(){{add("");}};
    }
    List<String> ret = new ArrayList<>();
    for (int i = start + 1; i <= s.length(); i++) {
      // 切出一个子串，看看是不是单词
      String sub = s.substring(start, i);
      // 如果是单词，对剩余子串继续划分
      if (dict.contains(sub)) {
        // last是剩余子串返回出的结果数组
        List<String> last = dfs(i);
        // 遍历剩余子串返回出的结果数组
        for (String l : last) {
          // 把word和每个子数组拼接，然后推入res
          ret.add(!l.equals("") ? sub + " " + l : sub);
        }
      }
    }
    memo.put(start, ret);
    return ret;
  }
}
```



