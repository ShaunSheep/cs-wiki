
#  旅行商问题 · Traveling Salesman Problem

## 题目链接

[旅行商问题 · Traveling Salesman Problem](https://www.jiuzhang.com/problem/traveling-salesman-problem/)

## 题目描述

#### 描述

给 `n` 个城市(从 `1` 到 `n`)，城市和无向道路`成本`之间的关系为3元组 `[A, B, C]`（在城市 `A` 和城市 `B` 之间有一条路，成本是 `C`）我们需要从1开始找到的旅行所有城市的付出最小的成本。

一个城市只能通过一次。你可以假设你可以到达`所有`的城市。

#### 样例

**样例1**

```java
输入: 
n = 3
tuple = [[1,2,1],[2,3,2],[1,3,3]]
输出: 3
说明：最短路是1->2->3
```

**样例2**

```java
输入:
n = 1
tuple = []
输出: 0
```

## 暴力dfs法解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  暴力dfs法 | 下文代码实现  | $O(1)$|$(1)$|



## 暴力dfs法代码实现
```java
class Result {
    int minCost;
    public Result(){
        this.minCost = 1000000;
    }
}

public class Solution {
    /**
     * @param n: an integer,denote the number of cities
     * @param roads: a list of three-tuples,denote the road between cities
     * @return: return the minimum cost to travel all cities
     */
    public int minCost(int n, int[][] roads) {
        int[][] graph = constructGraph(roads, n);
        Set<Integer> visited = new HashSet<Integer>();
        Result result = new Result();
        visited.add(1);
        
        dfs(1, n, visited, 0, graph, result);
        
        return result.minCost;
    }

    void dfs (int city, 
              int n, 
              Set<Integer> visited, 
              int cost,
              int[][] graph,
              Result result) {
        
        if (visited.size() == n) {
            result.minCost = Math.min(result.minCost, cost);
            return ;
        }
        
        for(int i = 1; i < graph[city].length; i++) {
            if (visited.contains(i)) {
                continue;
            }
            visited.add(i);
            dfs(i, n, visited, cost + graph[city][i], graph, result);
            visited.remove(i);
        }
    }

    int[][] constructGraph(int[][] roads, int n) {
        int[][] graph = new int[n + 1][n + 1];
        for (int i = 0; i < n + 1; i++) {
            for (int j = 0; j < n + 1; j++) {
                graph[i][j] = 100000;
            }
        }
        int roadsLength = roads.length;
        for (int i = 0; i < roadsLength; i++) {
            int a = roads[i][0], b = roads[i][1], c = roads[i][2];
            graph[a][b] = Math.min(graph[a][b], c);
            graph[b][a] = Math.min(graph[b][a], c);
        }

        return graph;
    }
    
}
```
1. `constructGraph`函数为构建图的通用做法
   - `Math.min`的用途是取成本最小的路径
2. `dfs`的定义
   1. `city`是指当前所在城市
   2. `n`是指一共有几个城市
   3. `visited`是指我已经访问过了哪些城市
   4. `cost`是指我已经经过了这些城市，到现在我的花费是多少
   5. `graph`是指图
   6. `result`是指全局最优解的结果对象，结果值为`result.minCost`
3. `Set<Integer> visited`的作用
   1. 递归出口的条件需要用到
4. 递归的搜索树结构
   1. 第一个点dfs(1,n,visited,0,graph,result)，1代表第一个点，n代表点的总数，visited代表到达第一个点当前总访问的情况，0代表到达当前点的总花费，graph是指总的图，result是指当到达当前点的最优解
?>遗留问题
- graph的结构如何理解？
- cost如何计算，当前层与下一层的cost有怎样的关系？
-  Math.min(graph[a][b], c)是什么意思？

## 剪枝dfs法代码实现
```java
class Result {
    int minCost;
    public Result(){
        this.minCost = 1000000;
    }
}

public class Solution {
    /**
     * @param n: an integer,denote the number of cities
     * @param roads: a list of three-tuples,denote the road between cities
     * @return: return the minimum cost to travel all cities
     */
    public int minCost(int n, int[][] roads) {
        int[][] graph = constructGraph(roads, n);
        Set<Integer> visited = new HashSet<Integer>();
        List<Integer> path = new ArrayList<Integer>();
        Result result = new Result();
        path.add(1);
        visited.add(1);
        
        dfs(1, n, path, visited, 0, graph, result);
        
        return result.minCost;
    }

    void dfs (int city, 
              int n, 
              List<Integer> path, 
              Set<Integer> visited, 
              int cost,
              int[][] graph,
              Result result) {
        
        if (visited.size() == n) {
            result.minCost = Math.min(result.minCost, cost);
            return ;
        }
        
        for(int i = 1; i < graph[city].length; i++) {
            if (visited.contains(i)) {
                continue;
            }
            if (hasBetterPath(graph, path, i)) {
                continue;
            }
            visited.add(i);
            path.add(i);
            dfs(i, n, path, visited, cost + graph[city][i], graph, result);
            visited.remove(i);
            path.remove(path.size() - 1);
        }
    }

    int[][] constructGraph(int[][] roads, int n) {
        int[][] graph = new int[n + 1][n + 1];
        for (int i = 0; i < n + 1; i++) {
            for (int j = 0; j < n + 1; j++) {
                graph[i][j] = 100000;
            }
        }
        int roadsLength = roads.length;
        for (int i = 0; i < roadsLength; i++) {
            int a = roads[i][0], b = roads[i][1], c = roads[i][2];
            graph[a][b] = Math.min(graph[a][b], c);
            graph[b][a] = Math.min(graph[b][a], c);
        }

        return graph;
    }

    boolean hasBetterPath(int[][] graph, List<Integer> path, int city) {
        int pathLength = path.size();
        for (int i = 1; i < pathLength; i++){
            int path_i_1 = path.get(i - 1);
            int path_i = path.get(i);
            int path_last = path.get(pathLength - 1);
            if (graph[path_i_1][path_i] + graph[path_last][city] >
                graph[path_i_1][path_last] + graph[path_i][city]) {
                return true;
            }
        }
        
        return false;
    }
    
}

```
1. `List<Integer> path`的用途?
2. `path.remove(path.size() - 1)`的用途？
3. `hasBetterPath`的用途？
4. `hasBetterPath`的定义？
   1. `path_i_1`的用途？
   2. `path_i`的用途？
   3. if条件的判断`graph[path_i_1][path_i] + graph[path_last][city]` 的含义？老师手绘了一张图，没看懂，需要多看几遍。