#  **单词搜索 II** 

## 题目链接

[**单词搜索 II** ](https://www.jiuzhang.com/problem/word-search-ii/https://www.jiuzhang.com/problem/word-search-ii/)

## 题目描述

描述

给出一个由小写字母组成的矩阵和一个字典。找出所有同时在字典和矩阵中出现的单词。一个单词可以从矩阵中的任意位置开始，可以向左/右/上/下四个相邻方向移动。一个字母在一个单词中只能被使用一次。且字典中不存在重复单词

样例

**样例 1:**

```java
输入：["doaf","agai","dcan"]，["dog","dad","dgdg","can","again"]
输出：["again","can","dad","dog"]
解释：
  d o a f
  a g a i
  d c a n
矩阵中查找，返回 ["again","can","dad","dog"]。
```

**样例 2:**

```java
输入：["a"]，["b"]
输出：[]
解释：
 a
矩阵中查找，返回 []。
```

挑战

使用单词查找树来实现你的算法



## 解题思路

| <div style="width:70pt">方法</div> | 描述         | <div style="width:70pt">时间复杂度</div> | <div style="width:70pt">空间复杂度</div> |
| ---------------------------------- | ------------ | ---------------------------------------- | ---------------------------------------- |
| dfs法                              | 下文代码实现 | $O(1)$                                   | $(1)$                                    |

比较困难，还得剪枝

## 代码实现

```java
public class Solution {
    public static int[] dx = {0, 1, -1, 0};
    public static int[] dy = {1, 0, 0, -1};
    /**
     * @param board: A list of lists of character
     * @param words: A list of string
     * @return: A list of string
     */
    public List<String> wordSearchII(char[][] board, List<String> words) {
        if (board == null || board.length == 0) {
            return new ArrayList<>();
        }
        if (board[0] == null || board[0].length == 0) {
            return new ArrayList<>();
        }
        
        boolean[][] visited = new boolean[board.length][board[0].length];
        Map<String, Boolean> prefixIsWord = getPrefixSet(words);
        Set<String> wordSet = new HashSet<>();
        
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[i].length; j++) {
                visited[i][j] = true;
                dfs(board, visited, i, j, String.valueOf(board[i][j]), prefixIsWord, wordSet);
                visited[i][j] = false;
            }
        }
        
        return new ArrayList<String>(wordSet);
    }
    
    private Map<String, Boolean> getPrefixSet(List<String> words) {
        Map<String, Boolean> prefixIsWord = new HashMap<>();
        for (String word : words) {
            for (int i = 0; i < word.length() - 1; i++) {
                String prefix = word.substring(0, i + 1);
                if (!prefixIsWord.containsKey(prefix)) {
                    prefixIsWord.put(prefix, false);
                }
            }
            prefixIsWord.put(word, true);
        }
        
        return prefixIsWord;
    }
    
    private void dfs(char[][] board,
                     boolean[][] visited,
                     int x,
                     int y,
                     String word,
                     Map<String, Boolean> prefixIsWord,
                     Set<String> wordSet) {
        if (!prefixIsWord.containsKey(word)) {
            return;
        }
        
        if (prefixIsWord.get(word)) {
            wordSet.add(word);
        }
        
        for (int i = 0; i < 4; i++) {
            int adjX = x + dx[i];
            int adjY = y + dy[i];
            
            if (!inside(board, adjX, adjY) || visited[adjX][adjY]) {
                continue;
            }
            
            visited[adjX][adjY] = true;
            dfs(board, visited, adjX, adjY, word + board[adjX][adjY], prefixIsWord, wordSet);
            visited[adjX][adjY] = false;
        }
    }
    
    private boolean inside(char[][] board, int x, int y) {
        return x >= 0 && x < board.length && y >= 0 && y < board[0].length;
    }
}
```