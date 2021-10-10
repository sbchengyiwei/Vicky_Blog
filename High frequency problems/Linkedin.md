[215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int n = nums.length;
        int left = 0; 
        int right = n - 1;
        while(true) {
            int p = partition(nums, left, right);
            if (p + 1 == k) return nums[p];
            else if (p + 1 > k) right = p - 1;
            else left = p + 1;
        }
    }
    
    int partition(int[] nums, int left, int right) {
        int pivot = nums[left];  // 注意 pivot 是值 最后交换时候输入的是位置
        int l = left + 1;
        int r = right;
        while(l <= r) {
            if (nums[l] <= pivot && nums[r] >= pivot) swap(nums, l++, r--);
            else if (nums[l] >= pivot) l++; // 用 else if 因为这个时候已经移动 l 和 r 却没有判断是不是越界
            else if (nums[r] <= pivot) r--;
        }
        swap(nums, left, r);
        return r;
    }
    
    void swap(int[] nums, int l, int r) {
        int temp = nums[l];
        nums[l] = nums[r];
        nums[r] = temp;
    }
}
```



Factor Combinations (5 times): https://leetcode.com/problems/factor-combinations/

```java
class Solution {
    // O(n!)
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> list = new ArrayList<>();
    public List<List<Integer>> getFactors(int n) {
        
        if (n <= 3) return res;
        helper(n, 2, 1);
        return res;
    }
    void helper(int n, int start, int prod) {
        if(prod > n) return;
        if (prod == n) {
            res.add(new ArrayList(list));
            return;
        }
        for (int i = start; i <= n / prod; i++) {  //  i <= n / prod 缩小范围否则会超时
            if (n % i == 0 && i != n) { //since pro could be 1
            list.add(i);
            helper(n, i , prod * i);
            list.remove(list.size()-1);
            }
        }
    }
}
```



Longest Palindromic Subsequence (7 times): https://leetcode.com/problems/longest-palindromic-subsequence/ 

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        int n = s.length();
        int[][] dp = new int[n][n];

        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;   // 单个字母也是一个长度为一的字符串
            for(int j = i - 1; j >= 0; j--) {
                if (s.charAt(i) == s.charAt(j)) dp[i][j] = dp[i - 1][j + 1] + 2;
                else dp[i][j] = Math.max(dp[i][j + 1], dp[i - 1][j]);  //不包含i的和不包含j的都试试
            }
        }
        return dp[n - 1][0];
    }
}
```

Find K Pairs with Smallest Sums (7 times): https://leetcode.com/problems/find-k-pairs-with-smallest-sums/ 

```java
class Solution {
  // time (2k*logk)
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> res = new ArrayList<>();
        PriorityQueue<int[]> pq = new PriorityQueue<>((a,b) -> (
            nums1[a[0]] + nums2[a[1]] - nums1[b[0]] - nums2[b[1]]));
        for (int i = 0; i < nums1.length; i++) { // it is more convient if we put k pairs or len1 pairs in the pq
            pq.offer(new int[]{i, 0});
            if (pq.size() == k) break;
        }
        while(res.size() < k && !pq.isEmpty()) {   // can less than k pairs
            int[] cur = pq.poll();
            res.add(Arrays.asList(nums1[cur[0]], nums2[cur[1]]));
            if (cur[1] + 1 < nums2.length) {
                pq.offer(new int[]{cur[0], cur[1] + 1});
            }
        }
        return res;
    }
}

// index1, index2
// 0       0 (1)
// 1,      0 (4)
// 2,      0 (6)

// 0,      1 (2)
// 0,      2 (3)
// 1,      1 (5)
```

Maximum Profit in Job Scheduling (6 times): https://leetcode.com/problems/maximum-profit-in-job-scheduling/

```java
class Job {
    int s,e,p;
    public Job(int s, int e, int p) {
        this.s = s;
        this.p = p;
        this.e = e;
    }
}
class Solution {
    public int jobScheduling(int[] startTime, int[] endTime, int[] profit) {
        int n = startTime.length;
       
        Job[] jobs = new Job[n];
        for(int i = 0; i < n; i++){
            jobs[i] = new Job(startTime[i], endTime[i], profit[i]); 
        }
        //按 start 排序 然后从后往前遍历 每次都找 >= 当前 end 的最小值 然后更新 dp: max(选, 不选)
        Arrays.sort(jobs, (a, b) -> Integer.compare(a.s, b.s));
        
        int[] dp = new int[n];
        dp[n - 1] = jobs[n - 1].p;
        
        for(int i = n - 2; i >= 0; i--){
            int l = lowerbound(jobs, i, jobs[i].e);   // 传入 jobs 数组 因为 jobs 排序 但是 start 没有排序
            dp[i] = Math.max(jobs[i].p + (l < n ? dp[l] : 0), dp[i + 1]);
        }
        return dp[0];
    }
    
    int lowerbound(Job[] jobs, int i, int end) { 
            int l = i + 1, h = jobs.length;
            while(l < h){
                int m = l + (h - l) / 2;
                if(jobs[m].s >= end) h = m;
                else l = m + 1;
            }
        return l;
    }
}

```

Paint House (9 times): https://leetcode.com/problems/paint-house/

```java
class Solution {
    public int minCost(int[][] costs) {
        // 利用原来的数组简化 dp
        int[] preRow = costs[0];
        for (int i = 1; i < costs.length; i++) {
            int[] currentRow = costs[i];
            currentRow[0] += Math.min(preRow[1], preRow[2]); //这个地方和前面不能一样否则会干扰下面的结果
            currentRow[1] += Math.min(preRow[0], preRow[2]);
            currentRow[2] += Math.min(preRow[1], preRow[0]);
            preRow = currentRow;
        }
        return Math.min(preRow[0], Math.min(preRow[1], preRow[2])); //preRow 能传出来
    }
}

```

 Max Points on a Line (9 times): https://leetcode.com/problems/max-points-on-a-line/

```java
class Solution {
    public int maxPoints(int[][] points) {
        int n = points.length;
        int res = 1;
        for (int i = 0; i < n; i++) {
            Map<String, Integer> map = new HashMap<>();// 经过 ith point的不同斜率都有多少个  
            for(int j = i + 1; j < n; j++) {     // 从 i + 1 开始就可以 最多的那条线以谁为起点都可以 都能找到 
                int x1 = points[i][0], x2 = points[j][0], y1 = points[i][1], y2 = points[j][1];
                int a = x1 - x2, b = y1 - y2;
                int gcd = gcd(a, b);
                String slope = Integer.toString(a/gcd) + "/" + Integer.toString(b/gcd);
                map.put(slope, map.getOrDefault(slope, 0) + 1);
                res = Math.max(res,  map.get(slope) + 1);
            }
        }
        return res;
    }
    
    int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }
}
```

[243. Shortest Word Distance](https://leetcode.com/problems/shortest-word-distance/)

```java
class Solution {
    public int shortestDistance(String[] wordsDict, String word1, String word2) {
        int idx = -1;
        int res = wordsDict.length;
        for (int i = 0; i < wordsDict.length; i++) {
            if (wordsDict[i].equals(word1) || wordsDict[i].equals(word2)) {
                if(idx != -1 && !wordsDict[i].equals(wordsDict[idx] )) {
                    res = Math.min(res, i - idx);
                }
                idx = i;
            }
        }
        return res;
    }
}
```

[245. Shortest Word Distance III](https://leetcode.com/problems/shortest-word-distance-iii/)

> only add a || word1.equals(word2)

```java
class Solution {
    public int shortestWordDistance(String[] wordsDict, String word1, String word2) {
        int idx = -1;
        int res = wordsDict.length;
        for (int i = 0; i < wordsDict.length; i++) {
            if (wordsDict[i].equals(word1) || wordsDict[i].equals(word2)) {
                if(idx != -1 && (!wordsDict[i].equals(wordsDict[idx] )|| word1.equals(word2))) {
                    res = Math.min(res, i - idx);
                }
                idx = i;
            }
        }
        return res;
    }
}
```



[244. Shortest Word Distance II](https://leetcode.com/problems/shortest-word-distance-ii/submissions/)

> notice there may repeated words, so we need to resturn the minmum one
>
> and use 2 pointers is great for this problem  

```java
class WordDistance {
    HashMap<String, List<Integer>> word2index;
    public WordDistance(String[] wordsDict) {
        word2index = new HashMap<>();
        int index = 0;
        for (String word : wordsDict) {
            word2index.computeIfAbsent(word, k -> new ArrayList<>()).add(index); // already sorted by this way
            index++;
        }
    }
    
    public int shortest(String word1, String word2) {
        List<Integer> index1 = word2index.get(word1);
        List<Integer> index2 = word2index.get(word2);
        
        int p = 0, q = 0;
        int res = Integer.MAX_VALUE;
        while(p < index1.size() && q < index2.size()) {
            res = Math.min(res, Math.abs(index1.get(p) - index2.get(q)));
            if (index1.get(p) > index2.get(q))  q++;
            else p++;
        }
        return res;
    }
}

/**
 * Your WordDistance object will be instantiated and called as such:
 * WordDistance obj = new WordDistance(wordsDict);
 * int param_1 = obj.shortest(word1,word2);
 */
```

[53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

```java
class Solution {
    public int maxSubArray(int[] nums) {
        if(nums.length == 0) return -1;
        int sum = 0;
        int min = 0;//nothing put in 
        int res = Integer.MIN_VALUE;//res could be negative;
        for (int n : nums) {
            sum += n;
            res = Math.max(res, sum - min);
            min = Math.min(min, sum);
        }
        return res;
    }
}
```

Way2: Dynamic Programming, Kadane's Algorithm

> We need a general way to figure out when a part of the array is **worth** keeping. Whenever the sum of the array is negative, we know the entire array is not worth keeping, so we'll reset it back to an empty array.

```java
class Solution {
    public int maxSubArray(int[] nums) {
        // Initialize our variables using the first element.
        int currentSubarray = nums[0];
        int maxSubarray = nums[0];
        
        // Start with the 2nd element since we already used the first one.
        for (int i = 1; i < nums.length; i++) {
            int num = nums[i];
            // If current_subarray is negative, throw it away. Otherwise, keep adding to it.
            currentSubarray = Math.max(num, currentSubarray + num);
            maxSubarray = Math.max(maxSubarray, currentSubarray);
        }
        
        return maxSubarray;
    }
}
```

[339. Nested List Weight Sum](https://leetcode.com/problems/nested-list-weight-sum/)

```java
//DFS
class Solution {
    int res = 0;
    public int depthSum(List<NestedInteger> nestedList) {
        
        depthSum(nestedList, 1);
        return res;
    }
    
    void depthSum(List<NestedInteger> nestedList, int depth) {
        for (NestedInteger nest : nestedList) {
            if (nest.isInteger()) res+= nest.getInteger() * depth;
            else {
                 depthSum(nest.getList(), depth + 1);
            }
        } 
    }
    
}
```

![img](https://assets.leetcode.com/uploads/2021/01/14/nestedlistweightsumex1.png)

BFS:

```java
class Solution {
    public int depthSum(List<NestedInteger> nestedList) {
        Queue<NestedInteger> queue = new LinkedList<>();
        queue.addAll(nestedList);

        int depth = 1;
        int total = 0;

        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                NestedInteger nested = queue.poll();
                if (nested.isInteger()) {
                    total += nested.getInteger() * depth;
                } else {
                    queue.addAll(nested.getList());
                }
            }
            depth++;
        }
        return total;
    }
}
```



### Design

[706. Design HashMap](https://leetcode.com/problems/design-hashmap/)

> There are two main issues that we should tackle, in order to design an *efficient* hashmap data structure: *1). hash function design* and *2). collision handling*.
>
> !!! key(not same) is different with hashcode(can be same)
>
> Array -> hold the buckets
>
> buket -> is LinkedList

```java
class MyHashMap {

    class Pair<U, V> {
      public U first;
      public V second;

      public Pair(U first, V second) {
        this.first = first;
        this.second = second;
      }
    }
    
    //集成一个 bucket 注意 get remove add 要在 bucket 里写
    class Bucket {
      private List<Pair<Integer, Integer>> bucket; //List 有 remove add 这些方法 

      public Bucket() {
        this.bucket = new LinkedList<Pair<Integer, Integer>>();
      }

      public Integer get(Integer key) {
        for (Pair<Integer, Integer> pair : bucket) {
          if (pair.first.equals(key))
            return pair.second;
        }
        return -1;
      }

      public void put(Integer key, Integer value) {
        boolean found = false;
        for (Pair<Integer, Integer> pair : bucket) {
          if (pair.first.equals(key)) {
            pair.second = value;
            found = true;
          }
        }
        if (!found)
          bucket.add(new Pair<Integer, Integer>(key, value));
      }

      public void remove(Integer key) {
        for (Pair<Integer, Integer> pair : bucket) {
          if (pair.first.equals(key)) {
            bucket.remove(pair);
            break;
          }
        }
      }
    }
    
    
    private int key_space;
    private Bucket[] hash_table;
    
    private int hash(int key) {
        return key % key_space;
    }

    /** Initialize your data structure here. */
    public MyHashMap() {
      key_space = 2069;
      hash_table = new Bucket[key_space];
      Arrays.fill(hash_table, new Bucket());
    }  

    /** value will always be non-negative. */
    public void put(int key, int value) {
      
      hash_table[hash(key)].put(key, value);
    }
  
    /**
     * Returns the value to which the specified key is mapped, or -1 if this map contains no mapping
     * for the key
     */
    public int get(int key) {
      
      return hash_table[hash(key)].get(key);
    }

    /** Removes the mapping of the specified value key if this map contains a mapping for the key */
    public void remove(int key) {
     
      hash_table[hash(key)].remove(key);
    }
}

/**
 * Your MyHashMap object will be instantiated and called as such: MyHashMap obj = new MyHashMap();
 * obj.put(key,value); int param_2 = obj.get(key); obj.remove(key);
 */
```

[705. Design a HashSet](https://leetcode.com/problems/design-hashset/)

```java
class MyHashSet {
  class Bucket {
      private LinkedList<Integer> bucket;

      public Bucket() {
        bucket = new LinkedList<Integer>();
      }
      
      public boolean exists(Integer key) {
        return bucket.contains(key);
      }

      public void insert(Integer key) {
       
        if (!exists(key)) {
          bucket.addFirst(key);
        }
      }

      public void delete(Integer key) {
        bucket.remove(key);
      }


  }
  private Bucket[] bucketArray;
  private int keyRange;

  /** Initialize your data structure here. */
  public MyHashSet() {
    keyRange = 769;
    bucketArray = new Bucket[keyRange];
    for (int i = 0; i < keyRange; ++i)
      bucketArray[i] = new Bucket();
  }

  protected int hash(int key) {
    return (key %keyRange);
  }

  public void add(int key) {
    
    this.bucketArray[hash(key)].insert(key);
  }

  public void remove(int key) {
    
    this.bucketArray[hash(key)].delete(key);
  }

  /** Returns true if this set contains the specified element */
  public boolean contains(int key) {

    return this.bucketArray[hash(key)].exists(key);
  }
}
```



[380. Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/)

```java
class RandomizedSet {
    Map<Integer, Integer> map;
    List<Integer> list;
    Random random;
    public RandomizedSet() {
        map = new HashMap<>();
        list = new ArrayList<>();
        random = new Random();
    }
    //bool insert(int val) Inserts an item val into the set if not present. Returns true if the item was not present, false otherwise.
    public boolean insert(int val) {
        if (map.containsKey(val)) {
            return false;
        }
        list.add(val);
        map.put(val, list.size() - 1);
        return true;
    }
    
    public boolean remove(int val) {
         if (!map.containsKey(val)) {
            return false;
        }
        int index = map.remove(val);
        if (index != list.size() - 1) {
            int lastVal = list.get(list.size() - 1);
            list.set(index, lastVal);
            map.put(lastVal, index);
        }

        list.remove(list.size() - 1);
        return true;
    }
    
    public int getRandom() {
        return list.get(random.nextInt(list.size()));
    }
}
```

[Max Stack](https://leetcode.com/problems/max-stack/)

```java
class MaxStack {
    Stack<Integer> stack;
    Stack<Integer> maxStack;

    public MaxStack() {
        stack = new Stack();
        maxStack = new Stack();
    }

    public void push(int x) {
 
        if (maxStack.isEmpty() || maxStack.peek() <= x) maxStack.push(x);
        stack.push(x);
    }

    public int pop() {
        int n= stack.pop();
        if(maxStack.peek() == n) maxStack.pop();
        return n;
    }

    public int top() {
        return stack.peek();
    }

    public int peekMax() {
        return maxStack.peek();
    }

    public int popMax() {
        int max = maxStack.pop();
        Stack<Integer> buffer = new Stack();
        while (stack.peek() != max) buffer.push(stack.pop());
        stack.pop();
        while (!buffer.isEmpty()) push(buffer.pop()); //using push not just stack.push(); coz when the max is pop() the following numbers will have the next change to be the next max;
        return max;
    }
}
```

[146. LRU Cache](https://leetcode.com/problems/lru-cache/)

```java
public class LRUCache {

  class DLinkedNode {
    int key;
    int value;
    DLinkedNode prev;
    DLinkedNode next;
  }

  private void addNode(DLinkedNode node) {
    /**
     * Always add the new node right after head.
     */
    node.prev = head;
    node.next = head.next;

    head.next.prev = node;
    head.next = node;
  }

  private void removeNode(DLinkedNode node){
    /**
     * Remove an existing node from the linked list.
     */
    DLinkedNode prev = node.prev;
    DLinkedNode next = node.next;

    prev.next = next;
    next.prev = prev;
  }

  private void moveToHead(DLinkedNode node){
    /**
     * Move certain node in between to the head.
     */
    removeNode(node);
    addNode(node);
  }

  private DLinkedNode popTail() {
    /**
     * Pop the current tail.
     */
    DLinkedNode res = tail.prev;
    removeNode(res);
    return res;
  }

  private Map<Integer, DLinkedNode> cache = new HashMap<>();
  private int size;
  private int capacity;
  private DLinkedNode head, tail;

  public LRUCache(int capacity) {
    this.size = 0;
    this.capacity = capacity;

    head = new DLinkedNode();
    // head.prev = null;

    tail = new DLinkedNode();
    // tail.next = null;

    head.next = tail;
    tail.prev = head;
  }

  public int get(int key) {
    DLinkedNode node = cache.get(key);
    if (node == null) return -1;

    // move the accessed node to the head;
    moveToHead(node);

    return node.value;
  }

  public void put(int key, int value) {
    DLinkedNode node = cache.get(key);

    if(node == null) {
      DLinkedNode newNode = new DLinkedNode();
      newNode.key = key;
      newNode.value = value;

      cache.put(key, newNode);
      addNode(newNode);

      ++size;

      if(size > capacity) {
        // pop the tail
        DLinkedNode tail = popTail();
        cache.remove(tail.key);
        --size;
      }
    } else {
      // update the value.
      node.value = value;
      moveToHead(node);
    }
  }
}
```

[432. All O`one Data Structure](https://leetcode.com/problems/all-oone-data-structure/)

> 把 map连接下的事情都干完了再改变/删除这个 map的连接

```java
class AllOne {
    
    class Node {
        int val;
        HashSet<String> set;
        Node pre; // doubly linkedList
        Node next;
        public Node(int val) {
            this.val = val;
            this.set = new HashSet<>();
        }
    }
    
    HashMap<String, Node> map;
    Node head;
    Node tail;
    
    
    public AllOne() {
        this.map = new HashMap<>();
        head = new Node(0);
        tail = new Node(-1);
        head.next = tail;
        tail.pre = head;
    }
    
    //remove
    void remove(Node node) {
        node.pre.next = node.next;
        node.next.pre = node.pre;
    }
    //addNext
    void addNext(Node node){
        Node nextNode = new Node(node.val + 1);
        nextNode.pre = node;
        nextNode.next = node.next;
        node.next.pre = nextNode;
        node.next = nextNode;   
        
    }
    //addPre
    void addPre(Node node) {
        Node nextNode = new Node(node.val - 1);
        nextNode.next = node;
        nextNode.pre = node.pre;
        node.pre.next = nextNode;
        node.pre = nextNode;     
    }
    
    
    public void inc(String key) {
    
        if (map.containsKey(key)) {

                if (map.get(key).val != map.get(key).next.val-1)  addNext(map.get(key));

                map.get(key).next.set.add(key);
                map.get(key).set.remove(key);
            if (map.get(key).set.size() == 0) remove(map.get(key));
            //删干净了才换家
                map.put(key, map.get(key).next);
               
                
            
        } else { // not contain
            Node node;
            if (head.next.val == 1) {
                node = head.next;
            } else {
                addNext(head);
             
                node = head.next;
            }
            node.set.add(key);
            map.put(key, node);
        }
    }
    
    public void dec(String key) {
        if (map.containsKey(key)) {
            if (map.get(key).val == 1) {
                
                map.get(key).set.remove(key);
                
                if(map.get(key).set.size() == 0) remove(head.next);
                //删干净了才离开
                map.remove(key);
            } else {
                if (map.get(key).val != map.get(key).pre.val+1)  addPre(map.get(key));
                map.get(key).pre.set.add(key);
                map.get(key).set.remove(key);
                if (map.get(key).set.size() == 0) remove(map.get(key));
                
                map.put(key, map.get(key).pre);
                
                
            }
        } 
    }
    
    public String getMaxKey() {

        Node maxNode = tail.pre;
        if (maxNode.val == 0) return "";
        return maxNode.set.iterator().next();
    }
    
    public String getMinKey() {
          Node minNode = head.next;
        if (minNode.val == -1) return "";
        return minNode.set.iterator().next();
    }
}

/**
HashMap<String, Node>
DLinkedList<Node> -> Node (int val, HashSet<String> set)

 */

```



### Tree

[366. Find Leaves of Binary Tree](https://leetcode.com/problems/find-leaves-of-binary-tree/)

```java
    public List<List<Integer>> findLeaves(TreeNode root) {
        
        dfs(root);
        return res;
    }
    // return the height of the node
    int dfs(TreeNode node) {
        if (node == null) return -1;
        //formally, height(root)=1+max(height(root.left), height(root.right))the hei
        int height = Math.max(dfs(node.left), dfs(node.right)) + 1;
        // the first time is equal to the height, res add a new ArrayList
        if (res.size() == height) res.add(new ArrayList<>());
        // add the node to the conresponding position
        res.get(height).add(node.val);
        return height;
    }
    
}
```

[156. Binary Tree Upside Down](https://leetcode.com/problems/binary-tree-upside-down/)

```java
class Solution {
    TreeNode newHead = null;
    public TreeNode upsideDownBinaryTree(TreeNode root) {
        if (root == null) return root;
        dfs(root);
        //! the root node will turn to a leaf, remember set the left and right to the null
        root.left = null;
        root.right = null;
        return newHead;
    }
    
    void dfs(TreeNode root) {
        if(root.left == null) { 
            newHead = root;
            return;
        }
        dfs(root.left);
        //root is like the pre, we not go the cur node and set it, instead we use the pre
        root.left.right = root;
        root.left.left = root.right; 
    }
}

//more simplfy
class Solution {
    public TreeNode upsideDownBinaryTree(TreeNode root) {
        if (root == null || root.left == null) return root;
        TreeNode newHead = upsideDownBinaryTree(root.left);
        root.left.right = root;
        root.left.left = root.right; 
        //! the root node will turn to a leaf, remember set the left and right to the null
        root.left = null;
        root.right = null;
        
        return newHead;
    }
}
```

[297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

```java
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder res = new StringBuilder();
        Queue<TreeNode> queue = new LinkedList<>(); // add Node ; LinkedList
        queue.offer(root);
        while(!queue.isEmpty()) {
            TreeNode cur = queue.poll();
            if (cur != null) {
                res.append(" ");   // use space
                res.append(cur.val);
                queue.offer(cur.left);
                queue.offer(cur.right);
            } else {
                res.append(" ");
                res.append("null");
            }
        }
        res.deleteCharAt(0);  // deleteCharAt
        return res.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        String[] nodes = data.split(" ");
        if (nodes.length == 1) return null;
        Queue<TreeNode> queue = new LinkedList<>();
        TreeNode root = new TreeNode(Integer.parseInt(nodes[0]));
        queue.offer(root);
        int index = 1;
        while(!queue.isEmpty()) {
            TreeNode cur = queue.poll();
            if (!nodes[index].equals("null")) {
                cur.left = new TreeNode(Integer.parseInt(nodes[index])); // use parseInt
                queue.offer(cur.left);
            }
            index++;
            if (!nodes[index].equals("null")) {
                cur.right = new TreeNode(Integer.parseInt(nodes[index]));
                queue.offer(cur.right);
            }
            index++;
         }
        return root;
    }
}
```



Closest Binary Search Tree Value II (6 times): https://leetcode.com/problems/closest-binary-search-tree-value-ii/

```java
// 1. not serialize to an array: use pq  ->  T:nlogk Space:k
class Solution {
    PriorityQueue<Integer> pq;
    public List<Integer> closestKValues(TreeNode root, double target, int k) {
        pq = new PriorityQueue<>((o1, o2) -> Math.abs(o1 - target) > Math.abs(o2 - target) ? -1 : 1);
        inorder(root, k);
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            res.add(pq.poll());
        }
        return res;
    }
    public void inorder(TreeNode r, int k) {
        if (r == null) 
            return;
                    
        inorder(r.left, k);
        pq.offer(r.val);
        if(pq.size() > k) pq.poll();
        inorder(r.right, k);
    }  
}
2.serialize : quick selection   ->  Time: avg: O(n) worst: O(n^2) Space:n

class Solution {
    List<Integer> nums;
    public List<Integer> closestKValues(TreeNode root, double target, int k) {
        nums = new ArrayList();  
        inorder(root);
        int left = 0; 
        int right = nums.size() - 1;

        while(true) {
            int pivot = quickSelect(left, right, target);
            if (pivot + 1 == k) {
                break;
            } else if (pivot + 1 > k) {
                right = pivot - 1;
            } else {
                left = pivot + 1;
            }
        }
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i <= k -1; i++) {
            res.add(nums.get(i));
        }
        return res;   
    }
    
    int quickSelect(int left, int right, double target) {
        int pivot = nums.get(right);
        int l = left;
        int r = right - 1;
        while(l <= r) {
            if (Math.abs(nums.get(l) - target) <= Math.abs(pivot- target) && Math.abs(nums.get(r) - target) >= Math.abs(pivot - target)) {
                swap(l++, r--);
            }
            else if (Math.abs(nums.get(l) - target) <= Math.abs(pivot- target)) l++;
            else if (Math.abs(nums.get(r) - target) >= Math.abs(pivot- target)) r--;
        }
        swap(right, l);
        return l;
    }
    
    void swap(int l, int r) {
        int temp = nums.get(l);
        nums.set(l,nums.get(r));
        nums.set(r, temp);
    }
    
    public void inorder(TreeNode r) {
        if (r == null) 
            return;
                    
        inorder(r.left);
        nums.add(r.val);
        inorder(r.right);
    }      
}

//3.serialize two pointers   ->  Time: O(n) Space : O(n)
class Solution {
    List<Integer> nums;
    public List<Integer> closestKValues(TreeNode root, double target, int k) {
        nums = new ArrayList();  
        inorder(root);
        int left = 0; 
        int right = nums.size() - 1;
        while (right - left + 1 > k) {
            if (Math.abs(nums.get(left) - target) > Math.abs(nums.get(right) - target)) {
                left++;
            } else right--;
        }
        List<Integer> res = new ArrayList<>();
        for (int i = left; i <= right; i++) {
            res.add(nums.get(i));
        }
        return res;   
    }
    
    public void inorder(TreeNode r) {
        if (r == null) 
            return;
                    
        inorder(r.left);
        nums.add(r.val);
        inorder(r.right);
    }  
}
```

Lowest Common Ancestor of a Binary Tree III (5 times): https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iii/

```java
//Same to the 'Find the overlap of two LinkedList'
class Solution {
    public Node lowestCommonAncestor(Node p, Node q) {
        Node pointer1 = p;
        Node Pointer2 = q;
        while(pointer1 != pointer2) {
            if (pointer1.parent == null) pointer1 = q; 
            else pointer1 = pointer1.parent;
            if (pointer2.parent == null) pointer2 = p;
            else pointer2 = pointer2.parent;
        }
        return pointer1;
    }
}

/*                       p1
    5 -> 3  -> 8  ->1 -> 3
8 ->1 -> 3  -> 5 -> 3 
                  p2
*/

//easy but bad, check a lot of node which no need to
class Solution {
    public Node lowestCommonAncestor(Node p, Node q) {
        if (hasNode(p, q)) return p;
        else if (hasNode(q, p)) return q;
        while(true) { 
            //more efficient is to check is p is the left child or right child so we don't need to check the p's part
            if (hasNode(p.parent, q)) return p.parent;
            p = p.parent;
        }
    }
    boolean hasNode(Node root, Node target) {       
        if (root == null) return false;
        if (root == target) return true;
        return hasNode(root.left, target) || hasNode(root.right, target);
    }
}
```

