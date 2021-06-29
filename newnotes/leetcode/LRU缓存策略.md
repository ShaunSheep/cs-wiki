

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
| 链表+哈希表 | 下文代码实现  | $O(1)$|$(1)$|

- 链表
- 模拟

题干的数据结构，所需要提供以下特性：

- 支持头节点、尾结点
- 支持get访问某元素
  - 根据题意规定，通过get访问某元素时，需要将该元素移动至尾结点
- 支持delete删除某元素
  - 通过get访问某元素时候，将该元素移动至尾结点，就相当于删除了该元素的与老的节点的联系
- 支持移动某元素，并将其移动至尾结点
- 支持insert新元素
  - 根据题意规定，每个insert的新元素都要插入至链表的头节点
  - 题意还规定，如果链表已经满了，则先删除头结点的元素，再将新元素放至头结点的位置，完成新旧元素替换

我们知道，链表的优点是操作动态、访问静态，操作类行为是O(1)，如插入、删除；访问类行为是O(n)，如遍历、获取某元素

为了优化引入链表导致的访问效率低下，我们需要再加入一个数据结构哈希表，

最终，我认为LRUCache类的抽象ADT应该如下

- HashMap，key是insert接口传入的值，value是节点
- head，tail头尾两个Node类型节点
- get，访问一个元素
- moveToFail，移动一个元素至末尾
- insert，插入一个元素，会将该元素封装至一个新创建的Node节点中



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

