# **丑数II**

## 前置知识

- 丑数定义

  只包含质因子2，3和5的数称作丑数（Ugly Number）

- 6、8都是丑数，$6 =2 \times 3$，$8=2 \times 2 \times 2$

- 7、14不是丑数，因为他们质因数饱和7，

## **题目链接**

[丑数II](https://www.lintcode.com/problem/4/?_from=collection&fromId=161)

[丑数II-leetcode](https://leetcode-cn.com/problems/ugly-number-ii/)

## **题目描述**

描述

如果一个数只有质数因子`2`，`3`，`5` ，那么这个数是一个丑数。

前10个丑数分别为 `1, 2, 3, 4, 5, 6, 8, 9, 10, 12...`设计一个算法，找出第*N*个丑数。

我们可以认为 `1` 也是一个丑数。

$1 \leq n \leq 10^{5}1≤*n*≤105$

样例

**样例 1：**

输入：

```java
n = 9
```

输出：

```java
10
```

解释：

[1,2,3,4,5,6,8,9,10,....]，第9个丑数为10。

**样例 2：**

输入：

```java
n = 1
```

输出：

```java
1
```

挑战

要求时间复杂度为 O(*n*log*n*) 或者 O(*n*)。

## **解题思路**

| <div style="width:70pt">方法</div> | 描述 | <div style="width:70pt">时间复杂度</div> | <div style="width:70pt">空间复杂度</div> |
| ---------------------------------- | ---- | ---------------------------------------- | ---------------------------------------- |
| HashMap + Heap | 下文代码实现 | $O(n) $|$O(nlogn)$ |





## **代码实现**



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

