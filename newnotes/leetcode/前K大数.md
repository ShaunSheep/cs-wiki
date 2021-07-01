
#  前K大数

## 题目链接

([545 · 前K大数 - LintCode](https://www.lintcode.com/problem/544/?_from=collection&fromId=161))

## 题目描述

描述

在一个数组中找到前K大的数

样例

**样例1**

```
输入: [3, 10, 1000, -99, 4, 100] 并且 k = 3
输出: [1000, 100, 10]
```

**样例2**

```
输入: [8, 7, 6, 5, 4, 3, 2, 1] 并且 k = 5
输出: [8, 7, 6, 5, 4]
```



## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 快速查找或者堆排序 | 下文代码实现  |  ||



## 堆排序代码实现

```java
class Solution {
    /*
     * @param nums an integer array
     * @param k an integer
     * @return the top k largest numbers in array
     */
     public int[] topk(int[] nums, int k) {
         PriorityQueue<Integer> minheap = new PriorityQueue<Integer>(k, new Comparator<Integer>() {
             public int compare(Integer o1, Integer o2) {
                 return o1 - o2;
             }
         });

         for (int i : nums) {
             minheap.add(i);
             if (minheap.size() > k) {
                minheap.poll();
             }
         }

         int[] result = new int[k];
         for (int i = 0; i < result.length; i++) {
             result[k - i - 1] = minheap.poll();
         }
         return result;
    }
};

```

## 快速选择代码实现

```java
// base on quicksort
import java.util.Random;

class Solution {
    /*
     * @param nums an integer array
     * @param k an integer
     * @return the top k largest numbers in array
     */
    public int[] topk(int[] nums, int k) {
        // Write your code here
        quickSort(nums, 0, nums.length - 1, k);

        int[] topk = new int[k];
        for (int i = 0; i < k; ++i)
            topk[i] = nums[i];

        return topk;
    }
    
    private void quickSort(int[] A, int start, int end, int k) {
        if (start >= k)
            return;

        if (start >= end) {
            return;
        }
        
        int left = start, right = end;
        // key point 1: pivot is the value, not the index
        Random rand =new Random(end - start + 1);
        int index = rand.nextInt(end - start + 1) + start;
        int pivot = A[index];

        // key point 2: every time you compare left & right, it should be 
        // left <= right not left < right
        while (left <= right) {
            // key point 3: A[left] < pivot not A[left] <= pivot
            while (left <= right && A[left] > pivot) {
                left++;
            }
            // key point 3: A[right] > pivot not A[right] >= pivot
            while (left <= right && A[right] < pivot) {
                right--;
            }

            if (left <= right) {
                int temp = A[left];
                A[left] = A[right];
                A[right] = temp;
                
                left++;
                right--;
            }
        }
        
        quickSort(A, start, right, k);
        quickSort(A, left, end, k);
    }
};
```

