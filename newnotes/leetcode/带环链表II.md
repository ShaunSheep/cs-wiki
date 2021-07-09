
#  带环链表 II

## 题目链接

[带环链表 II](https://www.lintcode.com/problem/103/?_from=collection&fromId=161)

## 题目描述

## 解题思路
| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
|  双指针+链表 | 下文代码实现  | $O(1)$|$(1)$|



## 代码实现

```java
/**
 * Definition for ListNode
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */

public class Solution {
   public ListNode detectCycle(ListNode head) {
        if (head == null || head.next==null) {
            return null;
        }

        ListNode fast, slow;
        fast = head.next;			//快指针
        slow = head;				//慢指针
        while (fast != slow) {		//快慢指针相遇
            if(fast==null || fast.next==null)
                return null;
            fast = fast.next.next;
            slow = slow.next;
        } 
        
        while (head != slow.next) {  //同时移动head和慢指针
            head = head.next;
            slow = slow.next;
        }
        return head;				//两指针相遇处即为环的入口
    }
}
```