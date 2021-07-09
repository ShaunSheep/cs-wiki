
#  旅行商问题 · Traveling Salesman Problem

## 题目链接

[旅行商问题 · Traveling Salesman Problem](https://www.jiuzhang.com/problem/traveling-salesman-problem/)

只学了两种暴力dfs解法：

- 暴力dfs，跑测试用例大约200ms
- 暴力dfs+剪枝，比前者速度快一倍，跑测试用例大约200ms

搜索树我还是画不出来，暂时有需要的话参考这篇[TSP（Traveling Salesman Problem）](https://www.cnblogs.com/dddyyy/p/10084673.html)

遗留了三种解法没看

- 随机优化2种
- 动态规划+状态压缩

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
   - `Math.min`的用途是取成本最小的路径，主要目的是去除重复的路线，如果a点到b点有多条路径，选取成本最小的一条
   - cost如何计算？答：`graph[a][b]`的值是指二维数组(x=a，y=b)的值，也就是a点走到b点的`cost`成本
   - 如何计算进入下一层cost的成本？答：下一层代表的是当前的成本+去到下一点的成本，即`cost + graph[city][i]`
2. `dfs`的定义
   1. `city`是指当前所在城市
   2. `n`是指一共有几个城市
   3. `visited`是指我已经访问过了哪些城市
   4. `cost`是指我已经经过了这些城市，到现在我的花费是多少
   5. `graph`是指图
   6. `result`是指全局最优解的结果对象，结果值为`result.minCost`
3. `Set<Integer> visited`的作用
   1. 递归出口的条件需要用到，判断是否递归访问完毕所有的点，因为题目是要求不重复访问所有点，如果visited的长度等于n，就说明访问完了
4. 递归的搜索树结构
   1. 第一个点dfs(1,n,visited,0,graph,result)，1代表第一个点，n代表点的总数，visited代表到达第一个点当前总访问的情况，0代表到达当前点的总花费，graph是指总的图，result是指当到达当前点的最优解
?>遗留问题
- graph的结构如何理解？
- cost如何计算，当前层与下一层的cost有怎样的关系？

  

## 剪枝dfs法的解题思路

![mark](http://cdn.yangchaofan.cn/BlogGifRes/20210626/l00UufyhfUI3.jpg?imageslim)

- better path存储的是什么？答：截止当前点，成本最低的那一条路径上的所有点
- 通过判断是否比better path上的点成本更低，来判断是否更新better path的点
- 比其成本更高，则不进入当前点下一个点的搜索，节省搜索时间

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

   1. 存储的是当前层，母亲到达当前城市成本最低的那一条 路径上的点

2. `path.remove(path.size() - 1)`的用途？

3. `hasBetterPath`的用途？

   1. `hasBetterPath==true`表明当前这一层，已经有更优秀的走法了，没必要向下递归极限找后面的点了

      例如，1,2,3,4,5共5个点

      此时已经递归到点4，到达点4有许多种走法，假设有一条边2,1,3，4最好，其他边都比这一条走法成本大，那么大于这条本成本的路线，没有必要递归到点5的走法计算中

4. `hasBetterPath`的定义？
   1. `path_i_1`的用途？
   2. `path_i`的用途？
   3. 如何理解if条件的判断条件？
      1. `graph[path_i_1][path_i] + graph[path_last][city]` 大于`graph[path_i_1][path_last] + graph[path_i][city]`z则表明已经有更好的路径了，返回true即可。
      2. 因为`i-1->i `加上`last->city`就是当前better path上的按顺序访问的点，。`i-1->last`加上`i->city`是穷举的当前这些点打乱访问顺序排列的走法即模拟的其他路径


## 状态压缩dp

状压dp dp[i][j]dp[i][j]表示到第ii个点，当前通过的点的状态为jj的最短路。

```java
class node{
	int w, x, y;
	node(int w, int x, int y){
		this.w = w;
		this.x = x;
		this.y = y;
	}
}
public class Solution {
    /**
     * @param n: an integer,denote the number of cities
     * @param roads: a list of three-tuples,denote the road between cities
     * @return: return the minimum cost to travel all cities
     */
	int[][] dp = new int[12][4096];
	int[][] mp = new int[12][12];
	Comparator<node> nodecmp = new Comparator<node>() {
        @Override
        public int compare(node o1, node o2) {
            return o1.w - o2.w;
        }
    };
	Queue<node> q = new PriorityQueue<>(nodecmp);
    public int minCost(int n, int[][] roads) {
        // Write your code here
    	for(int i = 0; i < roads.length; ++i){
            int x = roads[i][0] - 1, y = roads[i][1] - 1;
    		if(mp[x][y] == 0)mp[x][y] = mp[y][x] = roads[i][2];
    		else mp[x][y] = mp[y][x] = Math.min(mp[x][y], roads[i][2]);
        }
    	for(int i = 0; i < n; ++i) {
    		for(int j = 0; j < (1 << n); ++j) {
    			dp[i][j] = 100000000;
    		}
    	}
    	q.add(new node(0,0,1));
    	dp[0][1] = 0;
    	while(!q.isEmpty()) {
    		node tmp = q.poll();
    		int w = tmp.w, x = tmp.x, y = tmp.y;
    		if(w > dp[x][y])continue;
    		for(int i = 0; i < n; ++i)if(mp[x][i] != 0 && (y & (1 << i)) == 0) {
    			int ny = (y | (1 << i));
    			if(dp[i][ny] > dp[x][y] + mp[x][i]) {
    				dp[i][ny] = dp[x][y] + mp[x][i];
    				q.add(new node(dp[i][ny], i, ny));
    			}
    		}
    	}
    	int min = 100000000;
    	for(int i = 0; i < n; ++i) {
    		min = Math.min(min, dp[i][(1 << n) - 1]);
    	}
    	return min;
    }
}
```

