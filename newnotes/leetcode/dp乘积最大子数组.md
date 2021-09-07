# 乘积最大子数组

## 题目链接

#### [乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

## 题目描述

给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

 

示例 1:

输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
示例 2:

输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。



## 解题思路

| 方法         | 描述         | 时间复杂度 | 空间复杂度 |
| :----------- | :----------- | :--------- | :--------- |
| dp动态规划法 | 下文代码实现 |            |            |



## 代码实现

```java
/*
 * @lc app=leetcode.cn id=152 lang=java
 *
 * [152] 乘积最大子数组
 */

// @lc code=start
class Solution {
    public int maxProduct(int[] nums) {
        if(nums.length == 0){
            return 0;
        }
        int[] f = new int[nums.length];
        int[] g = new int[nums.length];
        int res = Integer.MIN_VALUE;
        for(int i = 0; i < nums.length;i++){
            f[i] = nums[i];
            if(i > 0){
                f[i] = Math.max(nums[i]*f[i-1],nums[i]*g[i-1]);
            }
            g[i] = nums[i];
            if(i > 0 ){
                g[i] = Math.min(nums[i]*f[i-1],nums[i]*g[i-1]);
            }
            res = Math.max(f[i],res);
        }
        return res;
    }
}
// @lc code=end


```

