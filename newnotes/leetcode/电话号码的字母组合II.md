
#  电话号码的字母组合II

## 题目链接

[电话号码的字母组合II](https://www.lintcode.com/problem/270/?_from=collection&fromId=161)

## 题目描述

描述

给一些不包含`0`和`1`的数字字符串和一个字典，对于每个数字字符串，每个数字代表一个字母，请返回字典中可以匹配的字母组合的数量。

如果可以用一个数字字符串可以表示出一个单词的前缀，我们认为他们之间是匹配的。

下图的手机按键图，就表示了每个数字可以代表的字母。

|       1        |     2 ABC     |     3 DEF      |
| :------------: | :-----------: | :------------: |
| **4** **GHI**  | **5** **JKL** | **6** **MNO**  |
| **7** **PQRS** | **8** **TUV** | **9** **WXYZ** |

单词中仅包含小写字母

- 单词中仅包含小写字母
- $1 \leq len(queries) \leq 10^31≤len(queries)≤103$
- $1 \leq \sum |queries_i| \leq 5 \times 10^41≤∑∣*q**u**er**i**e**s**i*∣≤5×104$
- $1 \leq len(dict) \leq 10^31≤len(dict)≤103$
- $1 \leq \sum |dict_i| \leq 5 \times 10^41≤∑∣dicti∣≤5×104$

样例

**样例 1**

```
输入: query = ["2", "3", "4"]
dict = ["a","abc","de","fg"]
输出:[2,2,0]
说明：
"a" "abc" 和 "2" 匹配
"de" "fg" 和 "3" 匹配
没有单词和 "4" 匹配
```

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  暴力法 | 下文代码实现  | $O(1)$|$(1)$|



## 代码实现

```java
class TrieNode {
    int cnt;
    TrieNode[] next;
    public TrieNode() {
        cnt = 0;
        next = new TrieNode[10];
        for (int i = 0; i < 10; i++) {
            next[i] = null;
        }
    }
};
public class Solution {
    /**
     * @param queries: the queries
     * @param dict: the words
     * @return: return the queries' answer
     */
    public int[] letterCombinationsII(String[] queries, String[] dict) {
        // write your code here
        int[] LettertoNum = {2,2,2,3,3,3,4,4,4,5,5,5,6,6,6,7,7,7,7,8,8,8,9,9,9,9};
        int[] ans = new int[queries.length];
        TrieNode root = new TrieNode();
        for (String word: dict) {
            root.cnt++;
            TrieNode cur = root;
            for (int i = 0; i < word.length(); i++) {
                int curNum = LettertoNum[word.charAt(i) - 'a'];
                if (cur.next[curNum] == null) {
                    cur.next[curNum] = new TrieNode();
                }
                cur = cur.next[curNum];
                cur.cnt++;
            }
        }
        for (int i = 0; i < queries.length; i++){
            String query = queries[i];
            boolean flag = true;
            TrieNode cur = root;
            for (int j = 0; j < query.length(); j++) {
                int curNum = query.charAt(j) - '0';
                if (cur.next[curNum] == null) {
                    flag = false;
                    break;
                }
                cur = cur.next[curNum];
            }
            ans[i] = (flag ? cur.cnt : 0);
        }
        return ans;
        
    }
}
```