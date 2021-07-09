
#  第K大的元素 II

## 题目链接

[第K大的元素 II](https://www.lintcode.com/problem/606/?_from=collection&fromId=161)

## 题目描述

描述

找到数组中第K大的元素，N远大于K。请注意你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素

你可以改变数组中元素的位置

样例

**例1:**

```
输入:[9,3,2,4,8],3
输出:4
```

**例2:**

```
输入:[1,2,3,4,5,6,8,9,10,7],10
输出:1
```

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 数据结构优先级队列 | 下文代码实现  | $O(1)$|$(1)$|



## 代码实现

```java
public class Solution {
    /**
     * @param nums: an integer unsorted array
     * @param k: an integer from 1 to n
     * @return: the kth largest element
     */
    public int kthLargestElement2(int[] nums, int k) {
        // write your code here
                // Write your code here
        PriorityQueue<Integer> q = new PriorityQueue<Integer>(k);
        for (int num : nums) {
            q.offer(num);
            if (q.size() > k) {
                q.poll();
            }
        }
        return q.peek();
    }
}
```

