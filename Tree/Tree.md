1.traversal ✔️
2.construction ✔️
3.path 
4.depth
5.reverse

### Construction 

- [108.Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)  [also can be put in the BST topic]

  ```java
  class Solution {
      public TreeNode sortedArrayToBST(int[] nums) {
          if (nums == null) return null;
          return dfs(nums, 0, nums.length - 1);
      }
  
      public TreeNode dfs(int[] nums, int left, int right) {
          if (left > right) return null;
          
          // set the number in the middle as the root
          int mid = left + (right - left) / 2;
          TreeNode root = new TreeNode(nums[mid]);
          root.left = dfs(nums, left, mid - 1);
          root.right = dfs(nums, mid + 1, right);
          return root;
      }
  }
  ```

  

- [1008. Construct Binary Search Tree from Preorder Traversal](https://leetcode.com/problems/construct-binary-search-tree-from-preorder-traversal/) (2 ways)

  ```java
  // monotonic stack keeps the timeO(n)
  class Solution {
      public TreeNode bstFromPreorder(int[] preorder) {
          if (preorder == null) return null;
          Stack<TreeNode> stack = new Stack<>();
          TreeNode root = new TreeNode(preorder[0]);
          stack.push(root);
  
          for (int i = 1; i < preorder.length; i++) {
              TreeNode node = new TreeNode(preorder[i]);  
              if (preorder[i] < stack.peek().val) {
                  stack.peek().left  = node;  // ! node 
              } else {
                  TreeNode pre = stack.peek();
                  while (!stack.isEmpty() && preorder[i] > stack.peek().val) {
                      pre = stack.pop();  
                  }
                  pre.right = node;
              }
              stack.push(node);  // we must put the node we insert to root into the stack, not a new TreeNode without any link.
          }
          return root;
          
      }
  }
  
  
  //dfs traverse and insert one by one;
  class Solution {
      private TreeNode root;
      public TreeNode bstFromPreorder(int[] preorder) {
          root = new TreeNode(preorder[0]);
          for (int i = 1; i < preorder.length; ++i) insert(preorder[i], root);
          return root;
      }
  
      private void insert(int val, TreeNode root) {
          if (val < root.val) {
              if (root.left == null) root.left = new TreeNode(val);
              else insert(val, root.left);
          } else {
              if (root.right == null) root.right = new TreeNode(val);
              else insert(val, root.right);
          }
      }
  }
  
  // normal left part is smaller than root, so just partion the left array into two parts
  class Solution {
      public TreeNode bstFromPreorder(int[] preorder) {
          if (preorder == null || preorder.length == 0) return null;
          return dfs(preorder, 0, preorder.length - 1);
      }
      private TreeNode dfs(int[] preorder, int left, int right) {
          if (left > right) return null;
          TreeNode root = new TreeNode(preorder[left]);
  				// use binary search find the partition place
          int l = left;
          int r = right;
          while (l < r) {
              int mid = l + (r - l + 1) / 2;
              if (preorder[mid] < preorder[left]) {
                  l = mid;
              } else {
                  r = mid - 1;
              }
          }
          root.left = dfs(preorder, left + 1, l);
          root.right = dfs(preorder, l + 1, right);
          return root;
      }
  }
  
  ```

- [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

  ```java
  // ! the right part start from the (preStart + i - inStart + 1) item in the preoder!
  class Solution {
      public TreeNode buildTree(int[] preorder, int[] inorder) {
          if (preorder == null || preorder.length == 0) return null;
          return helper(0,0,inorder.length - 1, preorder, inorder);
          
      }
  
      private TreeNode helper(int preStart, int inStart, int inEnd, int[] preorder, int[] inorder){
          if (inStart > inEnd) return null;
          TreeNode root = new TreeNode(preorder[preStart]);
  
          for (int i = inStart; i <= inEnd; i++) {
              if (inorder[i] == root.val) {
                  root.left = helper(preStart + 1, inStart, i - 1, preorder, inorder);
                  root.right = helper(preStart + i - inStart + 1, i + 1, inEnd, preorder, inorder); // ! the right part start from the (preStart + i - inStart + 1) item in the preoder!
                  break;
              }
          }
          return root;
      }
  }
  ```

- [114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

  ```java
  class Solution {
      //postpost order dfs
      TreeNode pre = null;
      public void flatten(TreeNode root) {
          if (root == null) return;
          flatten(root.right);
          flatten(root.left);
          root.left = null;
          root.right = pre;
          pre = root;
      }
  }
  ```

- [99. Recover Binary Search Tree](https://leetcode.com/problems/recover-binary-search-tree/)

  ```java
  class Solution {
      // inorder dfs
      TreeNode pre;
      TreeNode first;
      TreeNode second;
      public void recoverTree(TreeNode root) {
          if (root == null) return;
          dfs(root);
          // reverse the val !  otherwise the tree no change!
          int temp = first.val;
          first.val = second.val;
          second.val = temp;
      }
      private void dfs(TreeNode root) {
          if (root == null) return;
          dfs(root.left);
          if (pre != null && pre.val > root.val) {
              if (first == null) first = pre;
              second = root;
          }
          pre = root;
          dfs(root.right);
      }
  }
  ```

- [297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

  ```java
  public class Codec {
      // Encodes a tree to a single string.
      public String serialize(TreeNode root) {
          if (root == null) return "";
          StringBuilder res = new StringBuilder();
          Queue<TreeNode> queue = new LinkedList<>();
          queue.offer(root);
          while (!queue.isEmpty()) { // 其实只要不是需要把每个level 明确划分的话都可以不用加 size
              TreeNode cur = queue.poll();
              if (cur == null) {
                  res.append("null ");
                  continue;
              }
              res.append(cur.val + " ");
              //  上面的 continue 保证 cur 一定不是 null 所以可以把cur.left和 cur.right 加进去
              queue.offer(cur.left);
              queue.offer(cur.right);
          }
          return res.toString();
      }
  
      // Decodes your encoded data to tree.
      public TreeNode deserialize(String data) {
          if (data.equals("")) return null;
          String[] str = data.split(" ");
          TreeNode root = new TreeNode(Integer.parseInt(str[0]));
          Queue<TreeNode> queue = new LinkedList<>();
          queue.offer(root);
          for (int i = 1; i < str.length; i++) {
              TreeNode cur = queue.poll();
              if (!str[i].equals("null")) {
                  cur.left = new TreeNode(Integer.parseInt(str[i]));
                  queue.offer(cur.left);
              }
              i++;
              if (!str[i].equals("null")) {
                  cur.right = new TreeNode(Integer.parseInt(str[i]));
                  queue.offer(cur.right);
              }
          }
          return root;
     }
  }
  ```

  

