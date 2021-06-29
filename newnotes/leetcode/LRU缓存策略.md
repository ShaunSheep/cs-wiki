

#  标题模板

## 题目链接

[LRU缓存策略](https://www.lintcode.com/problem/134/?_from=collection&fromId=161)

## 题目描述

描述

为最近最少使用（LRU）缓存策略设计一个数据结构，它应该支持以下操作：获取数据和写入数据。

- `get(key)` 获取数据：如果缓存中存在key，则获取其数据值（通常是正数），否则返回-1。
- `set(key, value)` 写入数据：如果key还没有在缓存中，则设置或插入其数据值。当缓存达到上限，它应该在写入新数据之前删除最近最少使用的数据用来腾出空闲位置。

最终, 你需要返回每次 `get` 的数据.

**样例**

**样例 1:**

```java
输入：
LRUCache(2)
set(2, 1)
set(1, 1)
get(2)
set(4, 1)
get(1)
get(2)
输出：[1,-1,1]
解释：
cache上限为2，set(2,1)，set(1, 1)，get(2) 然后返回 1，set(4,1) 然后 delete (1,1)，因为 （1,1）最少使用，get(1) 然后返回 -1，get(2) 然后返回 1。
```

**样例 2:**

```java
输入：
LRUCache(1)
set(2, 1)
get(2)
set(3, 2)
get(2)
get(3)
输出：[1,-1,2]
解释：
cache上限为 1，set(2,1)，get(2) 然后返回 1，set(3,2) 然后 delete (2,1)，get(2) 然后返回 -1，get(3) 然后返回 2。
```



## 解题思路

| <div style="width:70pt">方法</div>  |描述 |<div style="width:70pt">时间复杂度</div> |<div style="width:70pt">空间复杂度</div>|
|---|---|---|---|
| 链表 | 下文代码实现  | $O(1)$|$(1)$|

- 链表
- 模拟

题解： 涉及删除和移动操作，使用链表，链表是有序的，一直维护，近期最多使用的放于尾部，那么每次缓存达到上限的时候，删除头部即可，其余为链表的基础操作模拟即可。

## 代码实现

```java
public class LRUCache {
    private class Node{
        Node prev;
        Node next;
        int key;
        int value;

        public Node(int key, int value) {
            this.key = key;
            this.value = value;
            this.prev = null;
            this.next = null;
        }
    }

    private int capacity;
    private HashMap<Integer, Node> hs = new HashMap<Integer, Node>();
    private Node head = new Node(-1, -1);
    private Node tail = new Node(-1, -1);

    public LRUCache(int capacity) {
        this.capacity = capacity;
        tail.prev = head;
        head.next = tail;
    }

    public int get(int key) {
        if( !hs.containsKey(key)) {			//key找不到
            return -1;
        }

        // remove current
        Node current = hs.get(key);
        current.prev.next = current.next;
        current.next.prev = current.prev;

        // move current to tail
        move_to_tail(current);			//每次get，使用次数+1，最近使用，放于尾部

        return hs.get(key).value;
    }

    public void set(int key, int value) {			//数据放入缓存
        // get 这个方法会把key挪到最末端，因此，不需要再调用 move_to_tail
        if (get(key) != -1) {
            hs.get(key).value = value;
            return;
        }

        if (hs.size() == capacity) {		//超出缓存上限
            hs.remove(head.next.key);		//删除头部数据
            head.next = head.next.next;
            head.next.prev = head;
        }

        Node insert = new Node(key, value);		//新建节点
        hs.put(key, insert);
        move_to_tail(insert);					//放于尾部
    }

    private void move_to_tail(Node current) {    //移动数据至尾部
        current.prev = tail.prev;
        tail.prev = current;
        current.prev.next = current;
        current.next = tail;
    }
}
```

