#  k数和（二）

## 题目链接

[90 · k数和（二） - LintCode](https://www.lintcode.com/problem/90/)

这道题的低级版是[数字组合](newnotes/leetcode/数字组合)，数字组合题目要求了目标和没有限制数字个数，k树和限制了数字个数

这道题的普通低级版是[k数和](newnotes/leetcode/k数和II)，题目k数和要求了数字的个数，而数字组合不要求数字的个数

这三道题的关系是

1. 子集属于数字组合
2. 数字组合属于k数和II
3. 子集、数字组合、k数和II属于组合DFS

组合DFS类问题，必须保证按数组顺序访问元素，且一个元素只访问一次，所以需要index来去重，记录当前访问的位置

排列DFS类问题，不需要按顺序访问，只需要保证一个元素访问一次，所以需要visited数组来记录是否访问过，记录已经访问过的节点

## 题目描述

描述

给定`n`个不同的正整数，整数`k`（1<=k<=n1<=*k*<=*n*）以及一个目标数字。
在这`n`个数里面找出`K`个数，使得这`K`个数的和等于目标数字，你需要找出所有满足要求的方案。

样例

**样例 1：**

输入：

```java
数组 = [1,2,3,4]
k = 2
target = 5
```

输出：

```java
[[1,4],[2,3]]
```

解释：

1+4=5,2+3=5

**样例 2：**

输入：

```java
数组 = [1,3,4,6]
k = 3
target = 8
```

输出：

```java
[[1,3,4]]
```

解释：

1+3+4=8



## 解题思路

| <div style="width:70pt">方法</div> | 描述                | <div style="width:70pt">时间复杂度</div>          | <div style="width:70pt">空间复杂度</div> |
| ---------------------------------- | ------------------- | ------------------------------------------------- | ---------------------------------------- |
| dfs法                              | combine sum解题思路 | $O(Cnk)*k$方案总数是从n中取k个数组合，构造时间为n | $(1)$                                    |

类似[数字之和](newnotes/leetcode/数字组合)的解题思路，代码也类似,搜索树也类似。

## 代码实现

```java
public class Solution {
    /*
     * @param A: an integer array
     * @param k: a postive integer <= length(A)
     * @param target: an integer
     * @return: A list of lists of integer
     */
    public List<List<Integer>> kSumII(int[] A, int k, int target) {
        Arrays.sort(A);
        List<List<Integer>> subsets = new ArrayList();
        
        dfs(A, 0, k, target, new ArrayList<Integer>(), subsets);
        
        return subsets;
    }
    
    private void dfs(int[] A, int index, int k, int target,
                     List<Integer> subset,
                     List<List<Integer>> subsets) {
        if (k == 0 && target == 0) {
            subsets.add(new ArrayList<Integer>(subset));
            return;
        }
        if (k == 0 || target <= 0) {
            return;
        }
        
        for (int i = index; i < A.length; i++) {
            subset.add(A[i]);
            dfs(A, i + 1, k - 1, target - A[i], subset, subsets);
            subset.remove(subset.size() - 1);
        }
    }
}
```

k的作用是什么？

- 限制个数，比[数字之和](newnotes/leetcode/数字组合)多了这个参数k，所以多了一次判断`k==0`，是否已经访问了k个元素

target的作用是什么？

- 限制数字的和
- target<0,说明肯定找不到了，就跳出
- 每递归一层，k都会减少一，target都会减去一个元素的值，直至`k=0，target-nums[i]=0`

dfs退出条件是什么？

- 从当前点开始，已经访问完k个数，即k=0
- target减去每一层的数，最终等于0，即以index位置上的点开始，找到了符合target的k个数

dfs定义是什么？

- A是输入元素集合

- index是什么？

- k是什么？答：是剩余几个位置可以放元素，刚开始是k个，递归一层是k-1个，k=0的时候，就退出了

- dfs函数的入参target是两数之差，用于判断数组中是否存在一点，加上当前点的和等于target

- subset是什么？答：当前层的子集，，，，，，，

- subsets是什么？答：所有符合条件的子集，在满足退出条件的时候，会添加该层的子集至subsets

  

