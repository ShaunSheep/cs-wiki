#  k数和（二）

## 题目链接

[k数和（二）]([90 · k数和（二） - LintCode](https://www.lintcode.com/problem/90/))

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

| <div style="width:70pt">方法</div> | 描述         | <div style="width:70pt">时间复杂度</div> | <div style="width:70pt">空间复杂度</div> |
| ---------------------------------- | ------------ | ---------------------------------------- | ---------------------------------------- |
| dfs法                              | 下文代码实现 | $O(1)$                                   | $(1)$                                    |



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

