**# 丑数II**



**## 题目链接**



[丑数II](https://www.lintcode.com/problem/4/?_from=collection&fromId=161)

[丑数II-leetcode](https://leetcode-cn.com/problems/ugly-number-ii/)

**## 题目描述**



**## 解题思路**

| <div style="width:70pt">方法</div> | 描述 | <div style="width:70pt">时间复杂度</div> | <div style="width:70pt">空间复杂度</div> |
| ---------------------------------- | ---- | ---------------------------------------- | ---------------------------------------- |
| HashMap + Heap | 下文代码实现 | $O(n) $|$O(nlogn)$ |





**## 代码实现**



```java
class Solution {
    /**
     * @param n an integer
     * @return the nth prime number as description.
     */
    public int nthUglyNumber(int n) {
        // Write your code here
        Queue<Long> Q = new PriorityQueue<Long>();
        HashSet<Long> inQ = new HashSet<Long>();
        Long[] primes = new Long[3];
        primes[0] = Long.valueOf(2);
        primes[1] = Long.valueOf(3);
        primes[2] = Long.valueOf(5);
        for (int i = 0; i < 3; i++) {
            Q.add(primes[i]);
            inQ.add(primes[i]);
        }
        Long number = Long.valueOf(1);
        for (int i = 1; i < n; i++) {
            number = Q.poll();
            for (int j = 0; j < 3; j++) {
                if (!inQ.contains(primes[j] * number)) {
                    Q.add(number * primes[j]);
                    inQ.add(number * primes[j]);
                }
            }
        }
        return number.intValue();
    }
};
```

