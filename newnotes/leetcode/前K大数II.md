
#  前K大数 II

## 题目链接

[前K大数 II](https://www.lintcode.com/problem/545/?_from=collection&fromId=161)

## 题目描述

描述

实现一个数据结构，提供下面两个接口
1.`add(number)` 添加一个元素
2.`topk()` 返回此数据结构中最大的`k`个数字。当我们创建数据结构时，`k`是给定的。

样例

**样例1**

```
输入: 
s = new Solution(3);
s.add(3)
s.add(10)
s.topk()
s.add(1000)
s.add(-99)
s.topk()
s.add(4)
s.topk()
s.add(100)
s.topk()
		
输出: 
[10, 3]
[1000, 10, 3]
[1000, 10, 4]
[1000, 100, 10]

解释:
s = new Solution(3);
>> 生成了一个新的数据结构, 并且 k = 3.
s.add(3)
s.add(10)
s.topk()
>> 返回 [10, 3]
s.add(1000)
s.add(-99)
s.topk()
>> 返回 [1000, 10, 3]
s.add(4)
s.topk()
>> 返回 [1000, 10, 4]
s.add(100)
s.topk()
>> 返回 [1000, 100, 10]
```

**样例2**

```
输入: 
s = new Solution(1);
s.add(3)
s.add(10)
s.topk()
s.topk()

输出: 
[10]
[10]

解释:
s = new Solution(1);
>> 生成了一个新的数据结构, 并且 k = 1.
s.add(3)
s.add(10)
s.topk()
>> 返回 [10]
s.topk()
>> 返回 [10]
```

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 堆排序 | 下文代码实现  |  ||

用一个最大堆维护这些数字

当遇到add(number)这个操作时，就将元素加入到最大堆中

当遇到topk操作时就分K次取出最大堆顶元素，返回这些元素

然后将刚才加入的元素放回最大堆即可

## 代码实现

```java
public class Solution {
    private int maxSize;
    private Queue<Integer> minheap;
    public Solution(int k) {
        minheap = new PriorityQueue<>();
        maxSize = k;
    }

    public void add(int num) {
        if (minheap.size() < maxSize) {
            minheap.offer(num);
            return;
        }
        
        if (num > minheap.peek()) {
            minheap.poll();
            minheap.offer(num);
        }
    }

    public List<Integer> topk() {
        Iterator it = minheap.iterator();
        List<Integer> result = new ArrayList<Integer>();
        while (it.hasNext()) {
            result.add((Integer) it.next());
        }
        Collections.sort(result, Collections.reverseOrder());
        return result;
    }
};

```

