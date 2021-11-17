### Most Popular Problems for Facebook (last 6 months):

Minimum Remove to Make Valid Parentheses (153 times):

[https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/)

```java
class Solution {
    public String minRemoveToMakeValid(String s) {
        int opening = 0;
        int closing = 0; 
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (c == '(') {
                sb.append(c); 
                opening++;
            } else if (c ==')') {
                if (closing < opening) {
                    sb.append(c); closing++;
                }
            } else {
                sb.append(c);
            }
        }
       
        if (opening == closing) return sb.toString();
        StringBuilder res = new StringBuilder();
        int toRemove = opening - closing;
        int toAdd = opening - toRemove;
        int i = 0;
        for (char c : sb.toString().toCharArray()){ .k
            if (c == '(') { //if c is a letter toAdd don't change
                toAdd--;
                if (toAdd < 0) continue;
            }
            res.append(c);            
        }
        return res.toString();[-
    }
}
```

K Closest Points to Origin (82 times):

[https://leetcode.com/problems/k-closest-points-to-origin/](https://leetcode.com/problems/k-closest-points-to-origin/)

```java
class Solution {
    // quick selection using partition: time-> average O(n) space-> O(1)
    Random random = new Random();
    public int[][] kClosest(int[][] points, int k) {
        int left = 0;
        int right = points.length - 1;
        while (true) {
            int pos = partition(points, left, right);
            if (pos == k - 1) return Arrays.copyOfRange(points, 0, k);
            if (pos > k - 1) {
                right = pos - 1;
            } else left = pos + 1;
        }
    }

    private int partition(int[][] points, int left, int right) {
        int pivotId = left + random.nextInt(right - left + 1);
        int pivot = distance(points[pivotId]);
        swap(points, left, pivotId);
        int l = left + 1, r = right;
        while (l <= r) {
            if (distance(points[l]) > pivot && distance(points[r]) < pivot) 
                swap(points, l, r);
            if (distance(points[l]) <= pivot) l++;
            if (distance(points[r]) >= pivot) r--;
        }
        swap(points, r, left);
        return r;
    }

    private void swap(int[][] points, int left, int right) {
        int[] temp = points[left];
        points[left] = points[right];
        points[right] = temp;
    }

    private int distance(int[] point) {
        return point[0] * point[0] + point[1] * point[1];
    }
}
```

Dot Product of Two Sparse Vectors (58 times):

[https://leetcode.com/problems/dot-product-of-two-sparse-vectors/](https://leetcode.com/problems/dot-product-of-two-sparse-vectors/)

```java
pair for two sparse dot vector
hashmap for one sparse dot vector
(remember advance the pointers after adding)
```

Range Sum of BST (55 times):

[https://leetcode.com/problems/range-sum-of-bst/](https://leetcode.com/problems/range-sum-of-bst/)

```java

//learning stack to solve all the dfs of tree
//inorder
public List<Integer> inorderTraversal(TreeNode root) {
	List<Integer> res = new ArrayList<>();
	if (root == null) return res;
	Stack<TreeNode> stack = new Stack<>();
	putLeft(root);

	while (!stack.isEmpty()) {
		TreeNode cur = stack.pop();
		res.add(cur.val);
		if (cur.right != null) putLeft(cur.right);
	}
}

private putLeft(TreeNode node) {
	while (node != null) {
		stack.append(node);
		node = node.left;
	}
}

//perorder
public List<Integer> preorderTraversal(TreeNode root) {
	List<Integer> res = new ArrayList<>();
	if (root == null) return res;
	Stack<TreeNode> stack = new Stack<>();
	stack.push(root);

	while (!stack.isEmpty()) {
		TreeNode cur = stack.pop();
		res.add(cur.val);
		if (cur.right != null) stack.push(cur.right);
		if (cur.left != null) stack.push(cur.left);
	}
	return res;
}

//postorder
public List<Integer> postorderTraversal(TreeNode root) {
	List<Integer> res = new ArrayList<>();
	if (root == null) return res;
	Stack<TreeNode> stack = new Stack<>();
	stack.push(root);

	while (!stack.isEmpty()) {
		TreeNode cur = stack.pop();
		res.add(0, cur.val);
		if (cur.left != null) stack.push(cur.left);
		if (cur.right != null) stack.push(cur.right);
	}
	return res;
}
```

```java
class Solution {
    public int rangeSumBST(TreeNode root, int low, int high) {
        if (root == null) return 0;
        int res = 0;
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode cur = stack.pop();
            if (cur.val >= low && cur.val <= high) res += cur.val;
            if (cur.val < high && cur.right != null) stack.push(cur.right);
            if (cur.val > low && cur.left != null) stack.push(cur.left);
        }
        return res;
    }
}
```

Convert Binary Search Tree to Sorted Doubly Linked List (48 times):

[https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/](https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/)

```java
class Solution {
  Node first = null;   // the head
  Node last = null;   // predecessor : first node which value is smaller than the cur node

  public void helper(Node node) {
      if (node == null) return;
      helper(node.left);
      
      if (first == null) first = node;
      if (last != null) {
          last.right = node;
          node.left = last;
      }
      last = node;
      
      helper(node.right);
  }

  public Node treeToDoublyList(Node root) {
    if (root == null) return null;

    helper(root);
    
    last.right = first;
    first.left = last;
    return first;
  }
}
```

Making A Large Island (34 times):

[https://leetcode.com/problems/making-a-large-island/](https://leetcode.com/problems/making-a-large-island/)

```java
class UnionFind{
    int[] id;
    int[] size;
    int max;  // 记得如果没有 0 可以变 要return 的是整个 grid size
    void union();
    int find();
}

class Solution {
    int[][] dirs = new int[][]{[0, 1], [1,0],[-1,0],[0,-1]};
    public int largestIsland(int[][] grid) {
        int n = grid.length;
        UnionFind uf = new UnionFind(n * n);
        int visited = new int[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    for (int[] dir : dirs) {
                        int x = i + dir[0];
                        int y = j + dir[1];
                        if (valid(gird, x, y) && visited[x][y] == 0) {
                            uf.union(grid, i * n + j, x * n + y);
                            visited[x][y] = 1;
                        }
                    }
                }
            }
        }

        int max = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 0) {
                    for (int[] dir : dirs) {
                        int area = 0;
                        Set<Integer> set = new HashSet<>();
                        int x = i + dir[0];
                        int y = j + dir[1];
                        if (!set.contains(uf.find(x * n + y))){
                            area += uf.size[x * n + y]
                            set.add(uf.find(x * n + y));
                        }
                        max = Math.max(max, area);
                    }
                }
            }
        }
        
        return Math.max(uf.max, max);

    }
}
```

Merge k Sorted Lists (26 times):

[https://leetcode.com/problems/merge-k-sorted-lists/](https://leetcode.com/problems/merge-k-sorted-lists/)

```java
class Solution {
    // 不用 pq 用 Divide-and-conquer algorithm -> 节省空间复杂度到logk(递归的栈空间)
    // time : O(nlogk)

    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        return merge(lists, 0, lists.length - 1);
    }

    private ListNode merge(ListNode[] lists, int l, int r) {
        if (l == r) return lists[l];
        int mid = l + (r - l) / 2;
        return mergeTwoLists(merge(lists, l, mid), merge(lists, mid + 1, r));
    }

    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode();
        ListNode prev = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val > l2.val) {
                prev.next = l2;
                l2 = l2.next;           
            } else {
                prev.next = l1; 
                l1 = l1.next;
            }
            prev = prev.next;
        }
        if (l1 != null) prev.next = l1;
        if (l2 != null) prev.next = l2;
        return dummy.next;
    }
}
```

All Nodes Distance K in Binary Tree (19 times):

[https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/)

```java
class Solution1 {  //建图然后 bfs
    Map<TreeNode, TreeNode> parent;
    public List<Integer> distanceK(TreeNode root, TreeNode target, int k) {
        parent = new HashMap<>();
        List<Integer> ans = new ArrayList();
        //dfs -> annotate every node with information about it's parent.
        dfs(root, null);
        //bfs -> find all nodes have a distance k from the target.
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(target);
        Map<TreeNode, Integer> dist = new HashMap<>();
        dist.put(target,  0);
        while (!queue.isEmpty()) {
            TreeNode cur = queue.poll();
            if (dist.get(cur) == k) {
                ans.add(cur.val);
                for (TreeNode n: queue)
                    ans.add(n.val);
                return ans;
            }
            if (cur.left != null && !dist.containsKey(cur.left)){
                queue.offer(cur.left);
                dist.put(cur.left, dist.get(cur) + 1);
            }
            if (cur.right != null && !dist.containsKey(cur.right)){
                queue.offer(cur.right);
                dist.put(cur.right, dist.get(cur) + 1);
            }
            if (parent.get(cur) != null && !dist.containsKey(parent.get(cur))){
                queue.offer(parent.get(cur) );
                dist.put(parent.get(cur) , dist.get(cur) + 1);
            }
        }
        return ans;
    }
    
    private void dfs(TreeNode node, TreeNode par) {
        if (node == null) return;
        parent.put(node, par);
        dfs(node.left, node);
        dfs(node.right, node);
    }
}

class Solution2 {  // Only annote the nodes that on the path of the root to target. 
    // If we know the depth of all the nodes on its path, we can calculate all the nodes' depth in the tree.
    Map<TreeNode, Integer> parent;
    List<Integer> ans;
    public List<Integer> distanceK(TreeNode root, TreeNode target, int k) {
        parent = new HashMap<>();
        ans = new ArrayList<>();
        // find the target and annotate the path
        find(root, target);
        // traverse all the tree tree and calculate each node's depth(if equals k, add to ans)
        helper(root, k, 0);
        return ans;
    }
    
    private int find(TreeNode root, TreeNode target) {
        if (root == null) return -1;
        if (root == target) {
            parent.put(root, 0);
            return 0;
        }
        
        int left = find(root.left, target);
        if (left >= 0) {
            parent.put(root, left + 1);
            return left + 1;
        }
        int right = find(root.right, target);
        if (right >= 0) {
            parent.put(root, right + 1);
            return right + 1;
        }
        
        return -1;
    }
    
    private void helper(TreeNode root, int k, int depth) {
        if (root == null) return;
        
        if (parent.containsKey(root)) 
            depth = parent.get(root);
        
        if (depth == k) 
            ans.add(root.val);
        
        helper(root.left, k, depth + 1);
        helper(root.right, k, depth + 1);
    }
}
```

Copy List with Random Pointer (18 times):

[https://leetcode.com/problems/copy-list-with-random-pointer/](https://leetcode.com/problems/copy-list-with-random-pointer/)

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;
        
        Node ptr = head;
        while (ptr != null) {
            Node newNode = new Node(ptr.val);
            newNode.next = ptr.next;
            ptr.next = newNode;
            //point to the next node in the orignal list
            ptr = newNode.next;
        }
        ptr = head;
        
        while (ptr != null) {
            // set the clone's node's random pointers
            ptr.next.random = (ptr.random != null) ? ptr.random.next : null; // 指向 random 的clone 节点
            ptr = ptr.next.next;
        }
        // unweave the linked list
        Node ptr_old_list = head;
        Node ptr_new_list = head.next;
        Node ans = head.next;
        while (ptr_old_list != null) {
            ptr_old_list.next = ptr_old_list.next.next;
            ptr_new_list.next = (ptr_new_list.next != null) ? ptr_new_list.next.next : null; // 指向 next 的clone 节点
            ptr_old_list = ptr_old_list.next;
            ptr_new_list = ptr_new_list.next;
        }
        return ans;
    }
}
```

Combination Sum (17 times):

[https://leetcode.com/problems/combination-sum/](https://leetcode.com/problems/combination-sum/)

First Bad Version (17 times):

[https://leetcode.com/problems/first-bad-version/](https://leetcode.com/problems/first-bad-version/)

Vertical Order Traversal of a Binary Tree (17 times):

[https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/](https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/)

Subsets (17 times):

[https://leetcode.com/problems/subsets/](https://leetcode.com/problems/subsets/)

Random Pick Index (17 times):

[https://leetcode.com/problems/random-pick-index/](https://leetcode.com/problems/random-pick-index/)

```java
class Solution {  // when we need to many times pick

    private HashMap<Integer, List<Integer>> indices;
    private Random rand;
    
    public Solution(int[] nums) {
        this.rand = new Random();
        this.indices = new HashMap<Integer, List<Integer>>();
        int l = nums.length;
        for (int i = 0; i < l; ++i) {
            if (!this.indices.containsKey(nums[i])) {
                this.indices.put(nums[i], new ArrayList<>());
            }
            this.indices.get(nums[i]).add(i);
        }
    }
    
    public int pick(int target) {
        int l = indices.get(target).size();
        // pick an index at random
        int randomIndex = indices.get(target).get(rand.nextInt(l));
        return randomIndex;
    }
}

class Solution {  // data coming from stream or do not use extra memory

    private int[] nums;
    private Random rand;
    
    public Solution(int[] nums) {
        this.nums = nums;
        this.rand = new Random();
    }
    
    public int pick(int target) {
        int n = this.nums.length;
        int count = 0;
        int idx = 0;
        for (int i = 0; i < n; ++i) {
            // if nums[i] is equal to target, i is a potential candidate
            // which needs to be chosen uniformly at random
            if (this.nums[i] == target) {
                // increment the count of total candidates
                // available to be chosen uniformly at random
                count++;
                // we pick the current number with probability 1 / count (reservoir sampling)
                if (rand.nextInt(count) == 0) {
                    idx = i;
                }
            }
        }
        return idx;
    }
}
```

3Sum Closest (16 times):

[https://leetcode.com/problems/3sum-closest/](https://leetcode.com/problems/3sum-closest/)

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int diff = Integer.MAX_VALUE;
        for (int i = 0; i < nums.length - 2; i++) {
            int p = i + 1;
            int q = nums.length - 1;    
            while (p < q) {
                int sum = nums[i] + nums[p] + nums[q];
                if (Math.abs(target - sum) < Math.abs(diff)) 
                    diff = target - sum;
                    
                if (sum < target) p++;
                else q--;
            }   
        }
        return target - diff;    
    }
}
```

Insert into a Sorted Circular Linked List (15 times):

[https://leetcode.com/problems/insert-into-a-sorted-circular-linked-list/](https://leetcode.com/problems/insert-into-a-sorted-circular-linked-list/)

```java
class Solution {
    public Node insert(Node head, int insertVal) {
        if (head == null) { 
            Node insert  = new Node(insertVal); 
            insert.next = insert; 
            return insert;
        }
        Node pre = head;
        Node cur = head.next;
        boolean toInsert = false;
        
        do { // 用do 因为是先执行 可以完美解决环最开始的起点问题
             // case1: 找一个可以插入的位置 pre<= insertval <=cur
            if (pre.val <= insertVal && cur.val >= insertVal) {
               toInsert = true;
            }
            
            //case2: 如果走到了尾巴 验证一下 要插入的值是不是out of bound 如果是也插入
            if (pre.val > cur.val) {
                if (insertVal > pre.val || insertVal < cur.val) 
                    toInsert = true;
            }

            if (toInsert) {
                pre.next = new Node(insertVal, cur);
                return head;
            }
            
            pre = pre.next;
            cur = cur.next;

        } while (pre != head);
        
        // case3: 如果整个链表都是一样的 而要插入的和链表的值不一样 如[1]-0 [3,3,3] -4
        pre.next = new Node(insertVal, cur);
        return head;       
    }
}
```

Median of Two Sorted Arrays (14 times):

[https://leetcode.com/problems/median-of-two-sorted-arrays/](https://leetcode.com/problems/median-of-two-sorted-arrays/)

```java
时间复杂度是 log(min((m, n))  
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) return findMedianSortedArrays(nums2, nums1);
        int len = nums1.length + nums2.length;
        //切的位置
        int cut1 = 0;
        int cut2 = 0;
        //二分的范围 左右
        int cutL = 0;
        int cutR = nums1.length;
         while (cut1 <= nums1.length) {
             cut1 = (cutR - cutL) / 2 + cutL;
             cut2 = len / 2 - cut1;
             double L1 = (cut1 == 0) ? Integer.MIN_VALUE : nums1[cut1 - 1];
             double L2 = (cut2 == 0) ? Integer.MIN_VALUE : nums2[cut2 - 1];
             double R1 = (cut1 == nums1.length) ? Integer.MAX_VALUE : nums1[cut1];
             double R2 = (cut2 == nums2.length) ? Integer.MAX_VALUE : nums2[cut2];
             if (L1 > R2) {
                 cutR = cut1 -1 ;
             } else if (L2 > R1) {
                 cutL = cut1 + 1;
             } else {
                 //只有 L1 <= R2 && L2 <= R1的时候才符合
                 if (len % 2 == 0) {
                     double L = L1 > L2 ? L1 : L2;
                     double R = R1 < R2 ? R1 : R2;
                     return (L + R) / 2;
                 } else {
                     double mid = R1 < R2 ? R1 : R2;
                     return mid;
                 }

             }
         }
        return 0;   // 我觉得不用放 但是编译器会报错
    }
}
```

Valid Word Abbreviation (13 times):

[https://leetcode.com/problems/valid-word-abbreviatio](https://leetcode.com/problems/valid-word-abbreviation/)n

```java
class Solution {
    public boolean validWordAbbreviation(String word, String abbr) {
        int p = 0;
        int q = 0;
        while (p < word.length() && q < abbr.length()) {
            if (Character.isDigit(abbr.charAt(q))) {
                if(abbr.charAt(q) == '0') return false;
                int n = 0;
                while ( q < abbr.length() && Character.isDigit(abbr.charAt(q))) {
                    n = n * 10 + (abbr.charAt(q) - '0');
                    q++;
                }
                p += n;
            } else {
                if (word.charAt(p) != abbr.charAt(q)) return false;
                p++;
                q++;
            }
        }
        return p == word.length() && q == abbr.length(); // the pointers must point to the end of respective strings
        // eg: a - 2  h - h2
    }
}
```

Remove All Adjacent Duplicates in String II(12 times):

[https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/)

```java
class Solution {
    public String removeDuplicates(String s, int k) {
        Stack<Integer> counts = new Stack<>();
        char[] sa = s.toCharArray();
        int fast = 0;
        int slow = 0;
        while (fast < s.length()) {
            sa[slow] == sa[fast];
            if (slow == 0 || sa[slow] != sa[slow - 1]) {
                counts.push(1);
            } else {
                int incremented = counts.pop() + 1;
                if (incremented == k) {
                    slow = slow - k; // 后退 k 个 erase 这k 个字符
                } else {
                    counts.push(incremented);
                }
            }
            fast++;
            slow++;
        }
        System.out.println(slow);
        return new String(sa, 0, slow);
    }
}
```

String Compression (9 times):

[https://leetcode.com/problems/string-compression/](https://leetcode.com/problems/string-compression/)

```java
class Solution {
    public int compress(char[] chars) {
        int slow = 0;  // always points to the last char of the return string 
        int count = 1;  // remember to renew to 1 if already wrote the count in the string
        for (int fast = 1; fast <= chars.length; fast++) { 
            if (fast < chars.length && chars[slow] == chars[fast]) {
                count++;
            } else {
                if (count != 1) {
                    //put the number in a reverse order
                    // reverse the number 
                    // put the slow pointer in the next char's place 
                    slow++;
                    int anchor = slow; // abbbbb=s=1  
                    while (count > 0) {
                        chars[slow++] = (char) (count % 10 + '0');
                        count /= 10;
                    }
                    slow--;
                    reverse(chars, anchor, slow); 
                    //renew the counter
                    count = 1;
                }
                
                // set the slow to the next char if fast not over the bound
                if (fast < chars.length) {
                    slow++;
                    chars[slow] = chars[fast];
                }
            }
        }
        
        return slow + 1; 
    }
    
    public void reverse(char[] chars, int left, int right) {
        while (left < right) {
            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;
            left++;
            right--;
        }
    }
}
```

Reorganize String (9 times):

[https://leetcode.com/problems/reorganize-string/](https://leetcode.com/problems/reorganize-string/)

```java
class Solution {
    public String reorganizeString(String s) {
        if (s.length() < 2) {
            return s;
        }
        int[] counts = new int[26];
        int maxCount = 0;
        int length = s.length();
        for (int i = 0; i < length; i++) {
            char c = s.charAt(i);
            counts[c - 'a']++;
            maxCount = Math.max(maxCount, counts[c - 'a']);
        }
        if (maxCount > (length + 1) / 2) {
            return "";
        }
        char[] reorganizeArray = new char[length];
        int evenIndex = 0, oddIndex = 1;
        int halfLength = length / 2;
        for (int i = 0; i < 26; i++) {
            char c = (char) ('a' + i);
						// if count[i] == halfLength + 1, we put it from zero position
            while (counts[i] > 0 && counts[i] <= halfLength && oddIndex < length) {
                reorganizeArray[oddIndex] = c;
                counts[i]--;
                oddIndex += 2;
            }
            while (counts[i] > 0) { 
                reorganizeArray[evenIndex] = c;
                counts[i]--;
                evenIndex += 2;
            }
        }
        return new String(reorganizeArray);
    }
}
```

Word Ladder (8 times):

[https://leetcode.com/problems/word-ladder/](https://leetcode.com/problems/word-ladder-ii/)

```java
class Solution {
     public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        //1.build the graph
        int n = beginWord.length();
        Set<String> set = new HashSet<>(wordList);
        Map<String, LinkedList<String>> map = new HashMap<>();

        //1.1begin
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < 26; j++) {
                String cur = beginWord.substring(0, i) + (char) (j + 'a') + beginWord.substring(i + 1);
                if (set.contains(cur)) {
                    map.computeIfAbsent(beginWord, k -> new LinkedList<>()).add(cur);
                    map.computeIfAbsent(cur, k -> new LinkedList<>()).add(beginWord);
                }
            }
        }
        
        //1.2 others
        for (String s : wordList) {
        
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < 26; j++) {
                    String cur = s.substring(0, i) + (char) (j + 'a') + s.substring(i + 1);
                    if (set.contains(cur)) {
                        map.computeIfAbsent(s, k -> new LinkedList<>()).add(cur);
                        map.computeIfAbsent(cur, k -> new LinkedList<>()).add(s);
                    }
                }
            }

        }
        
        //2.bfs
        Queue<String> queue = new ArrayDeque<>();
        Map<String, Integer> dist = new HashMap<>();
        queue.offer(beginWord);
        dist.put(beginWord, 1);
        while (!queue.isEmpty()) {
            String cur = queue.poll();
            if (!map.containsKey(cur)) continue;  // map.get之前要先确定有没有！！！！
            for (String s : map.get(cur)) {
                if (dist.containsKey(s)) continue;
                queue.offer(s);
                dist.put(s, dist.get(cur) + 1);
                if (s.equals(endWord)) return dist.get(endWord);
            }
        }
        return 0;

    }
}
```

Longest Increasing Path in a Matrix (8 times):

[https://leetcode.com/problems/longest-increasing-path-in-a-matrix/](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

```java
class Solution {

    // 上下左右四个方向
    int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

    public int longestIncreasingPath(int[][] matrix) {
        // 从每一个点出发，往下深搜，看它最远能到哪
        int m = matrix.length;
        int n = matrix[0].length;
        
        // 记忆化
        int[][] memo = new int[m][n];
        
        // 每个点都要作为起始点遍历一下
        int ans = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // 已经遍历过的就不用遍历了
                if (memo[i][j] == 0) {
                    ans = Math.max(ans, dfs(matrix, m, n, i, j, memo));
                }
                // 这里为什么不用再比较一次 ans 和 memo[i][j]呢？
                // 因为遍历前面节点的时候已经把后面的节点遍历了
                // 说明后面的节点肯定比前面的节点的最长路径短
                // 所以，不用多判断一次了
            }
        }

        return ans;
    }

    private int dfs(int[][] matrix, int m, int n, int i, int j, int[][] memo) {
        // 已经遍历过，直接返回
        if (memo[i][j] != 0) {
            return memo[i][j];
        }

        // 否则，看四个方向是否有满足条件的节点去扩散
        // 每个节点的初始路径为1
        int ans = 1;
        for (int[] dir : dirs) {
            int nextI = i + dir[0];
            int nextJ = j + dir[1];
            if (nextI >= 0 && nextJ >= 0 && nextI < m && nextJ <n && matrix[nextI][nextJ] > matrix[i][j]) {
                ans = Math.max(ans, dfs(matrix, m, n, nextI, nextJ, memo) + 1);
            }
        }

        // 记录到缓存中
        memo[i][j] = ans;
        return ans;
    }
}
```

Flatten Nested List Iterator (8 times):

[https://leetcode.com/problems/flatten-nested-list-iterator/](https://leetcode.com/problems/flatten-nested-list-iterator/)

```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return empty list if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
import java.util.NoSuchElementException;

public class NestedIterator implements Iterator<Integer> {

    // In Java, the Stack class is considered deprecated. Best practice is to use
    // a Deque instead. We'll use addFirst() for push, and removeFirst() for pop.
    private Stack<NestedInteger> stack;
    
    public NestedIterator(List<NestedInteger> nestedList) {
        // The constructor puts them on in the order we require. No need to reverse.
        stack = new Stack();
        for (int i = nestedList.size() - 1; i >= 0; i--) {
                stack.push(nestedList.get(i));
            }
    }
        
    
    @Override
    public Integer next() {
        // As per java specs, throw an exception if there's no elements left.
        if (!hasNext()) throw new NoSuchElementException();
        // hasNext ensures the stack top is now an integer. Pop and return
        // this integer.
        return stack.pop().getInteger();
    }

    
    @Override
    public boolean hasNext() {
        // Check if there are integers left by getting one onto the top of stack.
        makeStackTopAnInteger();
        // If there are any integers remaining, one will be on the top of the stack,
        // and therefore the stack can't possibly be empty.
        return !stack.isEmpty();
    }

    private void makeStackTopAnInteger() {
        // While there are items remaining on the stack and the front of 
        // stack is a list (i.e. not integer), keep unpacking.
        while (!stack.isEmpty() && !stack.peek().isInteger()) {
            // Put the NestedIntegers onto the stack in reverse order.
            List<NestedInteger> nestedList = stack.pop().getList();
            for (int i = nestedList.size() - 1; i >= 0; i--) {
                stack.push(nestedList.get(i));
            }
        }
    }
}

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i = new NestedIterator(nestedList);
 * while (i.hasNext()) v[f()] = i.next();
 */
```

Kth Smallest Element in a Sorted Matrix (8 times):

[https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/)

```java
class Solution {
    // iterate through the string in reverse, count the backspace and skip the characters that should be skipped
    public boolean backspaceCompare(String s, String t) {
        int i = s.length() - 1;
        int j = t.length() -1;
        int skipS = 0;
        int skipT = 0;
        while (j >= 0 || i >= 0) {
            while(i>=0) {
                if (s.charAt(i) == '#') {
                    skipS++;
                    i--;
                } else if (skipS > 0) { //要把#都过完才执行 skip
                    skipS--;
                    i--;
                } else break;
            }
            
            while(j>=0) {
                if (t.charAt(j) == '#') {
                    skipT++;
                    j--;
                } else if (skipT > 0) {
                    skipT--;
                    j--;
                } else break;
            }
            // If two actual characters are different
            if (i >= 0 && j >= 0 && s.charAt(i) != t.charAt(j))
                return false;
            if ((i >= 0) != (j >= 0)) return false;
            i--; j--;
        }
        return true;
    }
}
```

Unique Paths (8 times):

[https://leetcode.com/problems/unique-paths/](https://leetcode.com/problems/unique-paths/)

Intersection of Two Linked Lists (8 times):

[https://leetcode.com/problems/intersection-of-two-linked-lists/](https://leetcode.com/problems/intersection-of-two-linked-lists/)

Design Tic-Tac-Toe (6 times):

[https://leetcode.com/problems/design-tic-tac-toe/](https://leetcode.com/problems/design-tic-tac-toe/)

```java
class TicTacToe { // use array store the moves on the rows, cols and diagonals, and optimize it by using - + in one data structure. (eg. if one col have been -1 it can never achieve the n)
    int[] rows;
    int[] cols;
    int diagonal;
    int antiDiagonal;
    
    public TicTacToe(int n) {
        rows=  new int[n];
        cols = new int[n];
    }
    
    public int move(int row, int col, int player) {  // if get a n number, one person mush win\
        int n = rows.length;
        int currentPlayer = (player == 1) ? 1 : -1;
        rows[row] += currentPlayer;
        cols[col] += currentPlayer;
        
        if (Math.abs(cols[col]) == n || Math.abs(rows[row]) == n) return player;
        if (row == col) {
            diagonal  += currentPlayer;
            if (Math.abs(diagonal) == n ) return player;
        }
        if (col == (cols.length - row - 1)) {
            antiDiagonal += currentPlayer;
             if (Math.abs(antiDiagonal) == n ) return player;
        }
        return 0;
    }
}

/**
 * Your TicTacToe object will be instantiated and called as such:
 * TicTacToe obj = new TicTacToe(n);
 * int param_1 = obj.move(row,col,player);
 */
```

Maximum Subarray (6 times): (divid and conquer???)

[https://leetcode.com/problems/maximum-subarray/](https://leetcode.com/problems/maximum-subarray/)

Product of Two Run-Length Encoded Arrays (6 times):

[https://leetcode.com/problems/product-of-two-run-length-encoded-arrays/](https://leetcode.com/problems/product-of-two-run-length-encoded-arrays/)

```java
class Solution {
    //encoded1 = [[2,1],[3,2]], encoded2 = [[3,3]]
    public List<List<Integer>> findRLEArray(int[][] encoded1, int[][] encoded2) {
        List<List<Integer>> res = new ArrayList<>();
        int p = 0;
        int q = 0;
        int n = encoded1.length();
        int m = encoded2.length();
        int last = 0;
        while (p < n && q < m) { 
            int product = encoded1[p][0] * encoded2[q][0];
            int len =  Math.min( encoded1[p][1] , encoded2[q][1]);
            
            if (product != last) {
                res.add(Arrays.asList(product,len));

            } else {
                 res.get(res.size()-1).set(1,res.get(res.size()-1).get(1)+len);
            }
            last = product;
            
             encoded1[p][1] -= len;
              encoded2[q][1] -= len;
            if (   encoded1[p][1]  == 0) p++;
             if (  encoded2[q][1]  == 0) q++;
        }
        return res;
        
    }
}
```

Palindromic Substrings (6 times):  (expand from the possible center, like peeling an onion)

[https://leetcode.com/problems/palindromic-substrings/](https://leetcode.com/problems/palindromic-substrings/)

```java
class Solution { //"aaa"
    //time: O(n^n) space:O(1)
    public int countSubstrings(String s) {
        int ans = 0;
        for (int i = 0; i < s.length(); ++i) {
            // odd-length palindromes, single character center
            ans += countPalindromesAroundCenter(s, i, i);
            // even-length palindromes, consecutive characters center
            ans += countPalindromesAroundCenter(s, i, i + 1);
        }
        return ans;
    }

    private int countPalindromesAroundCenter(String s, int lo, int hi) {
        int ans = 0;
        while (lo >= 0 && hi < s.length()) {
            if (s.charAt(lo) == s.charAt(hi)) {  // expand around the center
                lo--;
                hi++;
                ans++;
            } else break;   // the first and last characters don't match!
        }
        return ans;
    }
}
```

Target Sum (6 times): dp

[https://leetcode.com/problems/target-sum/](https://leetcode.com/problems/target-sum/)

Shortest Bridge (6 times):

[https://leetcode.com/problems/shortest-bridge/](https://leetcode.com/problems/shortest-bridge/)

```java
class Solution {
    // 用来转换方向
    private static final int[][] dirs = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};

    public int shortestBridge(int[][] grid) {
        // 记录下来整个图的尺寸
        int n = grid.length, m = grid[0].length;
        // 标记哪些位置已经访问过了
        boolean[][] visited = new boolean[n][m];
        // 标记是否找到第一个小岛了
        boolean findIsland = false;
        // 记录第一个小岛的边界
        Queue<int[]> board = new LinkedList<>();
        // 去标记第一个小岛，将原来的 1 改成 2
        for (int x = 0; x < n && !findIsland; ++x) {
            for (int y = 0; y < m && !findIsland; ++y) {
                if (!visited[x][y] && grid[x][y] == 1) {
                    mark(grid, visited, x, y, 2, board);
                    findIsland = true;
                }
            }
        }
        // 记录距离
        int ans = 0;
        while (!board.isEmpty()) {
            // 记录当前需要遍历的边界数量
            int size = board.size();
            for (int i = 0; i < size; ++i) {
                // 取出来下一个要检查的坐标
                int[] next = board.poll();
                for (int j = 0; j < 4; ++j) {
                    // 对这个坐标进行上下左右检查
                    assert next != null;
                    int nx = next[0] + dirs[j][0];
                    int ny = next[1] + dirs[j][1];
                    // 如果这个新坐标合法，而且没有访问过，就对其进行检查
                    if (nx >= 0 && nx < n && ny >= 0 && ny < m && !visited[nx][ny]) {
                        visited[nx][ny] = true; // 首先标记成访问过了
                        // 如果接触到了第二个小岛，那么直接返回距离计数器 ans
                        if (grid[nx][ny] == 1) return ans;
                        // 否则的话，就放到边界队列中
                        else board.add(new int[]{nx, ny});
                    }
                }
            }
            // 边界计数器加一
            ++ans;
        }
        return ans;
    }

    private void mark(int[][] grid, boolean[][] visited, int x, int y, int color, Queue<int[]> board) {
        grid[x][y] = color;
        // 判断是不是边界，是的话就直接放到队列中
        if (x == 0 || y == 0 || x == grid.length - 1 || y == grid[0].length - 1 ||
                grid[x + 1][y] == 0 || grid[x - 1][y] == 0 || grid[x][y + 1] == 0 || grid[x][y - 1] == 0) {
            board.add(new int[]{x, y});
        }
        // 对 [x, y] 上下左右四个方向进行拓展检查
        for (int i = 0; i < 4; ++i) {
            int nx = x + dirs[i][0];
            int ny = y + dirs[i][1];
            // 对小岛进行扩展查找
            if (nx >= 0 && nx < grid.length && ny >= 0 && ny < grid[0].length && !visited[nx][ny] && grid[nx][ny] == 1) {
                visited[nx][ny] = true;
                mark(grid, visited, nx, ny, color, board);
            }
        }
    }
}
```

Course Schedule (6 times):

[https://leetcode.com/problems/course-schedule/](https://leetcode.com/problems/course-schedule/)

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int[] indegrees = new int[numCourses];// indices represents the course Id
        List<List<Integer>> adjacency = new ArrayList<>(); // adjacency courses, indices represents the prerequisites course Id
       
        for(int i = 0; i < numCourses; i++)
            adjacency.add(new ArrayList<>());
        // Get the indegree and adjacency of every course.
        for(int[] cp : prerequisites) {
            indegrees[cp[0]]++;
            adjacency.get(cp[1]).add(cp[0]);
        }

        Queue<Integer> queue = new LinkedList<>();
        // Get all the courses with the indegree of 0.
        for(int i = 0; i < numCourses; i++)
            if(indegrees[i] == 0) queue.add(i);

        // BFS TopSort.
        while(!queue.isEmpty()) {
            int pre = queue.poll();
            numCourses--;
            for(int cur : adjacency.get(pre))
                if(--indegrees[cur] == 0) queue.add(cur);
        }
        return numCourses == 0;
    }
}
```

Populating Next Right Pointers in Each Node II (6 times):

[https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/)

```java
class Solution {
      public Node connect(Node root) {
        if (root == null)
            return root;
        // cur is upper and pre is lower (we don't need save the head position since we use dummy)
        Node cur = root;
        // move from level to level
        while (cur != null) {
            Node dummy = new Node(0);
            Node pre = dummy;
            // move on the same level
            while (cur != null) {
                if (cur.left != null) {
                    pre.next = cur.left;
                    pre = pre.next;
                }
                if (cur.right != null) {
                    pre.next = cur.right;
                    pre = pre.next;
                }
                cur = cur.next;
            }
            cur = dummy.next;
        }
        return root;
    }
}
```

Walls and Gates (5 times):

[https://leetcode.com/problems/walls-and-gates/](https://leetcode.com/problems/walls-and-gates/)

Number of Distinct Islands (5 times):

[https://leetcode.com/problems/number-of-distinct-islands/](https://leetcode.com/problems/number-of-distinct-islands/)

Smallest Subtree with all the Deepest Nodes (5 times):

[https://leetcode.com/problems/smallest-subtree-with-all-the-deepest-nodes/](https://leetcode.com/problems/smallest-subtree-with-all-the-deepest-nodes/)

```java
// If I stand on root, one of the children is deeper, then I know the result is on this branch. Same to its children(Recursion). And we can use a Structure to pass the result and store the depth.
class Solution {  
    public TreeNode subtreeWithAllDeepest(TreeNode root) {
        return dfs(root).node;
    }
    
    Result dfs(TreeNode root) {
        if (root == null) return new Result(root, 0);
        Result left = dfs(root.left);
        Result right = dfs(root.right);
        if (left.depth > right.depth) 
            return new Result(left.node, left.depth + 1);
        if (right.depth > left.depth) 
            return new Result(right.node, right.depth + 1);
        else
            return new Result(root, left.depth + 1);
    }
}

class Result {
    TreeNode node;
    int depth;
    
    Result(TreeNode node, int depth) {
        this.node = node;
        this.depth = depth;
    }
}
```

Find the Duplicate Number (4 times):

[https://leetcode.com/problems/find-the-duplicate-number/](https://leetcode.com/problems/find-the-duplicate-number/)

```java
class Solution { // 不能用位运算 因为不像 missing 只能 miss 一次， duplicate 可以 duplicate 多次 [2,2,2,2,2]
    public int findDuplicate(int[] nums) {
        int slow = 0;
        int fast = 0;
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (fast != slow);
        /*
---a----
     /    \b
    |c    |(frist meet)
     \ _ /

*/
        slow = 0; // a = c 所以再让 slow 从🕖开始走就好了
        while (fast != slow){
            slow = nums[slow];
            fast = nums[fast];
        }
        return fast;
    }
}
```

Next Closest Time (4 times):

[https://leetcode.com/problems/next-closest-time/](https://leetcode.com/problems/next-closest-time/)

```java
class Solution {//枚举+1min
    public String nextClosestTime(String time) {
        String[] arr = time.split(":");
        Set<Character> s = new HashSet();
        s.add(arr[0].charAt(0));
        s.add(arr[0].charAt(1));
        s.add(arr[1].charAt(0));
        s.add(arr[1].charAt(1));
        int hour = Integer.valueOf(arr[0]);
        int minute = Integer.valueOf(arr[1]);
        int minutes = hour*60+minute;
        for(int i=minutes+1;i<=minutes+24*60;i++){
            String h = ""+(i%(24*60))/60;
            if(h.length()==1)h="0"+h;
            String m = ""+(i%(24*60))%60;
            if(m.length()==1)m="0"+m;
            if(s.contains(m.charAt(0))&&
            s.contains(m.charAt(1))&&
            s.contains(h.charAt(0))&&
            s.contains(h.charAt(1))){
                return h+":"+m;
            }
        }
        return "";
    }
}
```

Reverse Words in a String (4 times):

[https://leetcode.com/problems/reverse-words-in-a-string/](https://leetcode.com/problems/reverse-words-in-a-string/)

```java
class Solution {
  public StringBuilder trimSpaces(String s) {
    int left = 0, right = s.length() - 1;
    // remove leading spaces
    while (left <= right && s.charAt(left) == ' ') ++left;

    // remove trailing spaces
    while (left <= right && s.charAt(right) == ' ') --right;

    // reduce multiple spaces to single one
    StringBuilder sb = new StringBuilder();
    while (left <= right) {
      char c = s.charAt(left);

      if (c != ' ') sb.append(c);
      else if (sb.charAt(sb.length() - 1) != ' ') sb.append(c);

      ++left;
    }
    return sb;
  }

  public void reverse(StringBuilder sb, int left, int right) {
    while (left < right) {
      char tmp = sb.charAt(left);
      sb.setCharAt(left++, sb.charAt(right));
      sb.setCharAt(right--, tmp);
    }
  }

  public void reverseEachWord(StringBuilder sb) {
    int n = sb.length();
    int start = 0, end = 0;

    while (start < n) {
      // go to the end of the word
      while (end < n && sb.charAt(end) != ' ') ++end;
      // reverse the word
      reverse(sb, start, end - 1);
      // move to the next word
      start = end + 1;
      ++end;
    }
  }

// main function
  public String reverseWords(String s) {
    // converst string to string builder 
    // and trim spaces at the same time
    StringBuilder sb = trimSpaces(s);

    // reverse the whole string
    reverse(sb, 0, sb.length() - 1);

    // reverse each word
    reverseEachWord(sb);

    return sb.toString();
  }
}
```

Reconstruct Itinerary (4 times):

[https://leetcode.com/problems/reconstruct-itinerary/](https://leetcode.com/problems/reconstruct-itinerary/)

Longest Repeating Character Replacement (4 times):

[https://leetcode.com/problems/longest-repeating-character-replacement/](https://leetcode.com/problems/longest-repeating-character-replacement/)

```java
class Solution {
    public int characterReplacement(String s, int k) {
        int maxCount = 0;
        int maxLen = 0;
        int[] count = new int[28];
        for (int i = 0, j= 0; i < s.length(); i++) {
            char c = s.charAt(i);
            count[c - 'A']++;
            maxCount = Math.max(maxCount, count[c - 'A']);
            if (maxCount + k < i - j + 1) 
                count[s.charAt(j++) - 'A']--;
            maxLen = Math.max(maxLen, i - j + 1);
        }
        return maxLen;
    }
}
```

Bus Routes (4 times):

[https://leetcode.com/problems/bus-routes/](https://leetcode.com/problems/bus-routes/)

Check Completeness of a Binary Tree (4 times):

[https://leetcode.com/problems/check-completeness-of-a-binary-tree/](https://leetcode.com/problems/check-completeness-of-a-binary-tree/)

```java
class Solution { // we can also put the null into queue, but if it is null, it must be the last node. So we can set a flag, and check it when we see a non-null node.
    public boolean isCompleteTree(TreeNode root) {
        
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        boolean flag=  false; // have met a null node
        while (!queue.isEmpty()) {
            TreeNode cur = queue.poll();
            if (cur == null) flag = true;
            else {  // met a non-null node,
                if (flag) return false; // but have met a null before
                queue.offer(cur.left);
                queue.offer(cur.right);
            }
        }
        return true;
    }
}
```

Detect Cycles in 2D Grid (4 times): uf (each time check two edges)

[https://leetcode.com/problems/detect-cycles-in-2d-grid/](https://leetcode.com/problems/detect-cycles-in-2d-grid/)

Different Ways to Add Parentheses (4 times):

[https://leetcode.com/problems/different-ways-to-add-parentheses/](https://leetcode.com/problems/different-ways-to-add-parentheses/)

```java
class Solution {
    public List<Integer> diffWaysToCompute(String expression) {// return all the possible answer
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < expression.length(); i++) {
            char c = expression.charAt(i);
            if (c == '+' || c == '-' || c == '*') {
                List<Integer> al = diffWaysToCompute(expression.substring(0,i));
                List<Integer> bl = diffWaysToCompute(expression.substring(i+1));
                for (int x : al) {
                    for (int y : bl) {
                        switch(c){
                            case '+':
                                res.add(x + y);
                                break;
                            case '-':
                                res.add(x - y);
                                break;
                            case '*':
                                res.add(x * y);
                                break;
                        }
                    }
                }
            }
        }
        if (res.size() == 0) res.add(Integer.valueOf(expression));
        return res;
    }
}
```

Pacific Atlantic Water Flow (3 times):

[https://leetcode.com/problems/pacific-atlantic-water-flow/](https://leetcode.com/problems/pacific-atlantic-water-flow/)

Monotonic Array (3 times):

[https://leetcode.com/problems/monotonic-array/](https://leetcode.com/problems/monotonic-array/)

```java
class Solution {
    public boolean isMonotonic(int[] A) {
        boolean increasing = true;
        boolean decreasing = true;
        for (int i = 0; i < A.length - 1; ++i) {
            if (A[i] > A[i+1])
                increasing = false;
            if (A[i] < A[i+1])
                decreasing = false;
        }

        return increasing || decreasing;
    }
}
```

Battleships in a Board (3 times):

[https://leetcode.com/problems/battleships-in-a-board/](https://leetcode.com/problems/battleships-in-a-board/)

Beautiful Array (3 times):

[https://leetcode.com/problems/beautiful-array/](https://leetcode.com/problems/beautiful-array/)

```java
class Solution {
    public int[] beautifulArray(int n) {
        // 1 3 5 + 2 4 (因为左右分别是奇数和偶数，不会出现中间有平均数的情况)
        if (n == 1) return new int[]{1};
        int[] odd =  beautifulArray(n / 2 + n % 2); 
        int[] even = beautifulArray(n / 2);
        int[] ans = new int[n];
        for (int i = 0; i< odd.length; i++) {
            ans[i] = 2 * odd[i] - 1;
        }
        for (int i = 0; i< even.length; i++) {
            ans[odd.length + i] = 2 * even[i];
        }
        return ans;
    }
}
```

Find All Duplicates in an Array (3 times):

[https://leetcode.com/problems/find-all-duplicates-in-an-array/](https://leetcode.com/problems/find-all-duplicates-in-an-array/)

```java
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> ans = new ArrayList<>();
//211  if a number is duplicate, we can see it twice. So the next time we see the number, the value at index abs(x)-1 is already negative, we know that we've seen element x before.
        for (int num : nums) {
            if(nums[Math.abs(num)-1] < 0) ans.add(Math.abs(num));
            nums[Math.abs(num)-1] *= -1;         
        }
        return ans;
    }
}
```

Remove Duplicate Letters (3 times):

[https://leetcode.com/problems/remove-duplicate-letters/](https://leetcode.com/problems/remove-duplicate-letters/)

```java
class Solution {
    public String removeDuplicateLetters(String s) {
        Map<Character, Integer> lastPos = new HashMap<>(); //
        for (int i = 0; i < s.length(); i++){
            lastPos.put(s.charAt(i), i);
        }
        Set<Character> hasPut = new HashSet<>(); //
        Stack<Character> stack = new Stack<>(); // space:O(26) = O(1)
        for (int i = 0; i < s.length();i++) {
            char c = s.charAt(i);
            if (hasPut.contains(c)) continue;
            while (!stack.isEmpty() && stack.peek() > c && lastPos.get(stack.peek()) > i) {
                char toRemove = stack.pop();
                hasPut.remove(toRemove);
            }
            stack.push(c);
            hasPut.add(c);
        }
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) {
            sb.append(stack.pop());
        }
        return sb.reverse().toString();
    }
}
```

Minimum Height Trees (3 times): (peel onion)

[https://leetcode.com/problems/minimum-height-trees/](https://leetcode.com/problems/minimum-height-trees/)

```java
class Solution {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        List<Integer> res = new ArrayList<>();
        if (n == 1) {
            res.add(0);
            return res;
        }
        List<HashSet<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new HashSet<>());
        }
        //build graph
        for (int[] edge: edges) {
            adj.get(edge[0]).add(edge[1]);
            adj.get(edge[1]).add(edge[0]);
        }
        // first put the leaves
        for (int i = 0; i < n; i++) {
            if (adj.get(i).size() == 1) {
                res.add(i);
            }
        }
        // put the leaves level by level until the last one or two nodes
        while(n > 2) {
            n  = n - res.size();
            List<Integer> leaves = new ArrayList<>();
            for (int i : res) { 
                for (int j : adj.get(i)) {
                    adj.get(j).remove(i);
                    if (adj.get(j).size() == 1) 
                        leaves.add(j);
                }
            }
            res = leaves;
        }
        return res;
    }
}
```

Binary Tree Longest Consecutive Sequence (3 times):

[https://leetcode.com/problems/binary-tree-longest-consecutive-sequence/](https://leetcode.com/problems/binary-tree-longest-consecutive-sequence/)

```java

class Solution {
    private int maxLength = 0;
    public int longestConsecutive(TreeNode root) {
        dfs(root);
        return maxLength;
    }
    
    private int dfs(TreeNode node) {  // bottom up
        if (node == null) return 0;
        int left = dfs(node.left) + 1;
        int right = dfs(node.right) + 1;
        
        if (node.left != null && node.val + 1 != node.left.val) 
            left = 1;
        if (node.right != null && node.val + 1 != node.right.val) 
            right = 1;
        int length = Math.max(left, right);
        maxLength = Math.max(maxLength, length);
        return length;
    }
}
```

Fibonacci Number (3 times):

[https://leetcode.com/problems/fibonacci-number/](https://leetcode.com/problems/fibonacci-number/)

Egg Drop With 2 Eggs and N Floors (3 times):

[https://leetcode.com/problems/egg-drop-with-2-eggs-and-n-floors/](https://leetcode.com/problems/egg-drop-with-2-eggs-and-n-floors/)

Add to Array-Form of Integer (3 times):

[https://leetcode.com/problems/add-to-array-form-of-integer/](https://leetcode.com/problems/add-to-array-form-of-integer/)

```java
class Solution {
    public List<Integer> addToArrayForm(int[] num, int k) {
        List<Integer> res = new ArrayList<>();
        int i = num.length - 1;
        while (i >= 0 || k > 0) {  
            if (i >= 0) k += num[i];  // since k is a number, can directly add digit to k
            res.add(k % 10);
            k /= 10;
            i--;
        } 
        Collections.reverse(res);
        return res;
    }
}
```

Design Circular Queue (3 times): use len

[https://leetcode.com/problems/design-circular-queue/](https://leetcode.com/problems/design-circular-queue/)

Single Element in a Sorted Array (3 times):

[https://leetcode.com/problems/single-element-in-a-sorted-array/](https://leetcode.com/problems/single-element-in-a-sorted-array/)

```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int left = 0;
        int right = nums.length -1;
        while (left < right) {
            int mid = left + (right - left) / 2; 
            if (mid % 2 == 1) mid--; // 1122344
            if (nums[mid] != nums[mid + 1]) {
                right = mid;
            } else left = mid + 2;
        }
        return nums[left];
    }
}
```

Sort Characters By Frequency (2 times): (bucket sort)

[https://leetcode.com/problems/sort-characters-by-frequency/](https://leetcode.com/problems/sort-characters-by-frequency/)

```java
public String frequencySort(String s) {
        
    if (s == null || s.isEmpty()) return s;
    
    // Count up the occurances.
    Map<Character, Integer> counts = new HashMap<>();
    for (char c : s.toCharArray()) {
        counts.put(c, counts.getOrDefault(c, 0) + 1);
    }
    
    int maximumFrequency = Collections.max(counts.values());
    
    // Make the list of buckets and apply bucket sort.
    List<List<Character>> buckets = new ArrayList<>();
    for (int i = 0; i <= maximumFrequency; i++) {
        buckets.add(new ArrayList<Character>());
    }
    for (Character key : counts.keySet()) {
        int freq = counts.get(key);
        buckets.get(freq).add(key);
    }

    // Build up the string. 
    StringBuilder sb = new StringBuilder();
    for (int i = buckets.size() - 1; i >= 1; i--) {
        for (Character c : buckets.get(i)) {
            for (int j = 0; j < i; j++) {
                sb.append(c);
            }
        }
    }
    return sb.toString();
}
```