## 1 Principle

### 1.1 What is a simply linked list

Like arrays, Linked List is a linear data structure. Unlike arrays, linked list elements are not stored at a contiguous location; the elements are linked using pointers.

![Linkedlist](https://media.geeksforgeeks.org/wp-content/cdn-uploads/gq/2013/03/Linkedlist.png)

### 1.2 Why Linked List?

Arrays can be used to store linear data of similar types, but arrays have the following limitations.
**1)** The size of the arrays is fixed: So we must know the upper limit on the number of elements in advance. Also, generally, the allocated memory is equal to the upper limit irrespective of the usage.
**2)** Inserting a new element in an array of elements is expensive because the room has to be created for the new elements and to create room existing elements have to be shifted.

For example, in a system, if we maintain a sorted list of IDs in an array id[].

id[] = [1000, 1010, 1050, 2000, 2040].

And if we want to insert a new ID 1005, then to maintain the sorted order, we have to move all the elements after 1000 (excluding 1000).
Deletion is also expensive with arrays until unless some special techniques are used. For example, to delete 1010 in id[], everything after 1010 has to be moved.

**Advantages over arrays**
**1)** Dynamic size
**2)** Ease of insertion/deletion

**Drawbacks:**
**1)** Random access is not allowed. We have to access elements sequentially starting from the first node. So we cannot do binary search with linked lists efficiently with its default implementation. Read about it [here](https://www.geeksforgeeks.org/binary-search-on-singly-linked-list/).
**2)** Extra memory space for a pointer is required with each element of the list.
**3)** Not cache friendly. Since array elements are contiguous locations, there is locality of reference which is not there in case of linked lists.

### 1.3 Representation:

A linked list is represented by a pointer to the first node of the linked list. The first node is called the head. If the linked list is empty, then the value of the head is NULL.
Each node in a list consists of at least two parts:
1) data
2) Pointer (Or Reference) to the next node
In C, we can represent a node using structures. Below is an example of a linked list node with integer data.
In Java or C#, LinkedList can be represented as a class and a Node as a separate class. The LinkedList class contains a reference of Node class type.

```java
class LinkedList {
    Node head; // head of the list
  
    /* Linked list Node*/
    class Node {
        int data;
        Node next;
  
        // Constructor to create a new node
        // Next is by default initialized
        // as null
        Node(int d) { data = d; }
    }
}

```

## 2 Build and Basic Operation

### 2.1 design/ get / add / delete

- [707. Design a List](https://leetcode.com/problems/design-linked-list/)

```java
// Singly Linked List way (we will talk about Doubly Linked way later)
class ListNode {
    int val;
    ListNode next;

    public ListNode(int val) {
        this.val = val;
    }
}

class MyLinkedList {
    int size;
    ListNode dummy;// is a sentinel
    /** Initialize your data structure here. */
    public MyLinkedList() {
        dummy = new ListNode(-1);
        size = 0;
    }
    
    /** Get the value of the index-th node in the linked list. If the index is invalid, return -1. */
    public int get(int index) {
        if (index < 0 || index >= size) return - 1;
        ListNode node = dummy;
        // 3 4 index = 1
        //i = 0  3 i= 1 4 
        for (int i = 0; i < index + 1; i++) {
            node = node.next;
        }
        return node.val;

    }
    
    /** Add a node of value val before the first element of the linked list. After the insertion, the new node will be the first node of the linked list. */
    public void addAtHead(int val) {
        addAtIndex(0, val);
    }
    
    /** Append a node of value val to the last element of the linked list. */
    public void addAtTail(int val) {
        addAtIndex(size, val);

    }
    
    /** Add a node of value val before the index-th node in the linked list. If index equals to the length of linked list, the node will be appended to the end of linked list. If index is greater than the length, the node will not be inserted. */
    public void addAtIndex(int index, int val) {
        if (index > size) return;
        if (index < 0) index = 0; // define by yourself;
        ListNode pre = dummy;
        for (int i = 0; i < index; i++) {
            pre = pre.next;
        }
        ListNode node = new ListNode(val);
        node.next = pre.next;
        pre.next = node;
        size++;
    }
    
    /** Delete the index-th node in the linked list, if the index is valid. */
    public void deleteAtIndex(int index) {
        if (index < 0 || index >= size) return;

        ListNode pre = dummy;
        for (int i = 0; i < index; i++) {
            pre = pre.next;
        }
        pre.next = pre.next.next;
        size--;
    }
}
```

- [203. 移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)
- [237. 删除链表中的节点](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/)

### 2.2 reverse /findMiddle/ isPalindrome/ isCycle / deleteDuplicate

- [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)
- [876. 链表的中间结点](https://leetcode-cn.com/problems/middle-of-the-linked-list/)
- [234. 回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)
- [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)
- [83. 删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

## 3 Advance Operation

###### 中等

- [92. 反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
- [143. 重排链表](https://leetcode-cn.com/problems/reorder-list/)
- [82. 删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
- [19. 删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)
- [148. 排序链表](https://leetcode-cn.com/problems/sort-list/)
- [86. 分隔链表](https://leetcode-cn.com/problems/partition-list/)
- [61. 旋转链表](https://leetcode-cn.com/problems/rotate-list/)
- [142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
- [147. 对链表进行插入排序](https://leetcode-cn.com/problems/insertion-sort-list/)
- [138. 复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)
- [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)
- [328. 奇偶链表](https://leetcode-cn.com/problems/odd-even-linked-list/)
- [707. 设计链表](https://leetcode-cn.com/problems/design-linked-list/)
- [109. 有序链表转换二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)
- [430. 扁平化多级双向链表](https://leetcode-cn.com/problems/flatten-a-multilevel-doubly-linked-list/)
- [725. 分隔链表](https://leetcode-cn.com/problems/split-linked-list-in-parts/)

###### 困难

- [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)





