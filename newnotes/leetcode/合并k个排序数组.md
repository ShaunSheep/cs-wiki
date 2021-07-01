
#  合并k个排序数组

## 题目链接

[合并k个排序数组](https://www.lintcode.com/problem/486/?_from=collection&fromId=161)

## 题目描述



描述

将 *k* 个有序数组合并为一个大的有序数组。

样例

**样例 1:**

```
输入:
  [
    [1, 3, 5, 7],
    [2, 4, 6],
    [0, 8, 9, 10, 11]
  ]
输出: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
```

**样例 2:**

```
输入: 
  [
    [1,2,3],
    [1,2]
  ]
输出: [1,1,2,2,3]
```

挑战

在 O(N log k) 的时间复杂度内完成：

- *N* 是所有数组包含的整数总数量。
- *k* 是数组的个数。

## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 堆排序 | 下文代码实现  | $O(1)$|$(1)$|

可以用堆做到 O(N log k) 的时间复杂度.

初始将所有数组的首个元素入堆, 并记录入堆的元素是属于哪个数组的.

每次取出堆顶元素, 并放入该元素所在数组的下一个元素.

## 代码实现

```java
class Element {
    public int row, col, val;
    Element(int row, int col, int val) {
        this.row = row;
        this.col = col;
        this.val = val;
    }
}

public class Solution {
    private Comparator<Element> ElementComparator = new Comparator<Element>() {
        public int compare(Element left, Element right) {
            return left.val - right.val;
        }
    };
    
    /**
     * @param arrays k sorted integer arrays
     * @return a sorted array
     */
    public int[] mergekSortedArrays(int[][] arrays) {
        if (arrays == null) {
            return new int[0];
        }
        
        int total_size = 0;
        Queue<Element> Q = new PriorityQueue<Element>(
            arrays.length, ElementComparator);
            
        for (int i = 0; i < arrays.length; i++) {
            if (arrays[i].length > 0) {
                Element elem = new Element(i, 0, arrays[i][0]);
                Q.add(elem);
                total_size += arrays[i].length;
            }
        }
        
        int[] result = new int[total_size];
        int index = 0;
        while (!Q.isEmpty()) {
            Element elem = Q.poll();
            result[index++] = elem.val;
            if (elem.col + 1 < arrays[elem.row].length) {
                elem.col += 1;
                elem.val = arrays[elem.row][elem.col];
                Q.add(elem);
            }
        }
        
        return result;
    }
}
```

