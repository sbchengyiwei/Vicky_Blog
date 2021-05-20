# Binary Search Tree

## Content



## 1 Principle

### 1.1 What is a singly linked list

The following definition is from Wikipedia:

>Defination: In [computer science](https://en.wikipedia.org/wiki/Computer_science), a **binary search tree** (**BST**), also called an **ordered** or **sorted binary tree**, is a [rooted](https://en.wikipedia.org/wiki/Rooted_tree) [binary tree](https://en.wikipedia.org/wiki/Binary_tree) whose internal nodes each store a key greater than all the keys in the node's left subtree and less than those in its right subtree. A binary tree is a type of [data structure](https://en.wikipedia.org/wiki/Data_structure) for storing data such as numbers in an organized way.

This picture better show the BST structure:

![i](https://media.geeksforgeeks.org/wp-content/uploads/BSTSearch.png)

**Binary Search Tree** is a node-based binary tree data structure which has the following properties:

- The left subtree of a node contains only nodes with keys lesser than the node’s key.
- The right subtree of a node contains only nodes with keys greater than the node’s key.
- The left and right subtree each must also be a binary search tree.

### 1.2 Pros and Use case: 

- The order of nodes in a BST means that each comparison skips about half of the remaining tree, so the whole lookup takes [time proportional to](https://en.wikipedia.org/wiki/Time_complexity) the [binary logarithm](https://en.wikipedia.org/wiki/Binary_logarithm) of the number of items stored in the tree. This is much better than the [linear time](https://en.wikipedia.org/wiki/Linear_time) required to find items by key in an (unsorted) array. (But slower than the corresponding operations on [hash tables](https://en.wikipedia.org/wiki/Hash_table).)

- Binary search trees allow [binary search](https://en.wikipedia.org/wiki/Binary_search_algorithm) for fast lookup, addition and removal of data items, and can be used to implement [dynamic sets](https://en.wikipedia.org/wiki/Set_(abstract_data_type)) and [lookup tables](https://en.wikipedia.org/wiki/Lookup_table). 

## 2 Build and Basic Operation 

### 2.1 Build

-  [108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

  ```java
  class Solution {
      public TreeNode sortedArrayToBST(int[] nums) {
          if (nums == null || nums.length == 0) return null;
          return helper(nums, 0, nums.length - 1);
      }
      private TreeNode helper(int[] nums, int left, int right) {
          if (left > right) return null;
          int mid = (right - left) / 2 + left;
          TreeNode node = new TreeNode(nums[mid]);
          node.left = helper(nums, left, mid - 1);
          node.right = helper(nums, mid + 1, right);
          return node;
      }
  }
  ```

- [109. Convert Sorted List to Binary Search Tree](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/)

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

  

### 2.2 Search / Insert / Delete

**Searching a key** 

1. Start from the root. 
2. Compare the searching element with root, if less than root, then recurse for left, else recurse for right. 
3. If the element to search is found anywhere, return true, else return false. 

- 700 二叉搜索树中的搜索

```java
// A utility function to search a given key in BST
class Solution {
  public TreeNode searchBST(TreeNode root, int val) {
    // Base Cases: root is null or key is present at root
    if (root == null || val == root.val) return root;
    // Key is greater or smaller than root's key
    return val < root.val ? searchBST(root.left, val) : searchBST(root.right, val); 
  }
}
```

**Insertion of a key** 
	A new key is always inserted at the leaf. We start searching a key from the root until we hit a leaf node. Once a leaf node is found, the new node is added as a child of the leaf node. 

```
         100                               100
        /   \        Insert 40            /    \
      20     500    --------->          20     500 
     /  \                              /  \  
    10   30                           10   30
                                              \   
                                               40
```

- [701. Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/)

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        // edge case
        if (root == null) return new TreeNode(val);
        TreeNode node = search(root, val);
        if (node.val > val) node.left = new TreeNode(val);
        if (node.val < val) node.right = new TreeNode(val);
        return root;
 
    }
    public TreeNode search(TreeNode root, int val) {
        //base case
        if (root.left == null && root.val > val) return root;
        if (root.right == null && root.val < val) return root;
        return val < root.val ? search(root.left, val) : search(root.right, val);
    }
}
```

**Deleting a key**

There are 3 situations：

​	**1)** ***Node to be deleted is the*** ***leaf:*** Simply remove from the tree. 

```
              50                            50
           /     \         delete(20)      /   \
          30      70       --------->    30     70 
         /  \    /  \                     \    /  \ 
       20   40  60   80                   40  60   80
```

​	**2)** ***Node to be deleted has only one child:*** Copy the child to the node and delete the child 

```
              50                            50
           /     \         delete(30)      /   \
          30      70       --------->    40     70 
            \    /  \                          /  \ 
            40  60   80                       60   80
```

​	**3)** ***Node to be deleted has two children:*** Find inorder successor of the node. Copy contents of the inorder successor to the node and delete the inorder successor. Note that inorder predecessor can also be used. 
 

```
              50                            60
           /     \         delete(50)      /   \
          40      70       --------->    40    70 
                 /  \                            \ 
                60   80                           80
```

​	The important thing to note is, inorder successor is needed only when the right child is not empty. In this particular case, inorder successor can be obtained by finding the minimum value in the right child of the node.

- [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)

  ```java
  //implementation of the upper process 
  class Solution {
      private static TreeNode findMinimum(TreeNode root){
         // The leftmost node is the smallest node.
          while(root.left!=null){
              root = root.left;
          }
          return (root);
      }
      
      public TreeNode deleteNode(TreeNode root, int key) {
          if(root==null){
              return null;
          }
          else if(root.val>key){
              root.left= deleteNode(root.left,key);
          }
          else if(root.val<key) {
              root.right= deleteNode(root.right,key);
          }
          else{
              //If root has no child
              if(root.right==null && root.left==null){
                  return null;
              }
  			//If root has one right child
              else if(root.left==null){
                  return root.right;
              }
  			//If root has one left child
              else if(root.right==null){
                  return root.left;
              }
  			//If root has 2 children
              else{
                  TreeNode min = findMinimum(root.right);
                  root.val=min.val;
                  root.right=deleteNode(root.right,min.val);
                  return root;
              }
      }
      return root;
    }
  }
  
  ```

  Ps:  What is what is successor and processor in BST inorder traverse?

  - successor : In Binary Tree, Inorder **successor** of a **node** is the next **node** in Inorder traversal of the Binary Tree. In Binary Search Tree, Inorder **successor** of an input **node** can also be defined as the **node** with the smallest key greater than the key of the input **node**.

  - predecessor:  In Binary Tree, Inorder **predecessor** of a **node** is the last **node** in Inorder traversal of the Binary Tree. In Binary Search Tree, Inorder **predecessor** of an input **node** can also be defined as the **node** with the largest key smaller than the key of the input **node**.

    

  ![i](https://trello-attachments.s3.amazonaws.com/6072d0d2385f2e81619166e2/60a1f1a4e4677279638b4c62/8339f466bd76d991109fc88aa9a672cc/Screen_Shot_2021-05-17_at_3.03.50_PM.png)



**Time Complexity:** The worst case time complexity of Search / Insertion / Delete operation is O(h) where h is the height of the Binary Search Tree. In worst case, we may have to travel from the root to the deepest leaf node. The height of a skewed tree may become n and the time complexity of the operations may become O(n).



### 2.3 Traverse

###### 简单

- [145. 二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)
- [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

###### 中等

- [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
- [102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
- [103. 二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
- [107. 二叉树的层序遍历 II](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)
- [106. 从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
- [144. 二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

