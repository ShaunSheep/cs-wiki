
#  单词搜索 III

## 题目链接

[单词搜索 III](https://www.lintcode.com/problem/1848/?_from=collection&fromId=161)

## 题目描述

描述

给出一个由小写字母组成的矩阵和一个字典。找出最多同时可以在矩阵中找到的字典中出现的单词数量。一个单词可以从矩阵中的任意位置开始，可以向左/右/上/下四个相邻方向移动。一个字母在整个矩阵中只能被使用一次。且字典中不存在重复单词。

样例

**样例 1:**

```java
输入：
["doaf","agai","dcan"]，["dog","dad","dgdg","can","again"]
输出：
2
解释：
  d o a f
  a g a i
  d c a n
矩阵中查找，你可以同时找到dad和can。
```

**样例 2:**

```java
输入：
["a"]，["b"]
输出：
0
解释：
 a
矩阵中查找，返回0。
```

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  dfs法 | 下文代码实现  | $O(1)$|$(1)$|

1. 算法思路
本题和word search II整体类似，只是最后要求的东西改变了，改为要求同时在整个棋盘上可以圈出的单词数量。我们采用和word search II一致的思路。

首先使用trie去预处理dict，用于表示前缀。然后从棋盘的起始点开始dfs，每当我们找到一个单词以后，我们继续在当前棋盘继续进行dfs，直到搜不到单词为止。

2. 优化
我们可以在棋盘搜索的时候进行优化，当一个单词找到以后，下一个单词我们可以从上一个单词的起点的后面开始搜索。因此我们在dfs的过程中可以记录一下单词的起始点，这样用于下一次开始的枚举。


## 代码实现

```java
class TrieNode {    		//定义字典树的节点
    String word;
    HashMap<Character, TrieNode> children;   //使用HashMap动态开节点
    public TrieNode() {
        word = null;
        children = new HashMap<Character, TrieNode>();
    }
};


class TrieTree{
    TrieNode root;
    
    public TrieTree(TrieNode TrieNode) {
        root = TrieNode;
    }
    
    public void insert(String word) {		//字典树插入单词
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {		
            if (!node.children.containsKey(word.charAt(i))) {
                node.children.put(word.charAt(i), new TrieNode());
            }
            node = node.children.get(word.charAt(i));
        }
        node.word = word;
    }
};

public class Solution {
    /**
     * @param board: A list of lists of character
     * @param words: A list of string
     * @return: A list of string
     */
    public int[] dx = {1, 0, -1, 0};   //搜索方向
    public int[] dy = {0, 1, 0, -1};
    
    public void search(char[][] board,			//在字典树上dfs查找
                       int x,
                       int y,
                       int start_x,
                       int start_y,
                       TrieNode cur,
                       TrieNode root,
                       List<String> results,
                       int[] ans) {
        if (!cur.children.containsKey(board[x][y])) {
            return;
        }
        
        TrieNode child = cur.children.get(board[x][y]);
        char tmp = board[x][y];
        board[x][y] = 0;  // mark board[x][y] as used
        if (child.word != null) {      //如果访问到字典树叶子，将字符串压入result即可
            String tmpstr = child.word;
            results.add(tmpstr);
            child.word = null;
            ans[0] = Math.max(ans[0], results.size());
            for (int i = start_x; i < board.length; i++) {
                int startj = 0;
                if (i == start_x) {
                    startj = start_y + 1;
                }
                for (int j = startj; j < board[0].length; j++) {
                    if (board[i][j] != 0) {
                        search(board, i, j, i, j, root, root, results, ans);
                    }
                }
            }
            results.remove(results.size() - 1);
            child.word = tmpstr;
        }
        
        
        for (int i = 0; i < 4; i++) {      //向四个方向dfs搜索
            if (!isValid(board, x + dx[i], y + dy[i])) {
                continue;
            }
            search(board, x + dx[i], y + dy[i], start_x, start_y, child, root, results, ans);
        }
        
        board[x][y] = tmp;  // revert the mark
    }
    
    private boolean isValid(char[][] board, int x, int y) {     //检测搜索位置合法
        if (x < 0 || x >= board.length || y < 0 || y >= board[0].length) {
            return false;
        }
        
        return board[x][y] != 0;
    }
    
    public int wordSearchIII(char[][] board, List<String> words) {
        List<String> results = new ArrayList<String>();
        int[] ans = new int[1];
        ans[0] = 0;
        TrieTree tree = new TrieTree(new TrieNode());
        for (String word : words){
            tree.insert(word);
        }
        
        for (int i = 0; i < board.length; i++) {				//遍历字母矩阵，将每个字母作为单词首字母开始搜索
            for (int j = 0; j < board[i].length; j++) {
                search(board, i, j, i, j, tree.root, tree.root, results, ans);
            }
        }
        
        return ans[0];
    }
}

```