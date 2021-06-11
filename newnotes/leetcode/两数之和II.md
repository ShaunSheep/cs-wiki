

#   两数之和 II

## 题目链接

[ 两数之和 II](https://www.lintcode.com/problem/443/?_from=ladder&fromId=161)

## 题目描述

描述
给一组整数，问能找出多少对整数，他们的和大于一个给定的目标值。请返回答案。
样例
样例 1:
```shell
输入: [2, 7, 11, 15], target = 24
输出: 1
解释: 11 + 15 是唯一的一对
```

样例 2:
```shell
输入: [1, 1, 1, 1], target = 1
输出: 6
```
!>挑战<br>
O(1) 额外空间以及 O(nlogn) 时间复杂度


## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  双指针 | 相向双指针，排序，打擂台,求和  | $O(nlogn)$|$(1)$|
|  第一步 | 满足条件`nums[start]+nums[end]>target`,则`result+=end-start`，且同时移动指针`end--`  | $O(nlogn)$|$(1)$|
|  第二步 | 不满足条件同时移动指针`start++`  | $O(nlogn)$|$(1)$|


## 代码实现
```java

public class Solution {
    /**
     * fix bug :back conditons is 0
     * 
     * @param nums:   an array of integer
     * @param target: An integer
     * @return: an integer
     */
    public int twoSum2(int[] nums, int target) {
        // write your code here
        if (nums == null || nums.length == 0) {
            return 0;
        }
        Arrays.sort(nums);
        int start = 0;
        int end = nums.length - 1;
        int count = 0;
        while (start < end) {
            if (nums[start] + nums[end] <= target) {
                start++;
            } else {
                // sum temp result
                int size = end - start;
                count += size;
                end--;
            }
        }
        return count;
    }
}
```