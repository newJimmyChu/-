# Java数据结构 （实现&总结）

### 1.1 简单HashMap实现

使用了固定size的数组（桶）来存储每个键值对，使用 x & (size - 1) 来计算键的哈希值

哈希冲突：将具有相同hash值的键值对储存在同一个桶的链表中，用头插法体现数据的时效性

后续需要实现HashMap的扩容功能 以及在存储大规模数据时，需要实现红黑树或者二叉树。

对于搜索在同一个桶中的对象，红黑树可以有效地降低搜索时间

备注：在实现Entry时，需要将key字段设置为final。这样可以有效地避免对键的修改

```java
class MyHashMap {

    private Entry[] entryArray;
    private int size = 65536;

    /** Initialize your data structure here. */
    public MyHashMap() {
        entryArray = new Entry[size];

    }

    /** value will always be non-negative. */
    public void put(int key, int value) {
        int h = hash(key);
        if (entryArray[h] != null) {
            if (entryArray[h].key == key) {
                entryArray[h].value = value;
                return;
            }

            Entry temp = entryArray[h].next;
            if (temp == null) {
                entryArray[h].next = new Entry(key, value);
                return;
            }
            while (temp.next != null) {
                if (temp.key == key) {
                    temp.value = value;
                    return;
                }
            }
            if (temp.key == key) {
                temp.value = value;
                return;
            }
            temp.next = new Entry(key, value);
        } else {
            entryArray[h] = new Entry(key, value);
        }
    }

    /** Returns the value to which the specified key is mapped, or -1 if this map contains no mapping for the key */
    public int get(int key) {
        int h = hash(key);
        if (entryArray[h] == null)
            return -1;
        if (entryArray[h].key == key)
            return entryArray[h].value;

        Entry temp = entryArray[h].next;
        while (temp != null) {
            if (temp.key == key)
                return temp.value;
            temp = temp.next;
        }
        return -1;

    }

    /** Removes the mapping of the specified value key if this map contains a mapping for the key */
    public void remove(int key) {
        int h = hash(key);
        if (entryArray[h] == null)
            return;
        if (entryArray[h].key == key) {
            entryArray[h] = entryArray[h].next;
            return;
        }

        Entry prev = entryArray[h];
        Entry temp = entryArray[h].next;
        while (temp != null) {
            if (temp.key == key) {
                prev.next = temp.next;
                return;
            }
            temp = temp.next;
            prev = prev.next;
        }
    }

    private int hash(int key) {
        return key & (size - 1);
    }
}

class Entry {

    public final int key;
    public int value;
    public Entry next;

    Entry(int key, int value) {
        this.key = key;
        this.value = value;
        this.next = null;
    }

    Entry(int key, int value, Entry next) {
        this.key = key;
        this.value = value;
        this.next = next;
    }

}
```



### 1.2 LinkedList 实现

该LinkedList为双向链表

备注：在删除节点时需要注意考虑头部删除，尾部删除的情况 

```java
class MyLinkedList {

    private ListNode head;
    private ListNode tail;
    private int size;

    /** Initialize your data structure here. */
    public MyLinkedList() {
        this.head = null;
        this.tail = null;
        this.size = 0;
    }

    /** Get the value of the index-th node in the linked list. If the index is invalid, return -1. */
    public int get(int index) {
        if (index == size - 1)
            return tail.val;
        if (index >= size)
            return -1;
        ListNode traverse = head;
        for (int i = 0; i < index; i ++) {
            traverse = traverse.next;
        }
        return traverse.val;
    }

    /** Add a node of value val before the first element of the linked list. After the insertion, the new node will be the first node of the linked list. */
    public void addAtHead(int val) {
        ListNode addedNode = new ListNode(val);
        if (head == null) {
            head = addedNode;
            tail = addedNode;
            size ++;
            return;
        }

        head.prev = addedNode;
        addedNode.next = head;
        head = addedNode;
        size ++;
    }

    /** Append a node of value val to the last element of the linked list. */
    public void addAtTail(int val) {
        ListNode addedNode = new ListNode(val);
        if (tail == null) {
            head = addedNode;
            tail = addedNode;
            size ++;
            return;
        }

        tail.next = addedNode;
        addedNode.prev = tail;
        tail = addedNode;
        size ++;
    }

    /** Add a node of value val before the index-th node in the linked list. If index equals to the length of linked list, the node will be appended to the end of linked list. If index is greater than the length, the node will not be inserted. */
    public void addAtIndex(int index, int val) {
        ListNode t = head;
        //System.out.println(size);
        //for (int i = 0; i < size; i ++)
        //    System.out.println(get(i));

        if (index == 0) {
            addAtHead(val);
        } else if (index == size) {
            addAtTail(val);
        } else if (index > size || index < 0) {
            return;
        } else {
            ListNode prev = head;
            ListNode next = head.next;
            for (int i = 0; i < index - 1; i ++) {
                prev = prev.next;
                next = next.next;
            }
            ListNode addedNode = new ListNode(val);
            addedNode.prev = prev;
            addedNode.next = next;
            prev.next = addedNode;
            next.prev = addedNode;
            size ++;
        }

    }

    /** Delete the index-th node in the linked list, if the index is valid. */
    public void deleteAtIndex(int index) {
        if (index >= size || size == 0)
            return;
        if (index == 0 && size == 1) {
            head = null;
            tail = null;
        } else if (index == 0) {
            head.next.prev = null;
            head = head.next;
        } else if (index == size - 1) {
            tail.prev.next = null;
            tail = tail.prev;
        } else {
            ListNode traverse = head;
            for (int i = 0; i < index; i ++) {
                traverse = traverse.next;
            }
            if (traverse.next != null) {
                traverse.next.prev = traverse.prev;
            }
            traverse.prev.next = traverse.next;
        }
        size --;
    }
}

class ListNode {

    public ListNode next;
    public ListNode prev;
    public int val;

    ListNode(int val) {
        this.val = val;
        this.next = null;
        this.prev = null;
    }

}

```

