
#  雇佣K个人的最低费用

## 题目链接

[雇佣K个人的最低费用 · Minimum Cost to Hire K Workers](https://www.jiuzhang.com/problem/minimum-cost-to-hire-k-workers/)

## 题目描述

有N名工人。 第i名工人具有质量quality[i]和最低工资预期工资wage[i]。

现在我们想雇用恰好K工人来组建一个付费团体。 雇用一组K工人时，我们必须按照以下规则付款：

- 与付费团体中的其他工作人员相比，付费团体中的每个工人都应按其质量比例获得报酬。
- 薪酬组中的每个工人必须至少获得最低工资预期。

返回满足上述条件的付费群体所需的最少金额。

1.1 <= K <= N <= 10000，其中N = quality.length = wage.length
2.1 <=quality[i] <= 10000
3.1 <=wage[i] <= 10000
4.在正确答案的10 ^ -5范围内的答案将被视为正确。

#### 样例

**例 1:**

```
输入: quality = [10,20,5], wage = [70,50,30], K = 2
输出: 105.00000
解释: 花费70雇佣0号，花费35雇佣2号
```

**例 2:**

```
输入: quality = [3,1,10,10,1], wage = [4,8,2,2,7], K = 3
输出: 30.66667
解释: 花费4雇佣0号，花费13.33333雇佣2号和3号 
```

## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  优先级队列和堆 | 下文代码实现  | $O(1)$|$(1)$|

对每一组的quality和wage计算时薪，按照时薪排序。 对于合法的k个数，为了满足条件，每个人的工资都大于初始工资，且成比例，所以每个人的时薪一定是这k个人中最高的那个人的时薪（否则最高的人不满足初始工资）。 所以对每个人的时薪找k个quality和最小的且时薪低于这个人的即可。 利用优先队列即可完成上述操作。


## 代码实现

```java
public class Solution {
   
 /**
     * @param quality: an array
     * @param wage: an array
     * @param K: an integer
     * @return: the least amount of money needed to form a paid group
     */
    class Pair {
        int q;
        double rank;
        Pair(int q, double rank) {
            this.q = q;
            this.rank = rank;
        }
    }
    public double mincostToHireWorkers(int[] quality, int[] wage, int K) {
        // Write your code here
        int len = quality.length;
        List<Pair> arr = new ArrayList<>();
        for (int i = 0; i < len; i++) {
            Pair pair = new Pair(quality[i], (double)wage[i] / quality[i]);
            arr.add(pair);
        }
        Collections.sort(arr, new Comparator<Pair>() {
            public int compare(Pair o1, Pair o2) {
                if (o1.rank == o2.rank) {
                    return o1.q - o2.q;
                }
                if (o1.rank < o2.rank) {
                    return -1;
                }
                else {
                    return 1;
                }
            }
        });
        Queue<Integer> q = new PriorityQueue<>(Collections.reverseOrder());
        double ans = Double.MAX_VALUE;
        int sum = 0;
        for (int i = 0; i < arr.size(); i++) {
            if (q.size() == K) {
                sum -= q.poll();
            }
            q.offer(arr.get(i).q);
            sum += arr.get(i).q;
            if (i >= K - 1) {
                ans = Math.min(ans, sum * arr.get(i).rank);
            }
        }
        return ans;
    }
}
```