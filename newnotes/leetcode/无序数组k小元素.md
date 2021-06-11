
#  无序数组K小元素

## 题目链接

[无序数组K小元素](https://www.lintcode.com/problem/461/?_from=ladder&fromId=161)

## 题目描述

描述
找到一个无序数组中第K小的数
样例

样例 1:
```shell
输入: [3, 4, 1, 2, 5], k = 3
输出: 3
```
样例 2:
```shell
输入: [1, 1, 1], k = 2
输出: 1
```
!>挑战<br>
O(nlogn)的算法固然可行, 但如果你能 O(n) 解决, 那就非常棒了.


## 解题思路

?>最简单的是直接排序，选择第k个位置的值，时间复杂度是$O(nlog)$；这里使用推荐双指针+快速排序模板

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  双指针法（形似） | 只是用了两根指针，相向双指针，核心思想用的是快速排序模板  | $O(n)$|$(1)$|


这题其实是快速排序算法的变体，通过快速排序算法的partition步骤，可以将小于pivot的值划分到pivot左边，大于pivot的值划分到pivot右边，所以可以直接得到pivot的rank。从而缩小范围继续找第k大的值。

partition步骤：

    1. 令left = start，right = end，pivot = nums[left]。
    2. 当nums[left] < pivot时，left指针向右移动。
    3. 当nums[right] > pivot时，right指针向左移动。
    4. 交换两个位置的值，right指针左移，left指针右移。
    5. 直到两指针相遇，否则回到第2步。

每次partition后根据pivot的位置，寻找下一个搜索的范围。



## 代码实现
```java
public class Solution {
    /**
     * @param k: An integer
     * @param nums: An integer array
     * @return: kth smallest element
     */
    public int kthSmallest(int k, int[] nums) {
         return quickSelect(nums, 0, nums.length - 1, k - 1);
    }

    /**
     * 
     * @param nums
     * @param i
     * @param j
     * @param k
     * @return
     */
    private int quickSelect(int[] nums, int start, int end, int k) {
        if (start == end) {
            return nums[start];
        }
        int left = start;
        int right = end;
        int pivot = nums[(start + end) / 2];

        while (left <= right) {
            while (left <= right && nums[left] < pivot) {
                left++;
            }

            while (left <= right && nums[right] > pivot) {
                right--;
            }
            if (left <= right) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
                left++;
                right--;
            }
        }

        // conditions
        if (right >= k && right >= start) {
            return quickSelect(nums, start, right, k);
        } else if (left <= k && left <= end) {
            return quickSelect(nums, left, end, k);
        }
        return nums[k];
    }
}

```