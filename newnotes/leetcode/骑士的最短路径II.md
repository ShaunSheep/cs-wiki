
#  骑士的最短路径II

## 题目链接

[骑士的最短路径II](https://www.lintcode.com/problem/630/?_from=collection&fromId=161)

## 题目描述
在一个 n * m 的棋盘中(二维矩阵中 0 表示空 1 表示有障碍物)，骑士的初始位置是 (0, 0) ，他想要达到 (n - 1, m - 1) 这个位置，骑士只能从左边走到右边。找出骑士到目标位置所需要走的最短路径并返回其长度，如果骑士无法达到则返回 -1.

样例
例1:

输入:
```java
[[0,0,0,0],[0,0,0,0],[0,0,0,0]]
```
输出:
```java
3
```
解释:
```java
[0,0]->[2,1]->[0,2]->[2,3]
```
例2:

输入:
```java
[[0,1,0],[0,0,1],[0,0,0]]
```
输出:
```java
-1
```
说明
如果骑士所在位置为(x,y)，那么他的下一步可以到达以下位置:
```java
(x + 1, y + 2)
(x - 1, y + 2)
(x + 2, y + 1)
(x - 2, y + 1)
```
## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  bsf | [下文代码实现-参考S同学](https://www.jiuzhang.com/problem/knight-shortest-path-ii/) | $O(1)$|$(1)$|



## 代码实现

```java

class Node {
    int x;
    int y;
    int step;
    public Node(int x, int y, int step) {
        this.x = x;
        this.y = y;
        this.step = step;
    }
}

public class Solution {
    int[] xDirections = new int[]{1, -1, 2, -2};
    int[] yDirections = new int[]{2, 2, 1, 1};
    /**
     * @param grid: a chessboard included 0 and 1
     * @return: the shortest path
     */
    public int shortestPath2(boolean[][] grid) {
        // write your code here
        Queue<Node> queue = new LinkedList<>();
        int m = grid.length, n = grid[0].length;
        
        Node start = new Node(0, 0, 0);
        queue.offer(start);
        
        while (!queue.isEmpty()) {
            Node node = queue.poll();
            
            if (node.x == m - 1 && node.y == n - 1) {
                return node.step;
            }
            
            for (int i = 0; i < 4; i++) {
                int x = node.x + xDirections[i];
                int y = node.y + yDirections[i];
                
                if (!isInbound(x, y, grid)) {
                    continue;
                }
                
                if (grid[x][y]) {
                    continue;
                }
                
                grid[x][y] = true;
                queue.offer(new Node(x, y, node.step + 1));
            }
        }
        
        return -1;
    }
    
    private boolean isInbound(int x, int y, boolean[][] grid) {
        if (x < 0 || x >= grid.length) {
            return false;
        }
        
        if (y < 0 || y >= grid[0].length) {
            return false;
        }
        
        return true;
    }
}

```