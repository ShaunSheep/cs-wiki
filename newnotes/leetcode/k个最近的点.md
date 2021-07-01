
#  K个最近的点

## 题目链接

[K个最近的点](https://www.lintcode.com/problem/612/?_from=collection&fromId=161)

## 题目描述

描述

在二维空间里给定一些 `points` 和一个 `origin`，从 `points` 中找到 `k` 个离 `origin` 欧几里得距离最近的点。按照欧几里得距离由小到大返回。如果两个点有相同欧几里得距离，则按照x值来排序；若x值也相同，就再按照y值排序。

样例

例1:

```
输入: points = [[4,6],[4,7],[4,4],[2,5],[1,1]], origin = [0, 0], k = 3 
输出: [[1,1],[2,5],[4,4]]
```

例2:

```
输入: points = [[0,0],[0,9]], origin = [3, 1], k = 1
输出: [[0,0]]
```

挑战

O(nlogn) 的算法也是可以通过的, 但是你能否想到 O(nlogk) 的解决方案呢

## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  暴力法 | 下文代码实现  | $O(1)$|$(1)$|



## 代码实现

```java
/**
 * Definition for a point.
 * class Point {
 *     int x;
 *     int y;
 *     Point() { x = 0; y = 0; }
 *     Point(int a, int b) { x = a; y = b; }
 * }
 */
public class Solution {
    /**
     * @param points a list of points
     * @param origin a point
     * @param k an integer
     * @return the k closest points
     */
    private Point global_origin = null;
    public Point[] kClosest(Point[] points, Point origin, int k) {
        // Write your code here
        global_origin = origin;
        PriorityQueue<Point> pq = new PriorityQueue<Point> (k, new Comparator<Point> () {
            @Override
            public int compare(Point a, Point b) {
                int diff = getDistance(b, global_origin) - getDistance(a, global_origin);
                if (diff == 0)
                    diff = b.x - a.x;
                if (diff == 0)
                    diff = b.y - a.y;
                return diff;
            }
        });

        for (int i = 0; i < points.length; i++) {
            pq.offer(points[i]);
            if (pq.size() > k)
                pq.poll();
        }
        
        k = pq.size();
        Point[] ret = new Point[k];  
        while (!pq.isEmpty())
            ret[--k] = pq.poll();
        return ret;
    }
    
    private int getDistance(Point a, Point b) {
        return (a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y);
    }
}
```

