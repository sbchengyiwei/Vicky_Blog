1.traversal ✔️ 
**2.construction** ✔️
**3.path**  ✔️
**4.depth / breadth** ✔️
5.reverse ✔️ 6.LCA ✔️



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
          return dfs(0,0,inorder.length - 1, preorder, inorder);
          
      }
  
      private TreeNode dfs(int preStart, int inStart, int inEnd, int[] preorder, int[] inorder){
          if (inStart > inEnd) return null;
          TreeNode root = new TreeNode(preorder[preStart]);
  
          for (int i = inStart; i <= inEnd; i++) {
              if (inorder[i] == root.val) {
                  root.left = dfs(preStart + 1, inStart, i - 1, preorder, inorder);
                  root.right = dfs(preStart + i - inStart + 1, i + 1, inEnd, preorder, inorder); // ! the right part start from the (preStart + i - inStart + 1) item in the preoder!
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
          Queue<TreeNode> queue = new LinkedList<>();//!LinkedList才能加null；ArrayDeque 不行
          queue.offer(root);
          StringBuilder s = new StringBuilder();
          while(!queue.isEmpty()) {
              TreeNode cur = queue.poll();
              if (cur != null) { 
                    s.append(cur.val + " ");
                  queue.offer(cur.left);
                  queue.offer(cur.right);  
              } else {
                s.append("null ");
              }
          }
          return s.toString();
      }
  
      // Decodes your encoded data to tree.
      public TreeNode deserialize(String data) {
          String[] str = data.split(" ");
          Queue<TreeNode> queue = new ArrayDeque<>();
          TreeNode root = new TreeNode(Integer.parseInt(str[0]));
          queue.offer(root);
     for (int i = 1; i < str.length; i++) {
              TreeNode cur = queue.poll();
              if (!str[i].equals("null")) {
                  cur.left = new TreeNode(Integer.parseInt(str[i]));
                  queue.offer(cur.left);
              }
              i++;
              if (i < str.length && !str[i].equals("null")) {
                  cur.right = new TreeNode(Integer.parseInt(str[i]));
                  queue.offer(cur.right);
              }
          }
          return root;
      }
  }
  
  ```

  



### Path ：

1. from the root to the leaf

2. start or end in the middle

3. span aross two branches

- [112. Path Sum](https://leetcode-cn.com/problems/path-sum/)

  ```java
  //Input: root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22
  //Output: true
  
  class Solution {
      public boolean hasPathSum(TreeNode root, int targetSum) {
          if (root == null) return false;
          if (root.right == null && root.left == null)  return root.val == targetSum;
          return hasPathSum(root.left, targetSum - root.val) || hasPathSum(root.right, targetSum - root.val);
      }
  }
  ```

- [113. Path Sum II](https://leetcode-cn.com/problems/path-sum-ii/)

  ```java
  //Input: root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
  //Output: [[5,4,11,2],[5,8,4,5]]
  class Solution {
      public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
          
          List<List<Integer>> res = new ArrayList<>();
          if (root == null) return res;
          dfs(root, res, targetSum, new ArrayList<>());
          return res;
      }
  
      private void dfs(TreeNode root, List<List<Integer>> res, int targetSum, List<Integer> list) {
          if (root.right == null && root.left == null)  {
              if (root.val == targetSum) {
                  list.add(root.val);
                  res.add(new ArrayList<>(list));
                  list.remove(list.size() - 1);
                  return;
              }
          }
          if (root.left != null) {
              list.add(root.val);
              dfs(root.left, res, targetSum - root.val, list);
              list.remove(list.size() - 1);
          }
          if (root.right != null) {
              list.add(root.val);
              dfs(root.right, res, targetSum - root.val, list);
              list.remove(list.size() - 1);
          }
      }
  }
  ```

- [**437. Path Sum III**](https://leetcode-cn.com/problems/path-sum-iii/)

  ```java
  //Input: root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
  //Output: 3
  class Solution {
    //n * logn (本来是 logn 但是由于可以不从头开始 又要乘 n) nlogn 同理
      public int pathSum(TreeNode root, int targetSum) {
          if (root == null) return 0;
          return dfs(root, targetSum) + pathSum(root.left, targetSum) + pathSum(root.right, targetSum);
      }
  
      private int dfs(TreeNode root, int targetSum) {
          if (root == null) return 0; // 走到底部都没满足 return 0 就好
  
          int res = 0;
          if (targetSum == root.val) res += 1; //走到中间就满足了 但是因为有负数所以还要继续
        
          res += dfs(root.left, targetSum - root.val);
          res += dfs(root.right, targetSum - root.val);
          return res;
      }
  }
  ```

  **降低时间复杂度：使用 hashmap + 回溯法**

  ```java
  class Solution {
      //Time :O(n) Space :O(n)
      public int pathSum(TreeNode root, int targetSum) {
          HashMap<Integer, Integer> sum2num = new HashMap<>();
          sum2num.put(0, 1);
          return dfs(root, sum2num, targetSum, 0);
      }
  
      private int dfs(TreeNode root, HashMap<Integer, Integer> sum2num, int target, int presum) {
          if (root == null) return 0;
          presum += root.val;
          int count = 0;
          if (sum2num.containsKey(presum - target)) count += sum2num.get(presum - target);
          sum2num.put(presum, sum2num.getOrDefault(presum, 0) + 1);
          count += dfs(root.left, sum2num, target, presum);
          count += dfs(root.right,sum2num, target, presum);
          sum2num.put(presum,sum2num.get(presum) - 1);
          return count;
      }
  }
  ```

  

  ![img](https://assets.leetcode.com/uploads/2021/04/09/pathsum3-1-tree.jpg)

- [124. Binary Tree Maximum Path Sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

  ```java
  class Solution {
      int res = Integer.MIN_VALUE;  // since we have negative node so the res can be negative, we can not initialize res with 0
      public int maxPathSum(TreeNode root) {
          dfs(root);
          return res;
      }
      private int dfs(TreeNode root){
          if (root == null) return 0;
          int left = Math.max(0,dfs(root.left));
          int right = Math.max(0,dfs(root.right));
          res = Math.max(res, left + right + root.val);
          return Math.max(left, right) + root.val; 
      }
  }
  ```

  

### Depth / Breadth

- [662. Maximum Width of Binary Tree](https://leetcode-cn.com/problems/maximum-width-of-binary-tree/)

  ```java
  class Node {
      int pos;
      TreeNode node;
  
      public Node(TreeNode node, int pos) {
          this.node = node;
          this.pos = pos;  // each level's postion is different, use the last pos - first pos to get the breadth
      }
  }
  class Solution {
      public int widthOfBinaryTree(TreeNode root) {
          if (root == null) {
              return 0;
          }
          LinkedList<Node> deque = new LinkedList<>();
          Node node = new Node(root, 0);
          deque.add(node);
          int res = 0;
          while (!deque.isEmpty()) {
              int size = deque.size();
              res = Math.max(res, deque.getLast().pos - deque.getFirst().pos + 1);  //
              for (int i = 0; i < size; i++){
                  Node cur = deque.poll();
                  if (cur.node.left != null) {
                      deque.add(new Node(cur.node.left, cur.pos * 2 + 1));
                  }
                  if (cur.node.right != null) {
                      deque.add(new Node(cur.node.right, cur.pos * 2 + 2));
                  } 
              }
          }
          return res;
      }
  }
  ```

  

### LCA

- [236. Lowest Common Ancestor of a Binary Tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

  ```java
  class Solution {
      public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
          if (root == null || root == p || root == q) return root;
          TreeNode left = lowestCommonAncestor(root.left, p, q);
          TreeNode right = lowestCommonAncestor(root.right, p, q);
          if (left != null && right != null) return root;
          return left == null ? right : left;
      }
  }
  ```

  

follow up :  如果 a b 可能不存在的 那么在原函数加一个都空返回空 还需要一个函数 

slu  = lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q)  

if sul ==  p / q//说明 pq 直接隶属

​		return lowestCommonAncestor(slu, q, q) == null ? Null : sol

/ 	return lowestCommonAncestor(slu,p, p) == null ? Null : sole

else return slu
