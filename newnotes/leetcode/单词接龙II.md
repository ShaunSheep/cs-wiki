
#  单词接龙 II · Word Ladder II

## 题目链接

[单词接龙 II](https://www.lintcode.com/problem/121/)

## 题目描述

描述

给出两个单词（`start`和`end`）和一个字典，找出所有从`start`到`end`的最短转换序列。

变换规则如下：

1. 每次只能改变一个字母。
2. 变换过程中的中间单词必须在字典中出现。

所有单词具有相同的长度。

所有单词都只包含小写字母。

题目确保存在合法的路径。

单词数量小于等于10000单词长度小于等于10

样例

**样例 1：**

输入：

```java
start = "a"
end = "c"
dict =["a","b","c"]
```

输出：

```java
[["a","c"]]
```

解释：

"a"->"c"

**样例 2：**

输入：

```java
start ="hit"
end = "cog"
dict =["hot","dot","dog","lot","log"]
```

输出：

```java
[["hit","hot","dot","dog","cog"],["hit","hot","lot","log","cog"]]
```

解释：

1."hit"->"hot"->"dot"->"dog"->"cog"
2."hit"->"hot"->"lot"->"log"->"cog"

标签

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  暴力法 | 下文代码实现  | $O(1)$|$(1)$|



## 代码实现

```java
public class Solution {
    public List<List<String>> findLadders(String start, String end,
            Set<String> dict) {
        List<List<String>> ladders = new ArrayList<List<String>>();
        Map<String, List<String>> map = new HashMap<String, List<String>>();
        Map<String, Integer> distance = new HashMap<String, Integer>();
        dict.add(start);
        dict.add(end);
        //bfs搜start--end的最短路径
        bfs(map, distance, end, start, dict);
        List<String> path = new ArrayList<String>();
        //dfs输出距离最短的路径
        dfs(ladders, path, start, end, distance, map);
        return ladders;
    }

    void dfs(List<List<String>> ladders, List<String> path, String crt,
            String end, Map<String, Integer> distance,
            Map<String, List<String>> map) {
        path.add(crt);
        if (crt.equals(end)) {
            ladders.add(new ArrayList<String>(path));
        } else {
            for (String next : map.get(crt)) {
                if (distance.containsKey(next) && distance.get(crt) == distance.get(next) + 1) { 
                    dfs(ladders, path, next, end, distance, map);
                }
            }           
        }
        path.remove(path.size() - 1);
    }

    void bfs(Map<String, List<String>> map, Map<String, Integer> distance,
            String start, String end, Set<String> dict) {
        Queue<String> q = new LinkedList<String>();
        q.offer(start);
        distance.put(start, 0);
        for (String s : dict) {
            map.put(s, new ArrayList<String>());
        }
        
        while (!q.isEmpty()) {
            String crt = q.poll();

            List<String> nextList = expand(crt, dict);
            for (String next : nextList) {
                map.get(next).add(crt);
                if (!distance.containsKey(next)) {
                    distance.put(next, distance.get(crt) + 1);
                    q.offer(next);
                }
            }
        }
    }
	//暴力匹配当前字符串，修改一个字母后的新字符串存在于dict中
    List<String> expand(String crt, Set<String> dict) {
        List<String> expansion = new ArrayList<String>();

        for (int i = 0; i < crt.length(); i++) {
            for (char ch = 'a'; ch <= 'z'; ch++) {
                if (ch != crt.charAt(i)) {
                    String expanded = crt.substring(0, i) + ch
                            + crt.substring(i + 1);
                    if (dict.contains(expanded)) {
                        expansion.add(expanded);
                    }
                }
            }
        }

        return expansion;
    }
}
```

