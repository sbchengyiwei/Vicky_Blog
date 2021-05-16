# LeetCode -- Trie

## 1 Principle

### 1.1 What is a trie tree
The following definition is from Wikipedia.

> In computer science, trie, also known as prefix tree or dictionary tree, is an ordered tree used to store associative arrays, in which the key is usually a string. Different from binary search tree, the key is not directly saved in the node, but determined by the location of the node in the tree. All descendants of a node have the same prefix, that is, the string corresponding to the node, while the root node corresponds to an empty string. In general, not all nodes have corresponding values, only the key corresponding to the leaf node and some internal nodes have related values.

A simple TRIE structure is shown below:

<div align=center><img src="https://github.com/sbchengyiwei/sbchengyiwei.github.io/blob/main/images/1621092353806.png" width="350" height="200" /></div>


### 1.2 Advantage
1. The efficiency of insertion and query is very high, both are O(m), where m is the length of the string to be inserted/queried.

2. Different keywords in the Trie tree will not conflict.

3. Trie trees can only have similar hash collisions when a keyword is allowed to associate multiple values.

4. Trie tree does not need to find hash value, it has faster speed for **short string**. Usually, it is necessary to traverse the string to find the hash value.

5. Trie tree can sort keywords in lexicographic order.

## 2 Build and Basic Operation

### 2.1 add() & contains() & starWith() & search()

```java
class TrieNode{
    TrieNode[] children;
    String word;
    boolean isWord; 
    /** Initialize. */
    public TrieNode() {
        children = new TrieNode[26];
        word = null;
        isWord = false;
    }
}

class Trie {
    TrieNode root;
    /** Initialize. */
    public Trie() {
        root = new TrieNode();
    }
    /** Add a word. */
    public void add(String word) {
        TrieNode cur = root;
        for (char c : word.toCharArray()) {
            if (cur.children[c - 'a'] == null) {
                cur.children[c - 'a'] == new TrieNode();
            }
            cur = cur.children[c - 'a'];
        }
        cur.isWord = true;
    }
    /** Contains word (exactly). */
    public boolean contains(String word) {
        TrieNode cur = root;
        for (char c : word.toCharArray()) {
            if (cur.children[c - 'a'] == null) {
                return false;
            }
            cur = cur.children[c - 'a'];
        }
        return cur.isWord;
    }
    /** StartWith a prefix. */
    public boolean startWith(String prefix) {
        TrieNode cur = root;
        for (char c : prefix).toCharArray()) {
            if (cur.children[c - 'a'] == null) {
                return false;
            }
            cur = cur.children[c - 'a'];
        }
        return true;    
    }
    /** Search a word. */
    public boolean search (String word) {
        return search (root, word, 0);
    }

    private boolean search (TrieNode cur, String word, int index) {
        if (index == word.length()) {
            return cur.isWord;
        }
        if (word.charAt(index) == '.' ) {
            for (TrieNode temp : cur.children) {
                if (temp != null && search(temp, word, index + 1)) {
                    return true;
                }
            }
            return false;
        } else {
            char c = word.charAt(index);
            TrieNode temp = cur.children[c - 'a'];
            return temp != null && search(temp, word, index + 1);
        }
    }
}


```

### 2.2 LeetCode 208 Implement Trie (Prefix Tree)

```java
class TrieNode {
    TrieNode[] children;
    boolean isWord;

    public TrieNode() {
        children = new TrieNode[26];
        isWord = false;
    }
}

class Trie {
    
    TrieNode root;

    /** Initialize your data structure here. */
    public Trie() {
        root = new TrieNode();
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {
            if (node.children[word.charAt(i) - 'a'] == null) {
                node.children[word.charAt(i) - 'a'] = new TrieNode();
            }
            node = node.children[word.charAt(i) - 'a'];
        }
        node.isWord = true;
        
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {
            if (node.children[word.charAt(i) - 'a'] == null) return false;
            node = node.children[word.charAt(i) - 'a'];
        }
        return node.isWord;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        TrieNode node = root;
        for (int i = 0; i < prefix.length(); i++) {
            if (node.children[prefix.charAt(i) - 'a'] == null) return false;
            node = node.children[prefix.charAt(i) - 'a'];
        }
        return true;
    }
}
```





## 3 Application

### 3.1 LeetCode 720 Longest Word in Dictionary (EASY)

TrieNode + DFS

```java

class TrieNode{
    TrieNode[] children;
    String word;
    public TrieNode() {
        children = new TrieNode[26];
        word = null;
    }
}

class Solution {
    // TrieNode + DFS : find the longest child (word).
    public String longestWord(String[] words) {
        TrieNode root = new TrieNode();
        for (String word : words) {
            insert(root, word);
        }
        return find(root);
    }
    
    //build the TrieNode
    private void insert (TrieNode root, String word) {
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {
            if (node.children[word.charAt(i) - 'a'] == null) {
                node.children[word.charAt(i) - 'a'] = new TrieNode();
            }
            node = node.children[word.charAt(i) - 'a'];
        }
        node.word = word;
        
    }
    
    // find the longest lexicographical child
    private String find(TrieNode root) {
        String res = root.word == null ? "" : root.word;
        for (TrieNode child : root.children) {
            if (child == null || child.word == null) {
                continue;
            }
            String word = find(child);
            res = word.length() > res.length() ? word : res;
            // only when word longer than res, res will update. So the res will be lexicographical. 
        }
        return res;
        
    }
}
```


### 3.2 LeetCode 692 Top K Frequent Words (MIDDLE)

Solution1 : HashMap + PriorityQueue
注意` compareTo` 和`- `相比是`一样`的顺序！！！

```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> map = new HashMap<>();
        for(String word: words) {
            map.put(word, map.getOrDefault(word, 0) + 1);
        }
        
        PriorityQueue<String> pq = new PriorityQueue<>((s1, s2) -> {
            if(map.get(s1) == map.get(s2)) {
                return s1.compareTo(s2);
            } else {
                return map.get(s2) - map.get(s1);   
            }
        });
        
        for(String word: map.keySet()) {
            pq.offer(word);
        }
        
        List<String> result = new ArrayList<>();
        for(int i = 0; i < k; i++) {
            result.add(pq.remove());
        }
        return result;
    }
}
```


Solution 2: bucket sort + trie

```java
class TrieNode{
    TrieNode[] children;
    String word;
    public TrieNode() {
        children = new TrieNode[26];
        word = null;
    }
}

class Solution {
    //time : O(n) ; space: O(number of words * word.length() * 26)
    public List<String> topKFrequent(String[] words, int k) {
        
        Map<String, Integer> map = new HashMap<>();
        for(String word: words) {
            map.put(word, map.getOrDefault(word, 0) + 1);
        }
        // 0 - len freqs
        TrieNode[] count = new TrieNode[words.length + 1];
        for (String word : map.keySet()) {
            int freq = map.get(word);
            if (count[freq] == null) {
                count[freq] = new TrieNode();
            }
            insert(count[freq], word);
        }
        
        List<String> res = new LinkedList<>();
        for (int f = count.length - 1; f >= 1 && res.size() < k; f--) {
            if (count[f] == null) {
                continue;
            }
            getWords(count[f], res, k);
        }
        return res;
    }
    
    
    private void getWords(TrieNode node, List<String> list, int k) {
        if (node == null) {
            return;
        }
        if (node.word != null) {
            list.add(node.word);
        }
        if (list.size() == k) {
            return;
        }
        for (int i = 0; i < node.children.length; i++) {
            if (node.children[i] != null) {
                getWords(node.children[i], list, k);
            }
        }
    }
    
    // add words into trie
     //time : O(n) ; space: O(number of words * word.length() * 26)
    private void insert (TrieNode root, String word) {
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {
            if (node.children[word.charAt(i) - 'a'] == null) {
                node.children[word.charAt(i) - 'a'] = new TrieNode();
            }
            node = node.children[word.charAt(i) - 'a'];
        }
        node.word = word;
        
    }
    

}
```

### 3.3 LeetCode 421 Maximum XOR of Two Numbers in an Array (MIDDLE)

Solution1: HashSet + Greedy Algorithm (from the highest bit)
Applied Principle：set temp(greedy try) if `a^temp = b`  (a & b in set) then `a^b = temp` , temp is res.

```java

class Solution {
    //time : O(n) ; space : O(n)
    public int findMaximumXOR(int[] nums) {
        int res = 0;
        int mask = 0;
        for (int i = 31; i >= 0; i--) {
            // Mask : to keep/change/remove a desired part of information.
            // Mask is all 1 in the former i part, and all 0 in the later part.
            mask = mask | (1 << i);
            Set<Integer> set = new HashSet<>();
            for (int num : nums) {
                // Notice：use & to keep 1 of num in the former i part,
                set.add(num & mask);
            }
            // Here first assume that the n-th bit is 1, and the first n-1 bit res is obtained by the previous iteration.
            int temp = res | (1 << i);
            for (Integer prefix : set) {
                // Using the property a ^ b = c, then a ^ c = b, and b ^ c = a
                if (set.contains(prefix ^ temp)) {
                    res = temp;
                    break;
                }
            }
        }
        return res;
    }
}
```

This picture will better describe this process:

![](https://sbchengyiwei.github.io//post-images/1621131472057.jpg)

Solution2: Trie + Greedy Algorithm (from the highest bit)
Search the best xor for each num

```java
class TrieNode {
        TrieNode[] next;

        public TrieNode() {
            next = new TrieNode[2];
        }
    }

class Solution {
    //time : O(n) ; space : O(number of words * word.length() * 26)
    public int findMaximumXOR(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        TrieNode root = new TrieNode();
        // Build the trie.
        for (int num : nums) {
            TrieNode node = root;
            for (int i = 31; i >= 0; i--) {
                //See whether the bit of cur is 1 or 0, then build the node accordingly.
                int cur = (num >>> i) & 1;
                if (node.next[cur] == null) {
                    node.next[cur] = new TrieNode();
                }
                node = node.next[cur];
            }
        }
        int res = 0;
        // Search the largest xor for each num, then save to res and compare.
        for (int num : nums) {
            TrieNode node = root;
            int xor = 0;
            for (int i = 31; i >= 0; i--) {
                int cur = (num >>> i) & 1;
                if (node.next[cur ^ 1] != null) {
                    xor |= (1 << i);
                    node = node.next[cur ^ 1];
                } else {
                    node = node.next[cur];
                }
            }
            res = Math.max(res, xor);
        }
        return res;
    }
}
```

### 3.4 LeetCode 212 Word Search II (HARD)

Trie + DFS

```java
class TrieNode {
    TrieNode[] next;
    String word;

    public TrieNode() {
        next = new TrieNode[26];
        word = null;
    }
}

class Solution {
    public List<String> findWords(char[][] board, String[] words) {
        List<String> res = new ArrayList<String>();
        TrieNode root = buildTrie(words);
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                dfs(board, i, j , res, root);
            }
        }
        return res;
    }
    
    private TrieNode buildTrie(String[] words) {
        TrieNode root = new TrieNode();
        for (String word : words) {
            TrieNode node = root;
            for (char c : word.toCharArray()) {
                if (node.next[c - 'a'] == null) {
                    node.next[c - 'a'] = new TrieNode();
                }
                node = node.next[c - 'a'];
            }
            node.word = word;
        }
        
        return root;
    }
    
    private void dfs(char[][] board, int i, int j, List<String> res, TrieNode node) {
        if (i < 0 || j < 0 || i >= board.length || j >= board[0].length) return;
        char c = board[i][j];
        if (c == '#' || node.next[c - 'a'] == null) return;
        node = node.next[c - 'a'];
        if (node.word != null) {
            res.add(node.word);
            node.word = null;
        }
        // only use one time in a word;
        board[i][j] = '#';
        dfs(board, i-1, j , res, node);
        dfs(board, i+1, j , res, node);
        dfs(board, i, j+1, res, node);
        dfs(board, i, j-1 , res, node);
        board[i][j] = c;
    }
}
```
