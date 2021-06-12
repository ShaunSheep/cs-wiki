
#  最近公共祖先 II

## 题目链接

[最近公共祖先 II](https://www.lintcode.com/problem/474)

> 特点是每个节点多了一个属性parent 

## 题目描述
描述

给一棵二叉树和二叉树中的两个节点，找到这两个节点的最近公共祖先LCA。

两个节点的最近公共祖先，是指两个节点的所有父亲节点中（包括这两个节点），离这两个节点最近的公共的节点。

每个节点除了左右儿子指针以外，还包含一个父亲指针parent，指向自己的父亲。

样例

样例 1:
```java
输入：{4,3,7,#,#,5,6},3,5
输出：4
解释：
     4
     / \
    3   7
       / \
      5   6
LCA(3, 5) = 4
```

样例 2:

```java
输入：{4,3,7,#,#,5,6},5,6
输出：7
解释：
      4
     / \
    3   7
       / \
      5   6
LCA(5, 6) = 7
```

## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  哈希表法+分治法 | 先对A节点倒序访问其每一个父节点，存在哈希表里  | $O(h)$|$(1)$|
|  哈希表法+分治法 | 对A节点倒序访问其每一个父节点，判断是否在哈希表里，最先在哈希表里的就是最近的公共父节点  | $O(h)$|$(1)$|

## 解题思路2追赶法

借鉴LintCode 380.intersection of two linkedList的解法。 p1, p2分别从A，B出发， 向root方向遍历。 p1达到root之后， 从B开始重新向root遍历。p2达到root之后， 从A开始重新向root遍历。 p1和p2在第二次遍历时，一定会在第一个intersection（i.e LCA）相遇。 时间复杂度O(h)，h是数的最大高度。
这是一个追及问题, A, B走到root之后互换指针位置再走, 那么p1, p2分别就都会走A的深度+B的深度, 然后root到公共祖先的长度是一样的, 可以推得两个指针一定会在公共祖先处相遇, 这样走的总路程才会一样

## 解题思路3双栈倒序查找

      4
     / \
    3   7
       / \
      5   6

求3和5的最近祖先
3的祖先存储在栈A： 4 ，3
5的祖先存储在栈B： 4，7，5

## 哈希法代码实现

```java
/**
 * Definition of ParentTreeNode:
 * 
 * class ParentTreeNode {
 *     public ParentTreeNode parent, left, right;
 * }
 */
public class Solution {
    /*
     * @param root: The root of the tree
     * @param A: node in the tree
     * @param B: node in the tree
     * @return: The lowest common ancestor of A and B
     */
    public ParentTreeNode lowestCommonAncestorII(ParentTreeNode root, ParentTreeNode A, ParentTreeNode B) {
        // write your code here
        Set<ParentTreeNode> parentSet = new HashSet<>();
        ParentTreeNode curr = A;
        while(curr != null){
            parentSet.add(curr);
            curr = curr.parent;
        }

        curr = B;
        while(curr!=null){
            if(parentSet.contains(curr)){
                return curr;
            }
            curr = curr.parent;
        }
        return null;
    }
}
```

## 追赶法代码实现

```java
/**
 * Definition of ParentTreeNode:
 * 
 * class ParentTreeNode {
 *     public ParentTreeNode parent, left, right;
 * }
 */


public class Solution {
   public ParentTreeNode lowestCommonAncestorII(ParentTreeNode root, ParentTreeNode A, ParentTreeNode B) {
    // write your code here
    ParentTreeNode p1 = A, p2 = B;
	while (p1 != p2) {
        p1 = p1.parent == null ? B : p1.parent;
        p2 = p2.parent == null ? A : p2.parent; 
    }
    return p1;
}
}

```

## 双栈倒序查找

```java
/**
 * Definition of ParentTreeNode:
 * 
 * class ParentTreeNode {
 *     public ParentTreeNode parent, left, right;
 * }
 */


public class Solution {
   /*
     * @param root: The root of the tree
     * @param A: node in the tree
     * @param B: node in the tree
     * @return: The lowest common ancestor of A and B
     */
    public ParentTreeNode lowestCommonAncestorII(ParentTreeNode root, ParentTreeNode A, ParentTreeNode B) {
        // write your code here
        Stack<ParentTreeNode> pathA = getPath(A);
        Stack<ParentTreeNode> pathB = getPath(B);
        
        ParentTreeNode result = root;
        while (!pathA.isEmpty() && !pathB.isEmpty()) {
            if (pathA.peek() != pathB.peek()) {
                break;
            }
            result = pathA.pop();
            pathB.pop();
        }
        return result;
    }
    
    private Stack<ParentTreeNode> getPath(ParentTreeNode node) {
        Stack<ParentTreeNode> path = new Stack<>();
        while (node != null) {
            path.push(node);
            node = node.parent;
        }
        return path;
    }
}

```