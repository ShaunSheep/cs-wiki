# 1. twoSum

## 题目链接

[两数之和](https://leetcode-cn.com/problems/two-sum/)<br>

?>另外一篇两数之和的进阶版笔记在这里 [两数之和](newnotes/leetcode/两数之和.md)，伪代码思路更清晰，代码抽象设计更好。

## 题目描述

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

你可以按任意顺序返回答案
示例 1：

```js
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 
```

## 解题思路

一共有两种解题思路

|思路   | 概述| 时间复杂度 | 空间复杂度 |
|---|---|---|---|
| 暴力数组遍历法 | 枚举两个不同下标的和：for(int i...){for (int j...)}<br>逐个判断两数之和是否等于target：num[i]+num[j] == target<br> | $O(n^2)$ | $O(1)$ |
| 哈希表查找法 | <img src="/newnotes/pics/twoSum-hash.png"> | $O(n)$ | $O(n)$ |



## 代码实现

?>为了锻炼英文写作，原谅我注释使用英文<br>
暴力数组遍历法-twoSumO_Square<br>
哈希表查找法-twoSumO_N<br>

```java
import java.util.HashMap;
import java.util.Map;

/*
 * @lc app=leetcode.cn id=1 lang=java
 *
 * [1] 两数之和
 */

// @lc code=start
class Solution {
    /**
     * 
     * @param nums   [3,2,4]
     * @param target 6
     * @return [1,2]
     */
    public int[] twoSum(int[] nums, int target) {
        // bug free
        if (nums == null || nums.length == 0) {
            return new int[0];
        }
        // solution
        // return twoSumO_Square(nums, target);
        return twoSumO_N(nums, target);
    }

    /**
     * time O(n^2) input x and search targe -x <br>
     * memory O(1)
     * 
     * @param nums
     * @param target
     * @return
     */
    public int[] twoSumO_Square(int[] nums, int target) {
        // work code
        int[] result = new int[2];
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                // x == targe - y
                if (nums[i] + nums[j] == target) {
                    result[0] = i;
                    result[1] = j;
                }
            }
        }
        return result;
    }

    /**
     * time O(n) <br>
     * memory O(N)
     * 
     * @param nums
     * @param target
     * @return
     */
    public int[] twoSumO_N(int[] nums, int target) {
        // key : nums[i]
        // value : i
        Map<Integer, Integer> targetMap = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            // isExist the map key
            // which equeal the result target - nums[i] ?
            if (targetMap.containsKey(target - nums[i])) {
                // map.get(i) i
                return new int[] { targetMap.get(target - nums[i]), i };
            }
            targetMap.put(nums[i], i);
        }
        return new int[0];
    }
}
// @lc code=end

```