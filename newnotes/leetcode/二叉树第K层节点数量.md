
#  二叉树第K层节点数量

## 题目链接

[二叉树第K层节点数量](https://www.lintcode.com/problem/939/?_from=collection&fromId=161)

## 题目描述

描述

返回给定二叉树第 `K` 层(层序号从 `1` 开始，根节点为第1层)的节点数量。

样例

**样例1**

```
输入: root = {3,9,20,#,#,15,7}, k = 2
输出: 2
解释:
  3
 / \
9  20
  /  \
 15   7
```

**样例2**

```
输入: root = {3,1,2}, k = 1
输出: 1
解释:
  3
 / \
1   2
```

标签

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  bfs法 | 下文代码实现  | $O(1)$|$(1)$|



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
     * @param root: the root node
     * @param k: an integer
     * @return: the number of nodes in the k-th layer of the binary tree
     */
    public int kthfloorNode(TreeNode root, int k) {
        // Write your code here
        if(root == null) {
            return 0;
        }
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        int level = 1;
        while(!q.isEmpty()) {
            int size = q.size();
            if(level == k) {
                return size;
            }
            for(int i = 0; i < size; i++) {
                TreeNode cur = q.poll();
                if(cur.left != null) {
                    q.offer(cur.left);
                }
                if(cur.right != null) {
                    q.offer(cur.right);
                }
            }
            level++;
        }
        return 0;
    }
}
```