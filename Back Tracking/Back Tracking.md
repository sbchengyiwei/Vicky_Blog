

## What is Backtracking

Backtracking is an algorithmic-technique for solving problems recursively by trying to build a solution incrementally, one piece at a time, removing those solutions that fail to satisfy the constraints of the problem at any point of time (by time, here, is referred to the time elapsed till reaching any level of the search tree). 

According to the wiki definition, 

> **Backtracking** can be defined as a general algorithmic technique that considers searching every possible combination in order to solve a computational problem. 
>  

**Time complexity** calculation is based on the number of enumerations, that is, by looking at the mathematical formula. (unlike DFS, Backtracking does not walk the entire tree, but prunes as needed.) 



## Patterns

### Pattern1: Subset

#### [78. Subsets](https://leetcode-cn.com/problems/subsets/)

1. index 控制开始的数字 循环中list 加入 i 
2. i + 1 作为新的 index 递归
3. res 要**新定义**一个ArrayList<>(list) 
4. 记得 return

```java
class Solution {
   //time : O(n * 2^n)  space : O(n)
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if(nums == null || nums.length == 0) return res;
        helper(nums, res, new ArrayList<>(), 0);
        return res;
    }

    private void helper(int[] nums, List<List<Integer>> res, List<Integer> list, int index) {
        
        res.add(new ArrayList<>(list));
        for (int i = index; i < nums.length; i++) {
            list.add(nums[i]);
            helper(nums, res, list, i + 1);
            list.remove(list.size() - 1);
        }
    }
}
```



- **字符串**

#### [131. Palindrome Partitioning](https://leetcode-cn.com/problems/palindrome-partitioning/)

```java
class Solution {
    //time : O(n * 2^n)  space : O(n)
    public List<List<String>> partition(String s) {
        List<List<String>> res = new ArrayList<List<String>>();
        List<String> list = new ArrayList<String>();
        helper(res, list, s);
        return res;
    }
    
    private void helper(List<List<String>> res, List<String> list, String s) {
        if (s.length() == 0) {
            res.add(new ArrayList<>(list));
            return;
        }
        for (int i = 0; i < s.length(); i++) { //这里是 0 但是和 index 是一个原理 字符串和数组不一样 传入的字符串是已经剪切好的了
            if (isPalindrome(s.substring(0, i + 1))) {
                list.add(s.substring(0, i + 1));
                helper(res, list, s.substring(i + 1));
                list.remove(list.size() - 1);
            }
        }
    }
    
    private boolean isPalindrome(String s) {
        int left = 0;
        int right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return false;
            }
            left++; right--;
        }
        return true;
    }
}
```



### Pattern2: Conbiantion

#### [77. Combinations](https://leetcode-cn.com/problems/combinations/)

1. index 控制开始的数字 循环中list 加入 i 
2. i + 1 作为新的 index 递归
3. k 控制组合的长度，或者说k代表组合中填入的位置
4. res 要**新定义**一个ArrayList<>(list) 
5. 记得 return

> Given two integers n and k, return all possible combinations of k numbers out of the range [1, n]. You may return the answer in any order.

```java
class Solution {
    //time : O(n*(n k))  space : O(n)
    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> res = new ArrayList<>();
        helper(res, new ArrayList<>(), k, n, 1);
        return res;
    }

    private void helper( List<List<Integer>> res,  List<Integer>list, int k, int n, int index) {
        if (k == 0) { 
            res.add(new ArrayList<>(list));
            return;
        }
        for (int i = index; i <= n; i++) {
            list.add(i);
            helper(res, list, k - 1, n, i + 1);
            list.remove(list.size() - 1);
        }
    }
}
```



- **字符串** +.. 传入递归不用再remove

没有 index 也是因为不同的 k 传入不同的字符串 相当于自动变 index 了

#### [17. Letter Combinations of a Phone Number](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

```java
class Solution {
    public List<String> letterCombinations(String digits) {
        List<String> res = new ArrayList<>();
        if (digits == null || digits.length() == 0 ) return res;
        String[] input = new String[]{"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"}; 
        helper(digits, input, res, "", 0); 
        return res;
    }
    
    private void helper(String digits, String[] input, List<String> res,  String s, int k) {
        if (k == digits.length()) { 
            res.add(s);
            return;
        }
   
        for (int i = 0; i < input[digits.charAt(k) - '0'].length(); i++){
            helper(digits, input, res, s + input[digits.charAt(k) - '0'].charAt(i) , k + 1);
        }
    }
}
/*
  复杂度分析：
  时间复杂度：O(3^m * 4^n) 其中 m 是输入中对应 3 个字母的数字个数（包括数字 2、3、4、5、6、8），nn 是输入中对应 4 个字母的数字个数（包括数字 7、9），m+n 是输入数字的总个数。当输入包含 m 个对应 3 个字母的数字和 n 个对应 4 个字母的数字时，不同的字母组合一共有 3^m * 4^n 种，需要遍历每一种字母组合。

  空间复杂度：O(m+n)，除了返回值以外，空间复杂度主要取决于哈希表以及回溯过程中的递归调用层数，哈希表的大小与输入无关，可以看成常数，递归调用层数最大为 m+n。
*/
```



### Pattern3: Conbiantion Sum

#### [39. Combination Sum](https://leetcode-cn.com/problems/combination-sum/)

上面的 k 换成 sum 的剩余值

```java
class Solution {
    //time : O(n * 2^n)  space : O(n)
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if (candidates == null || candidates.length == 0) return res;
        helper(candidates, target, res, new ArrayList<>(), 0, 0);
        return res;
    }
    private void helper(int[] candidates, int target, List<List<Integer>> res, ArrayList<Integer> list, int sum, int start) {
        if (sum > target) return;
        if (sum == target) {
            res.add(new ArrayList<>(list));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            list.add(candidates[i]);
            helper(candidates, target, res, list, sum + candidates[i], i);
            list.remove(list.size() - 1);
        }
    }
}
```



#### [216. Combination Sum III](https://leetcode-cn.com/problems/combination-sum-iii/)

同 77 加了一个`n == 0` 的条件

```java
class Solution {
    //time : O(n * 2^n)  space : O(n)
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> res = new ArrayList<>();
        helper(res, new ArrayList<>(), n, k, 1);
        return res;
    }
    
    private void helper(List<List<Integer>> res, List<Integer> list, int n, int k, int start) {
        if (k == 0 && n ==0) {
            res.add(new ArrayList<>(list));
            return;
        }

        for (int i = start; i <= 9; i++) {
            list.add(i);
            helper(res, list, n - i , k -1 , i + 1);
            list.remove(list.size() - 1);
        }
    }
}
```



### Pattern4: Permutation

#### [46. Permutations](https://leetcode-cn.com/problems/permutations/)

1. 没有 index 和 k，是判断 nums.length 和 list.size() 是不是相等才返回，有的时候是判断 left 和 right、start 和 end 的关系
2. res 要**新定义**一个ArrayList<>(list)
3. 记得 return

```
if (list.size() == nums.length) {
            res.add(new ArrayList<>(list));
            return;
```

```java
class Solution {
  	//time : O(n * 2^n)  space : O(n)
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if (nums == null || nums.length == 0) return res;
        helper (nums, res, new ArrayList<Integer>());
        return res;
    }
    
    private void helper(int[] nums, List<List<Integer>> res, List<Integer> list) {
        if (list.size() == nums.length) {
            res.add(new ArrayList<>(list));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (!list.contains(nums[i])) {
                list.add(nums[i]);
                helper(nums, res, list);
                list.remove(list.size() - 1);
            }
        }
    }
}
```



- **字符匹配**

#### [22. Generate Parentheses](https://leetcode-cn.com/problems/generate-parentheses/)

```java
class Solution {
    //time : O(n * 2^n)  space : O(n)
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<String>();
        if (n == 0) return res;
        helper(res, "", n, n);
        return res;
        }
    private void helper(List<String> res, String s, int left, int right) {
        if (left > right) {
            return;
        }
        if (left == 0 && right == 0) {
            res.add(s);
            return;
        }
        if (left > 0) helper(res, s + "(", left - 1, right);
        if (right > 0) helper(res, s + ")", left, right - 1);
    }
}
```



- **结合 Tree(BST)** 

#### [95. Unique Binary Search Trees II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)（不推荐做）

```java
class Solution {
  	//time : O(n * 2^n)  space : O(n)
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

![img](https://camo.githubusercontent.com/f12bff91b1e483933a43f4214cc562f38180c4db16d27d9f4dea3113455d3b77/68747470733a2f2f6173736574732e6c656574636f64652e636f6d2f75706c6f6164732f323032312f30312f31382f756e697175656273746e332e6a7067)

