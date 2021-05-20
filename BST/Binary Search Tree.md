# Binary Search Tree

## Content



  * [1 Principle](#1-principle)
    + [1.1 What is a singly linked list](#11-what-is-a-singly-linked-list)
    + [1.2 Pros and Use cases](#12-pros-and-use-cases-)
    + [1.3 Traversal Way](#13-traversal-way-)
  * [2 Build and Basic Operation](#2-build-and-basic-operation)
    + [2.1 Build](#21-build)
    + [2.2 Search / Insert / Delete](#22-search---insert---delete)
  * [3 Other Operations](#3-other-operations--traversal-way-)
	  + [Easy](#easy)
	  + [Middle](#middle)
	  + [Hard](#hard)

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

### 1.2 Pros and Use cases: 

- The order of nodes in a BST means that each comparison skips about half of the remaining tree, so the whole lookup takes [time proportional to](https://en.wikipedia.org/wiki/Time_complexity) the [binary logarithm](https://en.wikipedia.org/wiki/Binary_logarithm) of the number of items stored in the tree. This is much better than the [linear time](https://en.wikipedia.org/wiki/Linear_time) required to find items by key in an (unsorted) array. (But slower than the corresponding operations on [hash tables](https://en.wikipedia.org/wiki/Hash_table).)
- Binary search trees allow [binary search](https://en.wikipedia.org/wiki/Binary_search_algorithm) for fast lookup, addition and removal of data items, and can be used to implement [dynamic sets](https://en.wikipedia.org/wiki/Set_(abstract_data_type)) and [lookup tables](https://en.wikipedia.org/wiki/Lookup_table). 

### 1.3 Traversal Way:

- If we want to print the BST in ascending order or find the Kth Largest node in BST, we must use inorder way to traverse;
- If we want to determine whether the tree is valid or find a given node, we can just use (doubly) preorder.

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

- [700. Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/)

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

- Ps:  What is what is successor and processor in BST inorder traverse?

  - successor : In Binary Tree, Inorder **successor** of a **node** is the next **node** in Inorder traversal of the Binary Tree. In Binary Search Tree, Inorder **successor** of an input **node** can also be defined as the **node** with the smallest key greater than the key of the input **node**.

    ![s](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/images/Screen%20Shot%202021-05-20%20at%204.07.27%20PM.png)

  - predecessor:  In Binary Tree, Inorder **predecessor** of a **node** is the last **node** in Inorder traversal of the Binary Tree. In Binary Search Tree, Inorder **predecessor** of an input **node** can also be defined as the **node** with the largest key smaller than the key of the input **node**.

    ![p](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/images/Screen%20Shot%202021-05-20%20at%204.07.32%20PM.png)

  

**Time Complexity:** The worst case time complexity of Search / Insertion / Delete operation is O(h) where h is the height of the Binary Search Tree. In worst case, we may have to travel from the root to the deepest leaf node. The height of a skewed tree may become n and the time complexity of the operations may become O(n).



## 3 Other Operations (Traversal Way)

###### Easy

- [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) 

  > The [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).”

  ```java
  // Same as 236 but much easier since it is a BST question.
  // Recursion
  class Solution {
      public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {  
          if(root.val < p.val && root.val < q.val) return lowestCommonAncestor(root.right, p, q)
          if(root.val > p.val && root.val > q.val) return lowestCommonAncestor(root.left, p, q);
          return root;
        
      }
  }
  
  // Iteration
  class Solution {
      public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
          TreeNode ancestor = root;
          while (true) {
              if (p.val < ancestor.val && q.val < ancestor.val) {
                  ancestor = ancestor.left;
              } else if (p.val > ancestor.val && q.val > ancestor.val) {
                  ancestor = ancestor.right;
              } else {
                  break;
              }
          }
          return ancestor;
      }
  }
  ```

  

###### Middle

- [98. Validate Binary Search Tree](https://leetcode-cn.com/problems/validate-binary-search-tree/) (doubly preoder)

  > Tips: We use `root.val` to update the values of `min` and `max` from top to bottom,  then we can just determine whether it is valid (go left to update min, go right to update max).

  ```java
  //Preorder 
  class Solution {
      public boolean isValidBST(TreeNode root) { 
         // edge case
          if (root == null) return true;
          return helper(root, null, null);
      }
      
      private boolean helper(TreeNode root, Integer min, Integer max) {
         // base case
          if (root == null) return true;
          // Judging conditions : notice that '=' is also false!!
          if (max != null && root.val >= max) return false;
          if (min != null && root.val <= min) return false;
          return (helper(root.left,min,root.val) && helper(root.right,root.val,max)); 
      }
  }
  ```

  

- [96. Unique Binary Search Trees](https://leetcode-cn.com/problems/unique-binary-search-trees/) 

  ```java
  //dp
  class Solution {
      public int numTrees(int n) {
          int[] res = new int[n+1];  // usually we need another [0] to calculate the polynomial
          res[0] = 1;
     
          //res[1] = res[0]*res[0]
          //res[2] = res[0]*res[1] + res[1]*res[0]
          //res[3] = res[0]*res[2] + res[1]*res[1] + res[2]*res[0] 
          //res[n] = res[0]*res[n-1] +  res[1]*res[n-2] + .... +  res[n-1]*res[0]
          for (int i = 1; i <= n; i++) {
              for (int j = 0; j < i; j++) {
                  res[i] += res[j] * res[i - j -1];
              }
          }
          return res[n];
      }
  }
  
  ```

  

- [95. Unique Binary Search Trees II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/) 

  > [Backtracking]() : talking in other chapter

  ```java
  class Solution {
      public List<TreeNode> generateTrees(int n) {
          List<TreeNode> res = constructTrees(1, n);
          return res;   
      }
      public List<TreeNode> constructTrees(int start, int end){
          List<TreeNode> list = new ArrayList<>();
          //  if start > end   then subtree will be empty so returning NULL in the list
          if (start > end) { 
              list.add(null); 
          } 
      
          // iterate all values from start to end for constructing left and right subtree using recursion
          for (int i = start; i <= end; i++) { 
              List<TreeNode> leftSubtree  = constructTrees(start, i - 1); // constructing left subtree 
              List<TreeNode> rightSubtree = constructTrees(i + 1, end); // constructing right subtree
              // now loop through all left and right subtrees and connecting them to ith root
              for (TreeNode left : leftSubtree) { 
                  for (TreeNode right : rightSubtree) { 
                      TreeNode node = new TreeNode(i);        // making value i as root 
                      node.left = left;                       // connect left subtree 
                      node.right = right;                     // connect right subtree 
                      list.add(node);                         // add this tree to list 
                  } 
              } 
          } 
          return list; 
      }
  }
  ```

  ![img](https://assets.leetcode.com/uploads/2021/01/18/uniquebstn3.jpg)

  

- [173. Binary Search Tree Iterator](https://leetcode-cn.com/problems/binary-search-tree-iterator/) (inorder)

  ```java
  // Use queue instead of res list. (List can also be used with index.)
  class BSTIterator {
      Queue<Integer> queue = new LinkedList<>();
      public BSTIterator(TreeNode root) {
          inorderTraversal(root);
      } 
      // equal to the helper()
      private void inorderTraversal(TreeNode root) {
          if (root == null) {
              return;
          }
          inorderTraversal(root.left);
          queue.offer(root.val);
          inorderTraversal(root.right);
      }
      public int next() {
          return queue.poll();
      }
      public boolean hasNext() {
          return !queue.isEmpty();
      }  
  }
  
  ```

  

- [230. Kth Smallest Element in a BST](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/) (inorder)

  > Tips : Global variable 
  >
  > 1. If the helper has a void return but you want to send the result, just add a global variable --- int res;
  >
  > 2. If count changes every recursion and is not a value that needs to be backtracked, just add a global variable --- int count;

  ```java
  class Solution {
      int res;
      int count;
      public int kthSmallest(TreeNode root, int k) {
          count = k;
          helper(root);
          return res;  
      }
      private void helper(TreeNode root) {
          if (root == null) return;
          helper(root.left);
          count--;
          if (count == 0) res = root.val;
          helper(root.right);
      }
  }
  ​```
  ```

  

###### Hard

- [99. Recover Binary Search Tree](https://leetcode-cn.com/problems/recover-binary-search-tree/) (inorder)

  > Tips: Regard the traversal of tree as a traversal of an appointed order array, then use two pointers to compare each value.
  >
  > Same as 114, use two pointers to restructure the tree with space O(1).

  ```java
  /*
  This question has two conditons:
  1  [5]  3  4  [2]  6
  pre.val > root.val (cur.val) Judge twice
  first = pre
  second = root
  ----
  1 2 3 4 [6] [5]
  pre.val > root.val (cur.val) Judge once
  first = pre
  second = root
  */
  
  class Solution {
      TreeNode first;
      TreeNode second;
      TreeNode pre;
      public void recoverTree(TreeNode root) {
          if (root == null) return;
          helper(root);
          int temp = first.val;
          first.val = second.val;
          second.val = temp;
      }
      
      private void helper(TreeNode root) {
          if (root == null) return;
          helper(root.left);
          if (pre != null && pre.val >= root.val) {
              if (first == null)  first = pre;
              second = root; 
          }
          pre = root;
          helper(root.right);
      }
  }
  
  ```

  

