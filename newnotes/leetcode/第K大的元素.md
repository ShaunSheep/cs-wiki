
#  第k大元素

## 题目链接

[第k大元素](https://www.lintcode.com/problem/5)

## 题目描述

描述

在数组中找到第 k 大的元素。

你可以交换数组中的元素的位置。
1 \leq k \leq 10^{5}1≤*k*≤105

样例

**样例 1：**

输入：

```
k = 1
nums = [1,3,4,2]
```

输出：

```
4
```

解释：

第一大的元素是4。

**样例 2：**

输入：

```
k = 3
nums = [9,3,2,4,8]
```

输出：

```
4
```

解释：

第三大的元素是4。

挑战

要求时间复杂度为O(n)，空间复杂度为O(1)。

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|   Parition 思想 | 下文代码实现  | $O(1)$|$(1)$|

标准 Parition 模板

## 代码实现

```java
public class Solution {
    /**
     * @param n: An integer
     * @param nums: An array
     * @return: the Kth largest element
     */
    public int kthLargestElement(int k, int[] nums) {
    
        return partition(nums, 0, nums.length - 1, nums.length - k);
    }

    public int partition(int[] nums, int start, int end, int k) {
        if (start >= end) {
            // it's sorted
            return nums[k];
        }
        int left = start;
        int right = end;
        // is value, is not index
        int pivot = nums[(start + end) / 2];

        // is left<=right
        while (left <= right) {
            // fix bug pivot is value ,is not index
            while (left <= right && nums[left] <pivot) {
                left++;
            }
            while (left <= right && nums[right] > pivot) {
                right--;
            }
            // compare left and right value
            // fix bug descending sort
            // fix bug left compare right
            if (left <= right) {
                // only recursive base switch places
                int temp = nums[right];
                nums[right] = nums[left];
                nums[left] = temp;
                // fix bug need move index
                left++;
                right--;
            }
        }

        //
        if (k <= right) {
            return partition(nums, start, right, k);
        }
        if (k >= left) {
            return partition(nums, left, end, k);
        }
        // it's sorted
        return nums[k];
    }
}
```

