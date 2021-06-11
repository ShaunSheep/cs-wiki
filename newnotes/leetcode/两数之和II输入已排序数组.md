
#  两数之和 II - 输入有序数组

## 题目链接

[两数之和 II - 输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/description/)

## 题目描述
给定一个已按照 升序排列  的整数数组 numbers ，请你从数组中找出两个数满足相加之和等于目标数 target 。

函数应该以长度为 2 的整数数组的形式返回这两个数的下标值。numbers 的下标 从 1 开始计数 ，所以答案数组应当满足 1 <= answer[0] < answer[1] <= numbers.length 。

你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。
 

示例 1：

```shell
输入：numbers = [2,7,11,15], target = 9
输出：[1,2]
解释：2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。
```

示例 2：
```shell
输入：numbers = [2,3,4], target = 6
输出：[1,3]
```
示例 3：

```shell
输入：numbers = [-1,0], target = -1
输出：[1,2]
```
 
?><br>
提示：<br>
    2 <= numbers.length <= 3 * 104<br>
    -1000 <= numbers[i] <= 1000<br>
    numbers 按 递增顺序 排列<br>
    -1000 <= target <= 1000<br>
    仅存在一个有效答案<br>



## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  双指针法 | 相向双指针  | $O(N)$|$(1)$|
|  第一步 | 初始化`start`和`end`两根指针  | $O(N)$|$(1)$|
|  第二步 | 计算`start`和`end`两个位置元素之和`currentTarget`  | $O(N)$|$(1)$|
|  第三步 | `currentTarget` 和`target`的大小关系，相等则返回`start+`，`end+1`  | $O(N)$|$(1)$|
|  第四步 | `currentTarget>target`,则指针左移，`end--` | $O(N)$|$(1)$|
|  第五步 | `currentTarget<target`,则指针右移，`start++` | $O(N)$|$(1)$|



## 代码实现

```java
// @lc code=start
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        // back conditions
        if (numbers == null || numbers.length == 0) {
            return new int[] { 0, 0 };
        }

        int start = 0;
        int end = numbers.length - 1;
        while (start < end) {
            int currentTarget = numbers[start] + numbers[end];
            if (currentTarget < target) {
                start++;
            } else if (currentTarget > target) {
                end--;
            } else {
                // index begin is 1
                return new int[] { start + 1, end + 1 };
            }
        }
        return new int[] { 0, 0 };
    }
}
// @lc code=end

```