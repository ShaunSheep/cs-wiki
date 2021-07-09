
#  标题模板

## 题目链接

[K步编辑](https://www.lintcode.com/problem/623/?_from=collection&fromId=161)

## 题目描述

描述

给出一个只含有小写字母的字符串的集合以及一个目标串，输出所有可以经过不多于 `k` 次操作得到目标字符串的字符串。

你可以对字符串进行一下的3种操作:

- 加入1个字母
- 删除1个字母
- 替换1个字母

样例

**样例 1:**

```
给出字符串 `["abc","abd","abcd","adc"]`，目标字符串为 `"ac"` ，k = `1`
返回 `["abc","adc"]`
输入:
["abc", "abd", "abcd", "adc"]
"ac"
1
输出:
["abc","adc"]

解释:
"abc" 去掉 "b"
"adc" 去掉 "d"
```

**样例 2:**

```
输入:
["acc","abcd","ade","abbcd"]
"abc"
2
输出:
["acc","abcd","ade","abbcd"]

解释:
"acc" 把 "c" 变成 "b"
"abcd" 去掉 "d"
"ade" 把 "d" 变成 "b"把 "e" 变成 "c"
"abbcd" 去掉 "b" 和 "d"
```

## 记忆化解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 记忆化搜索 | 下文代码实现  | $O(1)$|$(1)$|

记忆化的哈希表在 N 次记忆化搜索的过程中是共享的，从而避免对重复前缀的重复计算。

## 记忆化搜索代码实现

```java
class Solution:
    """
    @param words: a set of stirngs
    @param target: a target string
    @param k: An integer
    @return: output all the strings that meet the requirements
    """
    def kDistance(self, words, target, k):
        memo = {}
        results = []
        for word in words:
            if self.memo_search(word, target, {}) <= k:
                results.append(word)
        return results

    def memo_search(self, word, target, memo):
        if (word, target) in memo:
            return memo[(word, target)]

        if word == target:
            return 0
        if not word:
            return len(target)
        if not target:
            return len(word)
        
        if word[0] == target[0]:
            memo[(word, target)] = self.memo_search(word[1:], target[1:], memo)
            return memo[(word, target)]

        memo[(word, target)] = min(
            self.memo_search(word[1:], target[1:], memo) + 1,
            self.memo_search(word[1:], target, memo) + 1,
            self.memo_search(word, target[1:], memo) + 1,
        )
        return memo[(word, target)]
```



## 滚动数组代码实现

```java
class TrieNode{
    public TrieNode[] sons;
    public boolean isWord;
    public String word;
    
    public TrieNode() {
        int i;
        sons = new TrieNode[26];
        for (i = 0; i < 26; ++i) {
            sons[i] = null;
        }
        
        isWord = false;
    }
    
    static public void Insert(TrieNode p, String word) {
        int i;
        char[] s = word.toCharArray();
        for (i = 0; i < s.length; ++i) {
            int c = s[i] - 'a';
            if (p.sons[c] == null) {
                p.sons[c] = new TrieNode();
            }
            
            p = p.sons[c];
        }
        
        p.isWord = true;
        p.word = word;
    }
}

public class Solution {
    /**
     * @param words: a set of stirngs
     * @param target: a target string
     * @param k: An integer
     * @return: output all the strings that meet the requirements
     */
     
    int K;
    int n;
    char[] target;
    List<String> res;
    
    // p is the current TrieNode
    // f[] representss f[Sp][...]
    void dfs(TrieNode p, int[] f) {
        int[] newf;
        int i;
        if (p.isWord && f[n] <= K) {
            res.add(p.word);
        }
        
        for (int c = 0; c < 26; ++c) {
            if (p.sons[c] == null) {
                continue;
            }
            
            // calc newf
            newf = new int[n + 1];
            // newf[...]: f[Sp + c][....]
            
            // newf[j] = Math.min(Math.min(f[j], newf[j-1]), f[j-1]) + 1;
            for (i = 0; i <= n; ++i) {
                newf[i] = f[i] + 1;
            }
            
            for (i = 1; i <= n; ++i) {
                newf[i] = Math.min(newf[i], f[i - 1] + 1);
            }   
            
            for (i = 1; i <= n; ++i) {
                if (target[i - 1] - 'a' == c) {
                    newf[i] = Math.min(newf[i], f[i - 1]);
                }
                
                newf[i] = Math.min(newf[i - 1] + 1, newf[i]);
            }
            
            dfs(p.sons[c], newf);
        }
    }
     
    public List<String> kDistance(String[] words, String targets, int k) {
        res = new ArrayList<String>();
        K = k;
        TrieNode root = new TrieNode();
        int i;
        for (i = 0; i < words.length; ++i) {
            TrieNode.Insert(root, words[i]);
        }
        
        target = targets.toCharArray();
        n = target.length;
        int[] f = new int[n + 1];
        for (i = 0; i <= n; ++i) {
            f[i] = i;
        }
        
        dfs(root, f);
        return res;
    }
}


class TrieNode {
    // Initialize your data structure here.
    public TrieNode[] children;
    public boolean hasWord;
    public String str;
    
    // Initialize your data structure here.
    public TrieNode() {
        children = new TrieNode[26];
        for (int i = 0; i < 26; ++i)
            children[i] = null;
        hasWord = false;
    }

    // Adds a word into the data structure.
    static public void addWord(TrieNode root, String word) {
        TrieNode now = root;
        for(int i = 0; i < word.length(); i++) {
            Character c = word.charAt(i);
            if (now.children[c - 'a'] == null) {
                now.children[c - 'a'] = new TrieNode();
            }
            now = now.children[c - 'a'];
        }
        now.str = word;
        now.hasWord = true;
    }
}

public class Solution {
    /**
     * @param words a set of stirngs
     * @param target a target string
     * @param k an integer
     * @return output all the strings that meet the requirements
     */
    public List<String> kDistance(String[] words, String target, int k) {
        // Write your code here
        TrieNode root = new TrieNode();
        for (int i = 0; i < words.length; i++)
            TrieNode.addWord(root, words[i]);

        List<String> result = new ArrayList<String>();

        int n = target.length();
        int[] dp = new int[n + 1];
        for (int i = 0; i <= n; ++i)
            dp[i] = i;

        find(root, result, k, target, dp);
        return result;
    }

    public void find(TrieNode node, List<String> result, int k, String target, int[] dp) {
        int n = target.length();
        // dp[i] 表示从Trie的root节点走到当前node节点，形成的Prefix
        // 和 target的前i个字符的最小编辑距离
        if (node.hasWord && dp[n] <= k) {
            result.add(node.str);
        }
        int[] next = new int[n + 1];
        for (int i = 0; i <= n; ++i)
            next[i] = 0;

        for (int i = 0; i < 26; ++i)
            if (node.children[i] != null) {
                next[0] = dp[0] + 1;
                for (int j = 1; j <= n; j++) {
                    if (target.charAt(j - 1) - 'a' == i) {
                        next[j] = Math.min(dp[j - 1], Math.min(next[j - 1] + 1, dp[j] + 1));
                    } else {
                        next[j] = Math.min(dp[j - 1] + 1, Math.min(next[j - 1] + 1, dp[j] + 1));
                    }
                }
                find(node.children[i], result, k, target, next);
            }
    }
}
```

