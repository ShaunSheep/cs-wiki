# 深度优先搜索



# 组合类DFS

排列类dfs时间复杂度$O(2^n)$---答案是否正确，存疑

# 排列类DFS

排列类dfs时间复杂度$O(N!*n)$

## 第一个问题：组合类于排列类的区别

## 第二个问题：DFS时间复杂度通用公式

`O(方案总数 * 构造每个方案的时间)`

## 排列类dfs的实现

1. 多重循环实现
2. 递归实现

?> 递归与循环实现的区别

- 递归实现搜索的本质 是实现了按照给定参数来决定循环层数 的一个多重循环 

- 递归实现的搜索=n重循环，n由输入决定

## 典型NP问题：TSP中国邮政线路问题

5种思路：

1. 暴力 DFS 
2. 暴力 DFS + 最优性剪枝(prunning) 
3. 状态压缩动态规划 
4. 随机化算法 - 使用交换调整策略
5.  随机化算法 - 使用反转调整策略 

一个题掌握四种算法：

1. 排列式搜索 Permutaition Style DFS 
2. 最优性剪枝算法 Optimal Prunning Algorithm 
3. 状态压缩动态规划 State Compression Dynamic Programming 
4. 随机化算法 Randomlization Algorithm a. 又称为遗传算法 Genetic Algorithm，模拟退火算法 Simulated Annealing

## 随机化算法

随机化一个初始方案 ，通过一个调整策略调整到局部最优值 ，在时间限制内重复上述过程直到快要超时。