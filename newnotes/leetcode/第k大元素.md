# 第k大元素

## 题目链接

[第k大元素]()

## 题目描述
在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

示例 1:
```shell
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```
示例 2:
```shell
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```
## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  双指针法 | 划分法，划分思想，快速选择法  | $O(n)$|$(1)$|

其实是快速排序算法的变体，通过快速排序算法的partition步骤，可以将小于pivot的值划分到pivot左边，大于pivot的值划分到pivot右边，所以可以直接得到pivot的rank。从而缩小范围继续找第k大的值。
partition步骤：
1. 令left = start，right = end，pivot = nums[left]。
2. 当nums[left] < pivot时，left指针向右移动。
3. 当nums[right] > pivot时，right指针向左移动。
4. 交换两个位置的值，right指针左移，left指针右移。
5. 直到两指针相遇，否则回到第2步。
6. 每次partition后根据pivot的位置，寻找下一个搜索的范围。

## 代码实现

```java
// @lc code=start
class Solution {
    public int findKthLargest(int[] nums, int k) {
        // back conditions
        if (nums == null || nums.length == 0 || k < 1 || k > nums.length) {
            return -1;
        }

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
// @lc code=end


````