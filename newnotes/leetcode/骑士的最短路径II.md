
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
## bfs解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  bfs | [下文代码实现-参考S同学](https://www.jiuzhang.com/problem/knight-shortest-path-ii/) | $O(1)$|$(1)$|



## bfs代码实现

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

## dp解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  动态规划 | | $O(1)$|$(1)$|
## dp代码实现1

```java
public class Solution {
    private static int[] deltaX = {1,-1,2,-2};
    private static int[] deltxY = {-2,-2,-1,-1}; 
    /**
     * @param grid: a chessboard included 0 and 1
     * @return: the shortest path
     */
    public int shortestPath2(boolean[][] grid) {
        // write your code here
        if(grid == null || grid.length == 0){
            return -1;
        }
        int n = grid.length;
        int m = grid[0].length;

        // 状态dp[i][j]，从0，0至i，j的最短距离
        int[][] dp = new int[n][m];

        // 初始化所有点为证书最大值
        for(int i = 0; i < n;i++){
            for(int j = 0;j < m;j++){
                dp[i][j] = Integer.MAX_VALUE;
            }
        }
        dp[0][0] = 0;

        // 方程
        for(int j = 0;j < m;j ++){
            for(int i = 0;i < n;i++){
                if(grid[i][j]){
                    // 等于默认值，整数最大值
                    continue;
                }
                for(int direction = 0; direction <4;direction++){
                    int x = i + deltaX[direction];
                    int y = j + deltxY[direction];
                    if(x < 0 || x >=n || y < 0 ||y >= m){
                        // 避免整数溢出
                        continue;
                    }
                    if(dp[x][y] == Integer.MAX_VALUE){
                        // 避免整数溢出
                        continue;
                    }
                    // 允许数值+1，已经在上面俩个判断判断了溢出情况
                    dp[i][j] = Math.min(dp[i][j],dp[x][y] + 1);
                }
            }
        }
        if(dp[n - 1][m - 1] == Integer.MAX_VALUE){
            return -1;
        }
        return dp[n-1][m -1];
    }
}
```



## dp代码实现2

```java
public class Solution {
    /**
     * @param grid a chessboard included 0 and 1
     * @return the shortest path
     */
    public int shortestPath2(boolean[][] grid) {
        // Write your code here
        int n = grid.length;
        if (n == 0)
            return -1;
        int m = grid[0].length;
        if (m == 0)
            return -1;

        int[][] f = new int[n][m];
        for (int i = 0; i < n; ++i)
            for (int j = 0; j < m; ++j)
                f[i][j] = Integer.MAX_VALUE;

        f[0][0] = 0;
        for (int j = 1; j < m; ++j)
          for (int i = 0; i < n; ++i)
            if (!grid[i][j]) {
                if (i >= 1 && j >= 2 && f[i - 1][j - 2] != Integer.MAX_VALUE)
                    f[i][j] = Math.min(f[i][j], f[i - 1][j - 2] + 1);
                if (i + 1 < n && j >= 2 && f[i + 1][j - 2] != Integer.MAX_VALUE)
                    f[i][j] = Math.min(f[i][j], f[i + 1][j - 2] + 1);
                if (i >= 2 && j >= 1 && f[i - 2][j - 1] != Integer.MAX_VALUE)
                    f[i][j] = Math.min(f[i][j], f[i - 2][j - 1] + 1);
                if (i + 2 < n && j >= 1 && f[i + 2][j - 1] != Integer.MAX_VALUE)
                    f[i][j] = Math.min(f[i][j], f[i + 2][j - 1] + 1);
            }

        if (f[n - 1][m - 1] == Integer.MAX_VALUE)
            return -1;

        return f[n - 1][m - 1];
    }
}

```