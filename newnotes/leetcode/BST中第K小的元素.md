
#  BST中第K小的元素

## 题目链接

[BST中第K小的元素](https://www.lintcode.com/problem/902/)

## 题目描述

## 中序遍历法解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  中序遍历法 | 下文代码实现  | $O(n)$|$(1)$|

因为是BST，只需要做一次in order traversal，就可以从小到大遍历所有的点。记录下第k个点的值即可。

还有哈希法和栈法，时间有限可以在九章算法笔记里复习。

## 中序遍历法代码实现

```java
public class Solution {
    /**
     * @param root: the given BST
     * @param k: the given k
     * @return: the kth smallest element in BST
     */
    private int result = 0;
    private int count = 0;
     
    public int kthSmallest(TreeNode root, int k) {
        helper(root, k);
        return result;
    }
    
    private void helper(TreeNode root, int k) {
        if (root == null) {
            return;
        }
        helper(root.left, k);
        count++;
        if (count == k) {
            result = root.val;
        }
        helper(root.right, k);
    }
}
```