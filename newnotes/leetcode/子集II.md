
#  子集 II

## 题目链接

[子集 II](https://www.lintcode.com/problem/18/)

[ 子集 II - 力扣](https://leetcode-cn.com/problems/subsets-ii/solution/zi-ji-ii-by-leetcode-solution-7inq/)

## 题目描述

**描述**

给定一个可能具有重复数字的列表，返回其所有可能的子集。

子集中的每个元素都是非降序的两个子集间的顺序是无关紧要的解集中不能包含重复子集

样例

**样例 1：**

输入：

```
nums = [0]
```

输出：

```
[
  [],
  [0]
]
```

解释：

[0]的子集只有[]和[0]。

**样例 2：**

输入：

```
nums = [1,2,2]
```

输出：

```
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

解释：

```
[1,2,2]不重复的子集有[],[1],[2],[1,2],[2,2],[1,2,2]。
```



?>挑战<br>

你可以同时用递归与非递归的方式解决么？

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| dfs | 下文代码实现  | $O(n∗2n)$ |$O(n∗2n)$|

参考网上的图解，进行去重操作。搜索树采用[两种搜索树画法](newnotes/leetcode/DFS#两种搜索树画法)的画法1

![90.子集II.png](http://cdn.yangchaofan.cn/typora/子集II--画法1)

## 代码实现

```java
public class Solution {
    /**
     * @param nums: A set of numbers.
     * @return: A list of lists. All valid subsets.
     */
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> subsets = new ArrayList<>();
        
        Arrays.sort(nums);
        dfs(nums, 0, -1, new ArrayList<>(), subsets);
        
        return subsets;
    }
    
    private void dfs(int[] nums,
                     int index,
                     int lastSelectedIndex,
                     List<Integer> subset,
                     List<List<Integer>> subsets) {
        if (index == nums.length) {
            subsets.add(new ArrayList<Integer>(subset));
            return;
        }
        
        dfs(nums, index + 1, lastSelectedIndex, subset, subsets);
        // index位置的数字 是否等于上一个位置的数字
        if (index > 0 && nums[index] == nums[index - 1] && index - 1 != lastSelectedIndex) {
            return;
        }
        
        subset.add(nums[index]);
        dfs(nums, index + 1, index, subset, subsets);
        subset.remove(subset.size() - 1);
    }
}
```

- 为什么要排序？
  - 让重复的数字挤在一起，只取第一个重复的数组，参考`index`的作用
- nums是输入的数组元素
- index是用于确定我可以从什么位置开始挑数字，如果有重复，只挑出第一个重复的
  - 有10个2，只取第一个2，判断方式是`&& nums[index] == nums[index - 1] && index - 1 != lastSelectedIndex) {`
- subsetes是什么，截止当前层为止的子集
- subset是什么,当前层所持有的子集，这是公共的内存空间，所以需要new List封装一遍，记住当前层所持有的子集，因为当前层结束的时候会remove掉末尾元素
- 为什么要remove，参考搜索树的dfs搜索过程，

<<<<<<< HEAD
![mark](http://cdn.yangchaofan.cn/BlogGifRes/20210626/fTXTJLJ6KnTT.jpg?imageslim)
=======
>>>>>>> ad121414531ae704742b31cb26d82508ff7da1d8
