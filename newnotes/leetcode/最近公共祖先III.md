
#  最近公共祖先 III

## 题目链接

[最近公共祖先 III](https://www.lintcode.com/problem/578)

## 题目描述
描述

给一棵二叉树和二叉树中的两个节点，找到这两个节点的最近公共祖先LCA。

两个节点的最近公共祖先，是指两个节点的所有父亲节点中（包括这两个节点），离这两个节点最近的公共的节点。

返回 null 如果两个节点在这棵树上不存在最近公共祖先的话

?>这两个节点未必都在这棵树上出现。
每个节点的值都不同


样例

样例1

```java
输入: 
{4, 3, 7, #, #, 5, 6}
3 5
5 6
6 7 
5 8
输出: 
4
7
7
null
```

解释:

```java
  4
 / \
3   7
   / \
  5   6

LCA(3, 5) = 4
LCA(5, 6) = 7
LCA(6, 7) = 7
LCA(5, 8) = null
```
样例2

```java
输入:
{1}
1 1
输出: 
1
说明：
这棵树只有一个值为1的节点
```
## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  LCA-分治法-递归-深度搜索 | 下文代码实现  | $O(n)$|$(1)$|

Divide & Conquer + Global Variable（不需要 ResultType）

这题和 LCA 原题的区别主要是要找的 A 和 B 可能并不存在树里。所以我们要做出这两个改变

?>
    1. 用全局变量把 A 和 B 是否找到保存起来。最后在 main function 里面要查看是否都找到<br>
    2. 当 root 等于 A 或者 B 时不能直接返回root了。原题可以直接返回是因为两个 node 是保证存在的所以这情况下 LCA 一定是 root。<br>
    3. 现在 root 等于 A 或者 B 我们还是要继续往下找是否存在另外的一个<br>

这个方法只需要增加两个全局变量并且改动 LCA 原题的代码两行即可

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
 public TreeNode lowestCommonAncestor3(TreeNode root, TreeNode A, TreeNode B) {
    TreeNode res = divConq(root, A, B);
    if (foundA && foundB)		// 程序执行完之后查看是否两个都找到
        return res;
    else
        return null;
}

private boolean foundA = false, foundB = false;
private TreeNode divConq(TreeNode root, TreeNode A, TreeNode B) {
    if (root == null)
        return root;
    
    TreeNode left = divConq(root.left, A, B);
    TreeNode right = divConq(root.right, A, B);
    
    // 如果 root 是要找的，更新全局变量
    if (root == A || root == B) {
        foundA = (root == A) || foundA;
        foundB = (root == B) || foundB;
        return root;
    }
    
    // 和 LCA 原题的思路一样
    if (left != null && right != null)
        return root;
    else if (left != null)	// 注意这种情况返回的时候是不保证两个都有找到的。可以是只找到一个或者两个都找到
        return left;		// 所以在最后上面要查看是不是两个都找到了
    else if (right != null)
        return right;
    return null;
}
}
```