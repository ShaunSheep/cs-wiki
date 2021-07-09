
#  二叉树的路径和 II

## 题目链接

[二叉树的路径和 II](https://www.lintcode.com/problem/246/?_from=collection&fromId=161)

## 题目描述

描述

给一棵二叉树和一个目标值，设计一个算法找到二叉树上的和为该目标值的所有路径。路径可以从任何节点出发和结束，但是需要是一条一直往下走的路线。也就是说，路径上的节点的层级是逐个递增的。

样例

**样例1：**

```
输入:
{1,2,3,4,#,2}
6
输出:
[[2, 4],[1, 3, 2]]
解释:
这棵二叉树如图所示：
    1
   / \
  2   3
 /   /
4   2
对于给定目标值6, 很显然有 2 + 4 = 6 和 1 + 3 + 2 = 6 两条路径。
```

**样例2：**

```
输入:
{1,2,3,4}
10
输出:
[]
解释:
这棵二叉树如图所示：
    1
   / \
  2   3
 /   
4   
对于给定目标值10, 没有符合条件的答案。
```

标

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 分治法 | 下文代码实现  | $O(1)$|$(1)$|



## 代码实现

```java
/**
 * Definition of TreeNode:
 * public class TreeNode {
 *     public int val;
 *     public TreeNode left, right;
 *     public TreeNode(int val) {
 *         this.val = val;
 *         this.left = this.right = null;
 *     }
 * }
 */


public class Solution {
    /**
     * @param root the root of binary tree
     * @param target an integer
     * @return all valid paths
     */
    public List<List<Integer>> binaryTreePathSum2(TreeNode root, int target) {
        // Write your code here
        List<List<Integer>> results = new ArrayList<List<Integer>>();
        ArrayList<Integer> buffer = new ArrayList<Integer>();
        if (root == null)
            return results;
        findSum(root, target, buffer, 0, results);
        return results;
    }

    public void findSum(TreeNode head, int sum, ArrayList<Integer> buffer, int level, List<List<Integer>> results) {
        if (head == null) return;
        int tmp = sum;
        buffer.add(head.val);
        for (int i = level;i >= 0; i--) {
            tmp -= buffer.get(i);
            if (tmp == 0) {
                List<Integer> temp = new ArrayList<Integer>();
                for (int j = i; j <= level; ++j)
                    temp.add(buffer.get(j));
                results.add(temp);
            }
        }
        findSum(head.left, sum, buffer, level + 1, results);
        findSum(head.right, sum, buffer, level + 1, results);
        buffer.remove(buffer.size() - 1);
    }
}
```

