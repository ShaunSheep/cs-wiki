
#  二叉树的层次遍历 II

## 题目链接

[二叉树的层次遍历 II](https://www.lintcode.com/problem/70/?_from=collection&fromId=161)

## 题目描述

描述

给出一棵二叉树，返回其节点值从底向上的层次序遍历（按从叶节点所在层到根节点所在的层遍历，然后逐层从左往右遍历）

样例

**样例 1：**

输入：

```
tree = {1,2,3}
```

输出：

```
[[2,3],[1]]
```

解释：

```
    1
   / \
  2   3
```

它将被序列化为 {1,2,3}
**样例 2：**

输入：

```
tree = {3,9,20,#,#,15,7}
```

输出：

```
[[15,7],[9,20],[3]]
```

解释：

```
    3
   / \
  9  20
    /  \
   15   7
```

它将被序列化为 {3,9,20,#,#,15,7}

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| bfs法 | 下文代码实现  | $O(1)$|$(1)$|
利用队列层序遍历整棵树，最后翻转一下即可。


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
     * @param root: A tree
     * @return: buttom-up level order a list of lists of integer
     */
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        // write your code here
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) {
            return result;
        }
        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        queue.offer(root);
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> level = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                TreeNode head = queue.poll();
                level.add(head.val);
                if (head.left != null) {
                    queue.offer(head.left);
                }
                if (head.right != null) {
                    queue.offer(head.right);
                }
            }
            result.add(level);
        }
        
        Collections.reverse(result);
        return result;
    }
}
```