
## Content
- [1 Principle](#1-principle)
  * [1.1 What is a singly linked list](#11-what-is-a-singly-linked-list)
  * [1.2 Why Linked List](#12-why-linked-list-)
  * [1.3 Representation](#13-representation-)
- [2 Build and Basic Operation](#2-build-and-basic-operation)
  * [2.1 design/ get / add / delete](#21-design--get---add---delete)
  * [2.2 reverse](#22-reverse)
  * [2.3 findMiddle](#23-findmiddle)
  * [2.4 isCycle](#24-iscycle)
  * [2.5 merge](#25-merge)
  * [2.6 isPalindrome](#26-ispalindrome---reverse-----findmiddle)
  * [2.7 sort](#27-sort)
    + [2.7.1 Merge Sort : findMiddle + merge](#271-merge-sort---findmiddle---merge)
    + [2.7.2 Insertion Sort](#272-insertion-sort)
  * [2.8 rotate](#28-rotate)
- [3 Advance Operation](#3-advance-operation)
  - [Easy](#easy)
  - [Middle](#middle)
  - [Hard](#hard)

## 1 Principle

### 1.1 What is a singly linked list

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

>  Notice: Determine `node != null` or `node.next ! = null` is important!

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

  

- [237. Delete Node in a Linked List](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/)

  - Tips: It is guaranteed that the node to be deleted is not a tail node in the list.

  ```java
  class Solution {
     //time&space: O(1)
     
      public void deleteNode(ListNode node) {
          node.val = node.next.val;
          node.next = node.next.next;
      }
  }
  ```

- [203. Remove Linked List Elements](https://leetcode-cn.com/problems/remove-linked-list-elements/)

  - Notice: just one pointer can delete node, because each time we delete nodes the next node will update, we don't need move the pre pointer.

  ```java
  class Solution {
    // time: O(n) space : O(1)
      public ListNode removeElements(ListNode head, int val) {
          ListNode dummy = new ListNode(0);
          dummy.next = head;
          ListNode pre = dummy;
          while(pre !=null && pre.next !=null){
                if(pre.next.val == val)
                pre.next = pre.next.next; // .next = .next.next upadates the next node
                else
                pre = pre.next;
            }
          return dummy.next;
      }
  }
  ```

  The process is like :

  ![just one pointer](https://trello-attachments.s3.amazonaws.com/6072d0d2385f2e81619166e2/60a4a737f0e02710dd8e4ef7/6ec51dc72110d6d4172c8ada1494a5b9/Screen_Shot_2021-05-19_at_3.29.22_PM.png)

- [83. Remove Duplicates from Sorted List](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

  ```java
  class Solution {
      public ListNode deleteDuplicates(ListNode head) {
          ListNode pre = head;
          while(pre !=null && pre.next !=null){
                if(pre.val == pre.next.val)
                pre.next = pre.next.next;
                else
                pre = pre.next;
            }
          return head;
      }
  }
  
  ```

  The process is like :

  ![just one pointer](https://trello-attachments.s3.amazonaws.com/6072d0d2385f2e81619166e2/60a4bbef24d7a20d51a3fc15/a8c3f4ae8cf33b52d5658dabb0303ce3/Screen_Shot_2021-05-19_at_3.29.56_PM.png)

- [82. Remove Duplicates from Sorted List II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode pre = dummy;
        while(pre.next != null && pre.next.next != null) {
            if (pre.next.val == pre.next.next.val) {
              	// use num record the node.val we need to delete
                int num = pre.next.val;
                while (pre.next != null && pre.next.val == num) {
                    pre.next = pre.next.next;
                }
            } else pre = pre.next; 
        }
        return dummy.next;
    }
}
```

![d](https://assets.leetcode.com/uploads/2021/01/04/linkedlist1.jpg)

### 2.2 reverse 

- [206. Reverse Linked List](https://leetcode-cn.com/problems/reverse-linked-list/)

  ```java
  class Solution {
    // way1: recursion
      public ListNode reverseList(ListNode head) {
           if (head == null || head.next == null) return head;
           ListNode newHead = reverseList(head.next);
           head.next.next= head;
           head.next = null;
           return newHead;
      }
    
     // way2: iteration
      public ListNode reverseList(ListNode head) {
          if (head == null || head.next == null) return head;
          ListNode pre = null;
          while(head != null) {
              ListNode temp = head.next;
              head.next = pre;
              pre = head;
              head = temp;
          }
          return pre; 
      }
  }
  ```

  

- [92. Reverse Linked List II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

  - Just move the last node to the next of pre one by one. (the cur and pre don't move)

  ```java
  class Solution {
      public ListNode reverseBetween(ListNode head, int left, int right) {
          ListNode dummy = new ListNode(0);
          dummy.next = head;
          ListNode cur = head;
          ListNode pre = dummy;
          for (int i = 0; i < left - 1; i++) {
              cur = cur.next;
              pre = pre.next;
          }
          for (int i = 0; i < right - left; i++) {
             // if the pointer is going to change, use(order) it first
              ListNode temp = cur.next; 
              cur.next = temp.next; //use temp.next 
              temp.next = pre.next; // change temp.next , use pre.next
              pre.next = temp; // change pre.next
          }
          return dummy.next; 
      }
  }
  ```

  The progress is like: 

  ![reverse one by one, the cur and pre don't move](https://trello-attachments.s3.amazonaws.com/6072d0d2385f2e81619166e2/607efe6375538954584c3428/575a1daf60bac4226554c4a6076ac232/Screen_Shot_2021-05-19_at_3.52.43_PM.png)

### 2.3 findMiddle

- [876. Middle of the Linked List](https://leetcode-cn.com/problems/middle-of-the-linked-list/)

  ```java
  class Solution {
      //[1,2,3,4,5,6] will return [4,5,6]
      //[1,2,3,4,5] will return [3,4,5]
      public ListNode middleNode(ListNode head) {
          ListNode fast = head;
          ListNode slow = head;
          while (fast != null && fast.next != null) {  // The while condition will determine where the slow pointer will stop.
              fast = fast.next.next;
              slow = slow.next;
          }
          return slow;
      }
  }
  // sometimes will use, like in LeetCode 148 Sort List
  class Solution {
      //[1,2,3,4,5,6] will return [3,4,5,6]
      //[1,2,3,4,5] will return [3,4,5]
      public ListNode middleNode(ListNode head) {
          ListNode fast = head;
          ListNode slow = head;
          while (fast.next != null && fast.next.next != null) {
              fast = fast.next.next;
              slow = slow.next;
          }
          return slow;
      }  
  
  ```

### 2.4 isCycle 

- [141. Linked List Cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

  ```java
  public class Solution {
      public boolean hasCycle(ListNode head) {
          if (head == null) return false;
          ListNode fast = head;
          ListNode slow = head;
          while (fast != null && fast.next != null) {
              fast = fast.next.next;
              slow = slow.next;
              if (fast == slow) return true;
          }
          return false;
      }
  }
  ```

- [142. Linked List Cycle II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

  ```java
  public class Solution {
      public ListNode detectCycle(ListNode head) {
          ListNode fast = head;
          ListNode slow = head;
          while (fast != null && fast.next != null) { // same~~
              fast = fast.next.next;
              slow = slow.next;
              /*use the pricinple : 2*(a+b)=(a+b+c+b) => a=c
                     a                 b
              head - - - - - - - -> P - - -> Q (fast == slow)
                                   /         \
                                  |           |
                                   \         /
                                      - - -
                                        c                  */
              if (fast == slow) {
                  ListNode slow2 = head;
                  while (slow2 != slow) {
                      slow2 = slow2.next;
                      slow = slow.next;
                  }
                  return slow;
              }
          }
          return null;
      }
  }
  ```

  

### 2.5 merge

- [21.Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

  ```java
  class Solution {
      public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
          if (l2 == null) return l1;
          if (l1 == null) return l2;
          if (l2.val >= l1.val) {
              l1.next = mergeTwoLists(l1.next, l2);
            	return l1;
          } else {
              l2.next = mergeTwoLists(l2.next, l1);
            	return l2;
          }
      }
  }
  ```

  ![q](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

  

- [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

  ```java
  class Solution {
      public ListNode mergeKLists(ListNode[] lists) {
          if (lists == null || lists.length == 0) return null;
          PriorityQueue<ListNode> queue = new PriorityQueue<>((a,b) -> (a.val - b.val));
          ListNode dummy = new ListNode(0);
          ListNode cur = dummy;
          for (ListNode list : lists) {
              if (list != null) {
                  queue.offer(list);
              }
          }
          while (!queue.isEmpty()) {
              cur.next = queue.poll();
              cur = cur.next;
              if (cur.next != null) queue.offer(cur.next);
          }
          return dummy.next;
      }
  }
  ```

  

### 2.6 isPalindrome : reverse  +  findMiddle

- [234. Palindrome Linked List](https://leetcode-cn.com/problems/palindrome-linked-list/)

  ```java
  class Solution {
      public boolean isPalindrome(ListNode head) {
          ListNode slow = getMiddle(head);
          ListNode right = reverse(slow.next);
          slow.next = null; //!!! Remember to cut off the connection, otherwise a loop will appear.
          ListNode left = head;
          while (left != null && right != null) {
              if (left.val != right.val) return false;
              right = right.next;
              left = left.next;
          }
          return true;
      }
      
      private ListNode getMiddle(ListNode head) {
          ListNode fast = head;
          ListNode slow = head;
          while (fast.next != null && fast.next.next != null ) {
              fast = fast.next.next;
              slow = slow.next;
          }
          return slow;
      } 
  
      private ListNode reverse(ListNode head) {
          if (head == null || head.next == null) return head;
          ListNode newHead = reverse(head.next);
          head.next.next = head;
          head.next = null;
          return newHead;
      }  
  }
  ```

  

### 2.7 sort 

#### 2.7.1 Merge Sort : findMiddle + merge

- [148. Sort List](https://leetcode-cn.com/problems/sort-list/)

  ```java
  class Solution {
      public ListNode sortList(ListNode head) { // equal to the conbination of helper() in merge sort question
          if (head == null || head.next == null) return head;
          ListNode middle = getMiddle(head);
          ListNode next = middle.next;
          middle.next = null;
          return merge(sortList(head), sortList(next));
      }
      
      private ListNode merge(ListNode l1, ListNode l2) {
          if (l2 == null) return l1;
          if (l1 == null) return l2;
          if (l2.val >= l1.val) {
              l1.next = merge(l1.next, l2);
              return l1;
          } else {
              l2.next = merge(l2.next, l1);
              return l2;
          }
      }
      
      private ListNode getMiddle(ListNode head) {
          ListNode fast = head;
          ListNode slow = head;
          while (fast.next != null && fast.next.next != null ) {
              fast = fast.next.next;
              slow = slow.next;
          }
          return slow;
      }  
  }
  ```

#### 2.7.2 Insertion Sort

- [147. Insertion Sort List](https://leetcode-cn.com/problems/insertion-sort-list/)

  > Use a new node temp to point each element we take, and put it in the right place. 

  ```java
  class Solution {
      public ListNode insertionSortList(ListNode head) {
          ListNode dummy = new ListNode(0);
          dummy.next = head;
          ListNode pre = dummy;
          ListNode cur = head;
          ListNode temp = null;
          while (cur != null && cur.next != null) {
              // if there's a smaller node, take it out
              if (cur.val > cur.next.val) {
                  temp = cur.next;
                  cur.next = temp.next;
                  pre = dummy;
                  // find the insert place
                  while (pre.next.val <= temp.val) pre = pre.next;
                  temp.next = pre.next;
                  pre.next = temp;
              } else cur = cur.next;
          }
          return dummy.next;
      }
  }
  ```

  

### 2.8 rotate

- [61. Rotate List](https://leetcode.com/problems/rotate-list/)

  > Make it to a cicle and move head to new head.

  ```java
  class Solution {
      public ListNode rotateRight(ListNode head, int k) {
          if (head == null) return head;
          ListNode last = head;
          int len = 1;
          while (last.next != null) {
              last = last.next;
              len++;
          }
          last.next = head;
          for (int i = 1; i < len -k % len; i++) {  // notice k can larger than len!!!!
              head = head.next;
          }
          ListNode res = head.next;
          head.next = null;
          return res;
      }
  }
  ```




## 3 Advance Operation

###### Easy

- [19. Remove Nth Node From End of List](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/) : delete + two pointers (fast and slow)

  ```java
  // one pass solution : two pointers
  class Solution {
      public ListNode removeNthFromEnd(ListNode head, int n) {
          ListNode dummy = new ListNode(0);
          dummy.next = head;
          ListNode fast = dummy;
          ListNode slow = dummy;
          for (int i = 0; i <= n; i++) {
              fast = fast.next;
          }
          while (fast != null) {
              fast = fast.next;
              slow = slow.next;
          }
          slow.next = slow.next.next;
          return dummy.next;
      }
  }
  ```

- [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

  ```java
   /*
   41845n561845
   561845n41845
   */
  public class Solution {
      public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
          if (headA == null || headB == null) return null;
          ListNode cur1 = headA;
          ListNode cur2 = headB;
          while (cur1 != cur2 ) { //Here cur != cur2 means not pointing to the same node instead of comparing value!!!
              if(cur1 == null) cur1=headB;
              else cur1=cur1.next;
              if(cur2==null) cur2=headA;
              else cur2=cur2.next;
          }
          return cur1;  
      }
  }
  ```

  ![i](https://assets.leetcode.com/uploads/2021/03/05/160_statement.png)

- [138. Copy List with Random Pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

  >  Deep copy

  ```java
  class Solution {
      public Node copyRandomList(Node head) {
          Node cur = head;
          HashMap<Node, Node> map = new HashMap<>();
          while (cur != null) {
              map.put(cur, new Node(cur.val));
              cur = cur.next;
          }
          cur = head;
          while (cur != null) {
              map.get(cur).next = map.get(cur.next);
              map.get(cur).random = map.get(cur.random);
              cur = cur.next;
          }
          return map.get(head);
      }
  }
  ```

  


###### Middle

- [143. Reorder List](https://leetcode-cn.com/problems/reorder-list/) :  findMiddle + reverse + merge

  ```java
  class Solution {
      public void reorderList(ListNode head) {
          ListNode slow = getMiddle(head);
          merge(head, reverse(slow.next));
          slow.next = null;  //!!! Remember to cut off the connection, otherwise a loop will appear.
      }
      
      private ListNode getMiddle(ListNode head) {
          ListNode fast = head;
          ListNode slow = head;
          while (fast != null && fast.next != null ) { // // 1234 just return 4 is ok
              fast = fast.next.next;
              slow = slow.next;
          }
          return slow;
      } 
      
      private ListNode reverse(ListNode head) {
          if (head == null || head.next == null) return head;
          ListNode newHead = reverse(head.next);
          head.next.next = head;
          head.next = null;
          return newHead;
      }
      
      private void merge(ListNode head1, ListNode head2) {
          if (head1 == null || head2 == null) return;
          ListNode cur1 = head1;
          ListNode cur2 = head2;
          while (cur1 != null && cur2 != null) { // 12 & 4 //123 & 4
              ListNode n1 = cur1.next;
              ListNode n2 = cur2.next;
              cur1.next = cur2;
              cur2.next = n1;
              cur1 = n1;
              cur2 = n2;
          }
      }
  }
  ```

  ​									![reorder](https://assets.leetcode.com/uploads/2021/03/04/reorder1linked-list.jpg)

  

- [86. Partition List](https://leetcode-cn.com/problems/partition-list/)

  ```java
  class Solution {
      public ListNode partition(ListNode head, int x) {
          ListNode dummy = new ListNode(0);
          dummy.next = head;
          ListNode pre = dummy;
          ListNode cur = dummy;
          while (pre != null && cur != null && cur.next != null) {
              if (cur.next.val >= x) {
                  while (cur.next != null && cur.next.val >= x) {
                      cur = cur.next;  
                  }
                  if(cur.next == null) break;
                  ListNode temp = cur.next;
                  cur.next = temp.next;
                  temp.next = pre.next;
                  pre.next = temp;
                  cur = temp;
                  pre = temp;
              } else { 
                  pre = pre.next;
                  cur = cur.next;
              }   
          }
          return dummy.next;
      }
  }
  ```

  ![progress](https://assets.leetcode.com/users/images/b76ef27a-a223-48b9-9fb2-f8c209c58a64_1621428526.5480938.png)

  More easy solution (use two new list to store the two part nodes):

  ```java
  class Solution {
      public ListNode partition(ListNode head, int x) {
          // before and after are the two pointers used to create the two list
          // before_head and after_head are used to save the heads of the two lists.
          // All of these are initialized with the dummy nodes created.
          ListNode before_head = new ListNode(0);
          ListNode before = before_head;
          ListNode after_head = new ListNode(0);
          ListNode after = after_head;
  
          while (head != null) {
              // If the original list node is lesser than the given x,
              // assign it to the before list.
              if (head.val < x) {
                  before.next = head;
                  before = before.next;
              } else {
                  // If the original list node is greater or equal to the given x,
                  // assign it to the after list.
                  after.next = head;
                  after = after.next;
              }
              // move ahead in the original list
              head = head.next;
          }
          // Last node of "after" list would also be ending node of the reformed list
          after.next = null;
          // Once all the nodes are correctly assigned to the two lists,
          // combine them to form a single list which would be returned.
          before.next = after_head.next;
          return before_head.next;
      }
  }
  ```

  Another solution is like insertion sort list, get the smaller node and put it in front of dummy then reverse the former part~ 

  

- [24. Swap Nodes in Pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

- > Classic reverse question --- use recursion

  ```java
  class Solution {
      public ListNode swapPairs(ListNode head) {
          if (head == null || head.next == null) {
              return head;
          }
          ListNode newHead = head.next;
          head.next = swapPairs(newHead.next);
          newHead.next = head;
          return newHead;
      }
  }
  ```

  

- [328. Odd Even Linked List](https://leetcode-cn.com/problems/odd-even-linked-list/)

  > Classic question

  ```java
  public class Solution {
      public ListNode oddEvenList(ListNode head) {
          if (head == null) return null;
          ListNode odd = head, even = head.next, evenHead = even;
          while (even != null && even.next != null) {
              odd.next = even.next;
              odd = odd.next;
              even.next = odd.next;
              even = even.next;
          }
          odd.next = evenHead;
          return head;
      }
  }
  ```

  

- [109. Convert Sorted List to Binary Search Tree](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)

- > Same as 108. Convert Sorted Array to Binary Search Tree

  ```java
  class Solution {
      public TreeNode sortedListToBST(ListNode head) {
          if (head == null) return null;
          return helper(head, null);
      }
      private TreeNode helper(ListNode head, ListNode tail) {
          if (head == tail) return null;
          ListNode slow = getMiddle(head, tail);
          TreeNode node = new TreeNode(slow.val);
          node.left = helper(head, slow);
          node.right = helper(slow.next, tail);
          return node;
      }
      private ListNode getMiddle(ListNode head, ListNode tail) {
          ListNode fast = head;
          ListNode slow = head;
          while (fast.next != tail && fast.next.next != tail ) {
              fast = fast.next.next;
              slow = slow.next;
          }
          return slow;
      }  
  }
  ```

  

- [430. Flatten a Multilevel Doubly Linked List](https://leetcode-cn.com/problems/flatten-a-multilevel-doubly-linked-list/)

  > Realization problem 

  ```java
  /*
  // Definition for a Node.
  class Node {
      public int val;
      public Node prev;
      public Node next;
      public Node child;
  };
  */
  class Solution {
      public Node flatten(Node head) {
          Node node = head;
          while (node != null) {
              if (node.child != null) {
                  Node right = node.next;// save right to a node
                  // call recursively for flatten child
                  node.next = flatten(node.child);
                  node.next.prev = node;  
                	node.child = null;  // remember to set node.child = null
                  while (node.next != null) {
                      node = node.next;  // go to the last node of the left part
                  }
                  if (right != null) {  // then connect left part with right part
                      node.next = right;
                      right.prev = node;
                  }
              }
              node = node.next;
          }
          return head;
      }
  }
  /*
  Input ->
  1---2---3---4---5---6--NULL
                |
               7---8---9---10--NULL
                      |
                    11--12--NULL
  
  Output ->   1 -> 2 -> 3 -> 7 -> 8 -> 11 -> 12 -> 9 -> 10 -> 4 -> 5 -> 6
     */
  ```

  

- [725. Split Linked List in Parts](https://leetcode-cn.com/problems/split-linked-list-in-parts/)

  > Realization problem 

  ```java
  class Solution {
      public ListNode[] splitListToParts(ListNode root, int k) {
          ListNode[] res = new ListNode[k];
          if (root == null || k == 0) {
              return res;
          }
          
          int length = getLength(root);
          int part = length / k;
          int remainder = length % k;
          
          int[] nums = new int[k];
          for (int i = 0; i < nums.length; i++) {
              nums[i] = part;
              if (remainder > 0) {
                  nums[i]++;
                  remainder--;
              }
          }
          
          for (int i = 0; i < k; i++) {
              if (root == null) {  
                  break;  
              }
              res[i] = root;
              int count = 1;
              while (count < nums[i]) {
                  root = root.next;
                  count++;
              }
              ListNode next = root.next;
              root.next = null;
              root = next;
          }
          return res;
      }
      
      private int getLength(ListNode head) {
          int len = 0;
          while(head != null) {
              head = head.next;
              len++;
          }
          return len;
      }
  }
      
  /*About the result in example1: [1, 2, 3], k = 5
                                  Output: [[1],[2],[3],[],[]]
     We don't need to realize the [], just do nothing --- break;
     res[i] = null in nature.
                                  */
  ```

  

###### Hard

- [25. Reverse Nodes in k-Group](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

  > Same as 24. Swap Nodes in Pairs  --- use recursion

  ```java
  class Solution {
      public ListNode reverseKGroup(ListNode head, int k) {
          int count = k;
          ListNode pre = head;
          while (pre != null && count > 0) {
              pre = pre.next;
              count--;
          }
          if (count == 0) {
              pre = reverseKGroup(pre, k);
              while (count++ < k) {
                  ListNode temp = head.next;
                  head.next = pre;
                  pre = head;
                  head = temp;
              }
              head = pre;
          }
          return head;
      }
  }
  ```

  





