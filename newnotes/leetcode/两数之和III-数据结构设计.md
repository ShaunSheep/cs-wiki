
#  两数之和 III-数据结构设计

## 题目链接

[两数之和 III-数据结构设计](https://leetcode-cn.com/problems/two-sum-iii-data-structure-design)

## 题目描述

描述

设计b并实现一个 TwoSum 类。他需要支持以下操作:add 和 find。
add -把这个数添加到内部的数据结构。
find -是否存在任意一对数字之和等于这个值

样例

样例 1:
```shell
add(1);add(3);add(5);
find(4)//返回true
find(7)//返回false
```


## 解题思路
| 方法  |描述 |时间复杂度 |空间复杂度|
|---|---|---|---|
|  双指针 | 下文代码实现  | add和find都是$O(N)$|$O(N)$|
|  哈希法 | 下文代码实现  | add是$O(1)$，find是$$O(N)|$(N)$|


## 哈希法

?> 优点：add速度快O(1)，无需像双指针那样排序

```java
import java.util.HashMap;
import java.util.Map;

public class TwoSum {
    private Map<Integer, Integer> counter;

    public TwoSum() {
        counter = new HashMap<>();
    }

    /**
     * @param number: An integer
     * @return: nothing
     */
    public void add(int number) {
        // write your code here
        // key is value ,value is [origin value + 1,0 + 1]
        counter.put(number, counter.getOrDefault(number, 0) + 1);
    }

    /**
     * @param value: An integer
     * @return: Find if there exists any pair of numbers which sum is equal to the
     *          value.
     */
    public boolean find(int value) {
        // write your code here
        for (Integer key : counter.keySet()) {
            // searchKey + key == value
            int searchKey = value - key;
            int reulstCount = key == searchKey ? 2 : 1;
            if (counter.getOrDefault(searchKey, 0) >= reulstCount) {
                return true;
            }
        }
        return false;
    }
}

```

## 双指针

!>缺点：数据量大时会超时

### 代码实现

```java
public class TwoSum {
    List<Integer> mSums ;
    public TwoSum(){
        mSums = new ArrayList<>();
    }
    /**
     * @param number: An integer
     * @return: nothing
     */
    public void add(int number) {
        // write your code here
        mSums.add(number);
        // sort array
        int index = mSums.size() -1;
        // keep ascending order
        while(index > 0 && mSums.get(index-1) > mSums.get(index) ){
            // left value > right value
            // swap
            int temp = mSums.get(index);
            mSums.set(index,mSums.get(index-1));
            mSums.set(index-1,temp);
            index --;
        }
    }

    /**
     * @param value: An integer
     * @return: Find if there exists any pair of numbers which sum is equal to the value.
     */
    public boolean find(int value) {
        // write your code here
        int left = 0 ;
        int right = mSums.size()-1;
        while(left<right){
            int currentSum = mSums.get(left)+mSums.get(right);
            if(currentSum < value){
                left++;
            }else if(currentSum >value){
                right--;
            } else{
                return true;
            }
        }
        return false;

    }
}
```