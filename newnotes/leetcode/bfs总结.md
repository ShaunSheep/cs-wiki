# 参考
 - 九章算法2020
 - vip-数据结构精讲：从原理到实战
 - vip-300分钟搞定数据结构与算法
 - 微信-数重学数据结构与算法
 - 手机-数据结构与算法面试宝典

# BFS预备知识

## 队列

!>为什么要先学会队列之后，才能学BFS?

BFS的实现基础是队列结构。

### 队列定义

队列是典型的 FIFO 数据结构。插入（insert）操作也称作入队（enqueue），新元素始终被添加在队列的末尾。 删除（delete）操作也被称为出队（dequeue)。 你只能移除第一个元素。[^2]



<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/08/14/screen-shot-2018-05-03-at-151021.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-典型队列结构
      </div>
</center>


队列通常提供以下抽象ADT接口

| 接口名  | 用途           |
| ------- | -------------- |
| enqueue | 入队           |
| dequeue | 出队           |
| offer   | 访问第一个元素 |



### 队列实现

!>队列有几种实现方式

#### 数组实现

#### 链表实现

### 队列类型
!>队列有几种类型

#### 单节点队列


```java
// "static void main" must be defined in a public class.

class MyQueue {
    // store elements
    private List<Integer> data;         
    // a pointer to indicate the start position
    private int p_start;            
    public MyQueue() {
        data = new ArrayList<Integer>();
        p_start = 0;
    }
    /** Insert an element into the queue. Return true if the operation is successful. */
    public boolean enQueue(int x) {
        data.add(x);
        return true;
    };    
    /** Delete an element from the queue. Return true if the operation is successful. */
    public boolean deQueue() {
        if (isEmpty() == true) {
            return false;
        }
        p_start++;
        return true;
    }
    /** Get the front item from the queue. */
    public int Front() {
        return data.get(p_start);
    }
    /** Checks whether the queue is empty or not. */
    public boolean isEmpty() {
        return p_start >= data.size();
    }     
};

public class Main {
    public static void main(String[] args) {
        MyQueue q = new MyQueue();
        q.enQueue(5);
        q.enQueue(3);
        if (q.isEmpty() == false) {
            System.out.println(q.Front());
        }
        q.deQueue();
        if (q.isEmpty() == false) {
            System.out.println(q.Front());
        }
        q.deQueue();
        if (q.isEmpty() == false) {
            System.out.println(q.Front());
        }
    }
}
```

观察数组实现，有两个核心点：

-  `p_start`存储不可访问节点的位置
- 每一次 `deQueue`操作，`p_start`自增+1，不可访问的元素减少一个

这种实现方式有致命的缺点：随着每一次的`deQueue`操作，数组空间越来约浪费


#### 循环队列

循环队列有个部分

- 固定大小的数组
- head指针指示起始位置和tail指针指示结束位置
- 循环队列与单指针队列的区别是，能更好的**重用之前浪费的存储空间**

两种特殊的初始态：

- 空元素

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://pic.leetcode-cn.com/Figures/circular_queue/Slide64.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-典型循环队列结构-空元素
      </div>
</center>

- 一个元素

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://pic.leetcode-cn.com/Figures/circular_queue/Slide63.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-典型循环队列结构-一个元素
      </div>
</center>

- 队列操作：出队、入队、队列已满

<center >
    <div style="float:left">
         <img style="border-radius: 0.3125em;
        box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
        src="https://pic.leetcode-cn.com/Figures/circular_queue/Slide53.png" width = "60%" alt=""/>
        <br>
        <div style="color:orange; border-bottom: 1px solid #d9d9d9;
        display: inline-block;
        color: #999;
        padding: 2px;">
          图-典型循环队列结构-队列已满
   </div>
 </center>





<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://pic.leetcode-cn.com/Figures/circular_queue/Slide52.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-典型循环队列结构-入队操作情况1-tail指向元素2，在元素2后面添加元素10
      </div>
</center>

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://pic.leetcode-cn.com/Figures/circular_queue/Slide57.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-典型循环队列结构-入队操作情况2-tail节点指向元素23在元素23后面添加元素6
      </div>
</center>

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://pic.leetcode-cn.com/Figures/circular_queue/Slide54.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-典型循环队列结构-出队操作-移除head指向的元素5
      </div>
</center>



<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://pic.leetcode-cn.com/Figures/circular_queue/Slide54.png" width = "60%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      图-典型循环队列结构-出队操作-移除head指向的元素5
      </div>
</center>






```java
class MyCircularQueue {

  private int[] queue;
  private int headIndex;
  private int count;
  private int capacity;

  /** Initialize your data structure here. Set the size of the queue to be k. */
  public MyCircularQueue(int k) {
    this.capacity = k;
    this.queue = new int[k];
    this.headIndex = 0;
    this.count = 0;
  }

  /** Insert an element into the circular queue. Return true if the operation is successful. */
  public boolean enQueue(int value) {
    if (this.count == this.capacity)
      return false;
    this.queue[(this.headIndex + this.count) % this.capacity] = value;
    this.count += 1;
    return true;
  }

  /** Delete an element from the circular queue. Return true if the operation is successful. */
  public boolean deQueue() {
    if (this.count == 0)
      return false;
    this.headIndex = (this.headIndex + 1) % this.capacity;
    this.count -= 1;
    return true;
  }

  /** Get the front item from the queue. */
  public int Front() {
    if (this.count == 0)
      return -1;
    return this.queue[this.headIndex];
  }

  /** Get the last item from the queue. */
  public int Rear() {
    if (this.count == 0)
      return -1;
    int tailIndex = (this.headIndex + this.count - 1) % this.capacity;
    return this.queue[tailIndex];
  }

  /** Checks whether the circular queue is empty or not. */
  public boolean isEmpty() {
    return (this.count == 0);
  }

  /** Checks whether the circular queue is full or not. */
  public boolean isFull() {
    return (this.count == this.capacity);
  }
}

```

循环队列的主要核心点在于：

- deQueue的实现发生了变化，每次移除元素，是通过`head=(head+1)%size`的方式，

### 队列优缺点   



## 图
!>什么是图Graph

## 如何表示图
<E,V>,E表示Edge边，V表示Vertex顶点

## 图的类型
<img src="http://cdn.yangchaofan.cn/%E5%9B%BE%E7%9A%84%E7%B1%BB%E5%9E%8B.png">


图有两种，分为无向图Undirected Graph、有向图Directed Graph。bfs操作大部分情况都在图上进行。树图Tree Graph是一种特殊的图

## 如何定义图的结构？
常用邻接矩阵、邻接表描述一个图。由于邻接矩阵耗费空间过大，工程中常用邻接表作为图的存储结构。
### 什么是邻接矩阵？
邻接矩阵Adjacency Matrix

```java
[
[1,0,0,1],
[0,1,1,0],
[0,1,1,0],
[1,0,0,1],
]
```

### 什么是邻接表？
邻接表Adjacency List
```java
[
[1],
[0,2,3],
[1],
[1],
]
```
!>邻接矩阵和邻接表的区别？
邻接表消耗空间小于邻接矩阵，邻接表更适合作为图的存储结构。

### 如何实现邻接表？
- 自定义类DirectedGraphNode
- HashMap和HashSet搭配

# 树
## 树的定义

树是用来模拟树状结构性质的数据结构

树是由节点和边构成，每个节点包括1个值、1个子节点的列表

!> 树与图的联系？树可以视为一个拥有N个节点和N-1条边的有向无环图

!>二叉树BFS与图的BFS区别？
二叉树无需使用HashSet来存储访问过的节点；因为二叉树层级分明，没有环Cricle，不可能出现一个节点的儿子的儿子是自己的情况
在图中，一个节点的邻居有可能是自己



## 树的遍历

树的遍历有四种：前序遍历、中序遍历、后序遍历、后序遍历、层序遍历

其中，层序遍历是BFS的基础



# 二叉树
## 二叉树的定义

二叉树的每个节点最多有两个子树，即一个节点的子节点个数有4种情况：空、1个左节点、1个右节点、1个左节点和1个右节点

## 四个题目入门

以一个题目开始练手，掌握三种bfs的实现方法

### 二叉树的层次遍历

题目链接

[二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
又叫二叉树的层次遍历 。

题目描述

给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）。

 

示例：

```shell
二叉树：[3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7
返回其层序遍历结果：

[
  [3],
  [9,20],
  [15,7]
]
```

### 层序遍历思路
如图有一个二叉树
<img src="http://cdn.yangchaofan.cn/bfs%E7%BB%93%E6%9E%84%E7%A4%BA%E6%84%8F%E5%9B%BE.png">
有三种实现思路

1. 单队列
2. 双队列
3. 虚拟节点

单队列思路

| 步骤   | 操作                                                | 队列结果 | 输出结果 |
| ------ | --------------------------------------------------- | -------- | -------- |
| 第一步 | offer A <br>poll A<br>offer A.left<br>offer A.right | B C      | A        |
| 第二步 | poll B<br>offer B.left<br>offer B.right<br>         | C D E    | B        |
| 第三步 | poll C<br>offer C.left<br>offer C.right<br>         | D E F G  | C        |
| 第四步 | poll D<br>offer D.left<br>offer D.right<br>         | E F G    | D        |
| 第五步 | poll E <br>offer E.left<br>offer E.right<br>        | F G H    | E        |
| 第六步 | poll F<br>offer F.left<br>offer F.right<br>         | G H I    | F        |
| 第七步 | poll G<br>offer G.left<br>offer G.right<br>         | H I      | G        |
| 第八步 | poll H<br>offer H.left<br>offer H.right<br>         | I        | H        |
| 第九步 | poll I<br>offer I.left<br>offer I.right<br>         |          | I        |

输出结果集为：`A B C D E F G H I`

### 代码实现

#### 单队列实现

```java
import java.awt.List;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.Queue;

import javax.swing.tree.TreeNode;

/*
 * @lc app=leetcode.cn id=102 lang=java
 *
 * [102] 二叉树的层序遍历
 *
 * https://leetcode-cn.com/problems/binary-tree-level-order-traversal/description/
 *
 * algorithms
 * Medium (64.23%)
 * Likes:    837
 * Dislikes: 0
 * Total Accepted:    289K
 * Total Submissions: 449.9K
 * Testcase Example:  '[3,9,20,null,null,15,7]'
 *
 * 给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）。
 * 
 * 
 * 
 * 示例：
 * 二叉树：[3,9,20,null,null,15,7],
 * 
 * 
 * ⁠   3
 * ⁠  / \
 * ⁠ 9  20
 * ⁠   /  \
 * ⁠  15   7
 * 
 * 
 * 返回其层序遍历结果：
 * 
 * 
 * [
 * ⁠ [3],
 * ⁠ [9,20],
 * ⁠ [15,7]
 * ]
 * 
 * 
 */

// @lc code=start
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        // back conditions
        if (root == null) {
            return result;
        }
        // restore all treenode
        Queue<TreeNode> queueNodes = new LinkedList<TreeNode>();
        // queue offer one treenode
        queueNodes.offer(root);
        // step2
        while (!queueNodes.isEmpty()) {
            // current level all treenodes
            ArrayList<Integer> currentLevelTreeNodes = new ArrayList<>();
            int size = queueNodes.size();
            for (int i = 0; i < size; i++) {
                TreeNode head = queueNodes.poll();
                currentLevelTreeNodes.add(head.val);
                // offer one treenode
                if (head.left != null) {
                    queueNodes.offer(head.left);
                }
                if (head.right != null) {
                    queueNodes.offer(head.right);
                }
            }
            result.add(currentLevelTreeNodes);
        }
        return result;
    }

}
// @lc code=end

```

#### 双队列实现

```java
class Solution {
    /**
     * two queue
     * 
     * @param root
     * @return
     */
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> results = new ArrayList<>();
        // back conditions
        if (root == null) {
            return results;
        }
        // initial queue ,
        List<TreeNode> firstQueueList = new ArrayList<>();
        firstQueueList.add(root);

        while (!firstQueueList.isEmpty()) {
            // temporary queue ,records the left and right 
            // child nodes of the curren node
            List<TreeNode> secondQueueList = new ArrayList<>();
            // temporay list, record the value of the the left and right 
            // child nodes of the current node
            List<Integer> levelList = toIntegerList(firstQueueList);
            results.add(levelList);

            for (TreeNode node : firstQueueList) {
                if (node.left != null) {
                    secondQueueList.add(node.left);
                }
                if (node.right != null) {
                    secondQueueList.add(node.right);
                }
            }
            firstQueueList = secondQueueList;
        }
        return results;
    }

    /**
     * 
     * @param child nodes list
     * @return records the value of the child nodes of the current node
     */
    private List<Integer> toIntegerList(List<TreeNode> listNodes) {
        List<Integer> currentlevelAllNodes = new ArrayList<>();
        for (TreeNode node : listNodes) {
            currentlevelAllNodes.add(node.val);
        }
        return currentlevelAllNodes;
    }

}

```

#### 虚拟节点实现

```java
import java.awt.List;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.Queue;
import javax.swing.tree.TreeNode;
import sun.reflect.generics.tree.Tree;
// @lc code=start
/**
 * Definition for a binary tree node. public class TreeNode { int val; TreeNode
 * left; TreeNode right; TreeNode() {} TreeNode(int val) { this.val = val; }
 * TreeNode(int val, TreeNode left, TreeNode right) { this.val = val; this.left
 * = left; this.right = right; } }
 */
class Solution {
    /**
     * dummyNode 
     * 
     * @param root
     * @return
     */
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> results = new ArrayList<>();
        if (root == null) {
            return results;
        }

        Queue<TreeNode> queueNodes = new LinkedList<>();
        queueNodes.offer(root);
        queueNodes.offer(null);

        List<Integer> currentLevelNodes = new ArrayList();
        while (!queueNodes.isEmpty()) {
            TreeNode node = queueNodes.poll();
            // back conditions
            if (node == null) {
                if (currentLevelNodes.size() == 0) {
                    break;
                }
                results.add(currentLevelNodes);
                currentLevelNodes = new ArrayList();
                queueNodes.offer(null);
                continue;
            }
            currentLevelNodes.add(node.val);
            if (node.left != null) {
                queueNodes.offer(node.left);
            }
            if (node.right != null) {
                queueNodes.offer(node.right);
            }

        }
        return results;
    }

}
// @lc code=end

```

### 朋友圈

#### 题目描述

描述

一个班中有N 个学生。他们中的一些是朋友，一些不是。他们的关系传递。例如，如果A是B的一个直接朋友，而B是C的一个直接朋友，那么A是C的一个间接朋友。我们定义朋友圈为一组直接和间接朋友。
给出一个N*N 矩阵M表示一个班中学生的关系。如果m〔i〕〔J〕＝1，那么第i个学生和第j个学生是直接朋友，否则不是。你要输出朋友圈的数量。
```shell
1.1≤N≤200。
2.对于所有学生M[i][i] = 1。
3.如果 M[i][j] = 1, 那么 M[j][i] = 1。
```
样例

样例 1:
```shell
输入：[[1,1,0],[1,1,0],[0,0,1]]
输出：2
解释：
0号和1号学生是直接朋友，所以他们位于一个朋友圈内。
2号学生自己位于一个朋友圈内。所以返回2.
```
样例 2:
```shell
输入：[[1,1,0],[1,1,1],[0,1,1]]
输出：1
解释：
0号和1号学生是直接朋友，1号和2号学生处于同一个朋友圈。
所以0号和2号是间接朋友。所有人都处于一个朋友圈内，所以返回1。
```
#### bfs代码

```java
public class Solution {
    /**
     * @param M: a matrix
     * @return: the total number of friend circles among all the students
     */
    public int findCircleNum(int[][] nums) {
        // Write your code here
        int ansbfs = beginbfs(nums);
        return ansbfs;
    }

    public int beginbfs(int[][] nums){
        int size = nums.length;
        int ans = 0;
        boolean[] visited = new boolean[size];

        for(int i = 0 ;i<size;i++){
            visited[i] = false;
        }

        // bfs every one who is not be visited
        for(int i = 0;i<size;i++){
            if(visited[i] == false){
                // none visited
                ans++;
                // set visited
                visited[i] = true;
                
                // bfs this node
                Queue<Integer> queueNodes = new LinkedList<Integer>();
                queueNodes.offer(i);
                while(queueNodes.isEmpty() == false){
                    int currentNode = queueNodes.poll();
                    for(int j = 0;j<size;j++){
                        // target nums[][]
                        if(visited[j]==false && nums[currentNode][j] ==1){
                            visited[j] = true;
                            queueNodes.offer(j);
                        }
                    }
                }


            }
        }
        return ans;
    }
}

```
### 骑士的最短路径I

### 骑士的最短路径II
### 单词接龙
## 递归实现遍历法和分治法

### 递归
### DFS
### 回溯
### 遍历法与分治法
### 二叉树的分治模板
### 题目练习
找点V找路径


## 使用非递归实现二叉树的遍历
## 性价比之王-bfs
## 99%的二叉树问题-分治法

# BFS

## 什么是BFS

宽度优先[搜索算法](https://baike.baidu.com/item/搜索算法/2988274)（又称广度优先搜索）是最简便的图的搜索算法之一，这一算法也是很多重要的图的算法的原型。Dijkstra[单源最短路径](https://baike.baidu.com/item/单源最短路径/6975204)算法和Prim[最小生成树](https://baike.baidu.com/item/最小生成树)算法都采用了和宽度优先搜索类似的思想。其别名又叫BFS，属于一种盲目搜寻法，目的是系统地展开并检查图中的所有节点，以找寻结果。换句话说，它并不考虑结果的可能位置，彻底地搜索整张图，直到找到结果为止。[^1]

> n 是点数, m 是边数 

时间复杂度：O(n + m) 

空间复杂度：O(n)

## BFS使用条件

| 关键词                                           | 使用BFS的几率 |
| ------------------------------------------------ | ------------- |
| 拓扑排序                                         | 100%          |
| 出现连通块的关键词                               | 100%          |
| 分层遍历                                         | 100%          |
| 简单图最短路径                                   | 100%          |
| 给定⼀个变换规则，从初始状态变到终⽌状态最少⼏步 | 100%          |



## BFS代码模板

```java

public ReturnType bfs(Node startNode) {
        // BFS 必须要多队列 queue，别用栈 stack！
        Queue<Node> queue = new ArrayDeque<>();
        // hashmap 有两个作用，一个是记录一个点是否被丢进过队列了，避免重复访问
        // 另外一个是记录 startNode 到其他所有节点的最短距离
        // 如果只求连通性的话，可以换成 HashSet 就行
        // node 做 key 的时候比较的是内存地址
        Map<Node, Integer> distance = new HashMap<>();
         // 把起点放进队列和哈希表里，如果有多个起点，都放进去
         queue.offer(startNode);
         distance.put(startNode, 0); // or 1 if necessary
        
         // while 队列不空，不停的从队列⾥拿出⼀个点，拓展邻居节点放到队列中
         while (!queue.isEmpty()) {
        	 Node node = queue.poll();
         	// 如果有明确的终点可以在这里加终点的判断
             if (node 是终点) {
                 break or return something;
             }
             for (Node neighbor : node.getNeighbors()) {
                 if (distance.containsKey(neighbor)) {
                    continue;
                 }
                 queue.offer(neighbor);
                 distance.put(neighbor, distance.get(node) + 1);
             }
         }
         // 如果需要返回所有点离起点的距离，就 return hashmap
         return distance;
         // 如果需要返回所有连通的节点, 就 return HashMap ⾥的所有点
         return distance.keySet();
         // 如果需要返回离终点的最短距离
         return distance.get(endNode);
}
```

## 拓扑排序BFS代码模板

```java
 List<Node> topologicalSort(List<Node> nodes) {
        // 统计所有点的入度信息，放乳hashmap 里
        Map<Node, Integer> indegrees = getIndegrees(nodes);

        // 将所有入度为 0 的点放到队列中
        Queue<Node> queue = new ArrayDeque<>();
        for (Node node : nodes) {
            if (indegrees.get(node) == 0) {
                queue.offer(node);
            }
        }

        List<Node> topoOrder = new ArrayList<>();
        while (!queue.isEmpty()) {
            Node node = queue.poll();
            topoOrder.add(node);
            for (Node neighbor : node.getNeighbors()) {
                // 入度减一
                indegrees.put(neighbor, indegrees.get(neighbor) - 1);
                // 入度减到0说明不再依赖任何点，可以被放到队列（拓扑序）里了
                if (indegrees.get(neighbor) == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // 如果 queue 是空的时候，图中还有点没有被挖出来，说明存在环
        // 有环就没有拓扑序
        if (topoOrder.size() != nodes.size()) {
            return 没有拓扑序;
        }
        return topoOrder;
    }

    Map<Node, Integer> getIndegrees(List<Node> nodes) {
        Map<Node, Integer> counter = new HashMap<>();
        for (Node node : nodes) {
            counter.put(node, 0);
        }
        40 for (Node node : nodes) {
            for (Node neighbor : node.getNeighbors()) {
                counter.put(neighbor, counter.get(neighbor) + 1);
            }
        }
        return counter;
    }

```



## 学BFS有什么用？

- 理解图的存储
- 学会三大解题场景
  - 分层遍历：逐层遍历图、树、矩阵；图、树、矩阵的最短路径
  - 连通块问题：通过图中一个点找到其他所有连通的点；‘找所有方案’的非递归实现
  - 拓扑排序：求任意拓扑排序；求是否有拓扑排序；BFS解决此类问题，复杂度低于DFS

## 分层遍历的典型例题？

## BFS可以处理的典型例题？
- 层次遍历
- 边长一致的图
- 矩阵连通块
- 找出所有方案

[^1]: [宽度优先搜索_百度百科 (baidu.com)](https://baike.baidu.com/item/宽度优先搜索/5224802?fromtitle=BFS&fromid=542084&fr=aladdin)
[^2]:[队列 & 栈 - LeetBook](https://leetcode-cn.com/leetbook/read/queue-stack/k6zxm/)