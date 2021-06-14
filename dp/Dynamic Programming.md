### 1 What is Dynamic Programming

[Reference article](https://www.educative.io/courses/grokking-dynamic-programming-patterns-for-coding-interviews/m2G1pAq0OO0)

> Dynamic Programming (DP) is an algorithmic technique for solving an optimization problem by breaking it down into simpler subproblems and utilizing the fact that the optimal solution to the overall problem depends upon the optimal solution to its subproblems.
>
> 1.state: dp[] or dp[][]
>
> 2.state transition equation: if...: dp[i] = dp[i - 1]; else...
>
> 3.return: the last state/ maximum state in the middle

- The origin of DP: 
  - Recursion ->  Memoization -> DP
  - Exponential time complexity -> Reduce the time complexity by store the subproblem's results -> Resolve from bottom to the top without recursion

- Properties of DP:
  - Overlapping subproblems 
  - Optimal substructure
  - No aftereffect (every past state is a complete summary of past history)

- Essence of the DP: the definition of the **state** and the definition of the **state transition equation**. (Notice: The returned value is the last state, or need to judge in the middle.)

- DP optimization: Two-dimension to one-dimension, one-dimension to fixed.
- DP method: After determining the state, you can draw the matrix first, and then find the rule.



### 2 One dimensional DP 

> dp[i] represents the maximum or minimum value at i, the final maximum and minimum may be generated in the middle i.

> [53. Maximum Subarray](https://leetcode-cn.com/problems/maximum-subarray/)
>
> Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return *its sum*. 
>
> **Example 1:**
>
> ```
> Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
> Output: 6
> Explanation: [4,-1,2,1] has the largest sum = 6.
> ```

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        int maxSum = nums[0];
        for (int i = 1; i < nums.length; i++) {
            dp[i] = Math.max(dp[i - 1] + nums[i], nums[i]);
            if (dp[i] > maxSum) maxSum = dp[i];
        }
        return maxSum;
    }
}
//优化
class Solution {
    public int maxSubArray(int[] nums) {
        int sum = nums[0];
        int res = nums[0];
        for (int i = 1; i < nums.length; i++) {
            sum = sum <= 0 ? nums[i] : sum + nums[i];
            res = Math.max(res, sum);
        }
        return res;
    }
}
```





> [152. Maximum Product Subarray](https://leetcode-cn.com/problems/maximum-product-subarray/) (Also need to record the minimum value.)
>
> Given an integer array `nums`, find a contiguous non-empty subarray within the array that has the largest product, and return *the product*.
>
> It is **guaranteed** that the answer will fit in a **32-bit** integer.
>
> A **subarray** is a contiguous subsequence of the array.
>
> **Example 1:**
>
> ```
> Input: nums = [2,3,-2,4]
> Output: 6
> Explanation: [2,3] has the largest product 6.
> ```

```java
class Solution {
    public int maxProduct(int[] nums) {
        int[] min = new int[nums.length];
        int[] max = new int[nums.length];
        min[0] = nums[0];
        max[0] = nums[0];
        int maxProduct =  nums[0];
        for (int i = 1; i < nums.length; i++) {
            max[i] = Math.max(min[i - 1] * nums[i], Math.max(max[i - 1] * nums[i], nums[i]));
            min[i] = Math.min(max[i - 1] * nums[i], Math.min(min[i - 1] * nums[i], nums[i]));
            if (max[i] > maxProduct) maxProduct = max[i];
        }
        return maxProduct;
    }
}
```



##### Optimization from Two-dimension to one-dimension

> [120. Triangle](https://leetcode-cn.com/problems/triangle/)
>
> Given a triangle array, return the minimum path sum from top to bottom.
>
> For each step, you may move to an adjacent number of the row below. More formally, if you are on index i on the current row, you may move to either index i or index i + 1 on the next row.
>
> Example 1:
>
> ```
> Input: triangle = [[2],[3,4],[6,5,7],[4,1,8,3]]
> Output: 11
> Explanation: The triangle looks like:
>    2
>   3 4
>  6 5 7
> 4 1 8 3
> The minimum path sum from top to bottom is 2 + 3 + 5 + 1 = 11 (underlined above).
> ```

```java
//二维写法
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int[][] dp = new int[triangle.size()][triangle.size()];
        dp[0][0] = triangle.get(0).get(0);
        for (int i = 1; i < triangle.size(); i++) {
            dp[i][0] = dp[i - 1][0] + triangle.get(i).get(0);
            for (int j = 1; j < i; j++) {
                dp[i][j] = Math.min(dp[i - 1][j - 1], dp[i - 1][j]) + triangle.get(i).get(j);
            }
            dp[i][i] = dp[i - 1][i - 1] + triangle.get(i).get(i);
        }
        int min = dp[triangle.size() - 1][0];
        for (int i = 1; i < triangle.size(); i++) {
            min = Math.min(min, dp[triangle.size() - 1][i]);
        }
        return min;
    }
}

//优化为一维
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int[] dp = new int[triangle.size()];
        dp[0] = triangle.get(0).get(0);
        for (int i = 1; i < triangle.size(); i++) {
            dp[i] = dp[i - 1] + triangle.get(i).get(i); //从后往前
            for (int j = i - 1; j > 0; j--) {
                dp[j] = Math.min(dp[j - 1], dp[j]) + triangle.get(i).get(j);
            }  
            dp[0] = dp[0] + triangle.get(i).get(0);
        }
        int min = dp[0];
        for (int i = 1; i < triangle.size(); i++) {
            min = Math.min(min, dp[i]);
        }
        return min;
    }
}
```



### 3 Pattern1：Knapsack

> Given the weights and profits of ‘N’ items, we are asked to put these items in a knapsack that has a capacity ‘C’. The goal is to get the maximum profit from the items in the knapsack. Each item can only be selected once, as we don’t have multiple quantities of any item.
>
> Let’s take Merry’s example, who wants to carry some fruits in the knapsack to get maximum profit. Here are the weights and profits of the fruits:
>
> **Items:** { Apple, Orange, Banana, Melon }
> **Weights:** { 2, 3, 1, 4 }
> **Profits:** { 4, 5, 3, 7 }
> **Knapsack capacity:** 5
>
> Let’s try to put different combinations of fruits in the knapsack, such that their total weight is not more than 5:
>
> Apple + Orange (total weight 5) => 9 profit
> Apple + Banana (total weight 3) => 7 profit
> Orange + Banana (total weight 4) => 8 profit
> Banana + Melon (total weight 5) => 10 profit
>
> This shows that **Banana + Melon** is the best combination, as it gives us the maximum profit and the total weight does not exceed the capacity.
>
> The matrix of Knapsack is like the following: ![背包问题总结（上）](https://pic1.zhimg.com/v2-861a4291fcbfa52c8c2fece808134399_1440w.jpg?source=172ae18b)



#### 3.1 0-1 Knapsack

```java
public class Knapsack{

    public int getMaxValue_01BackPack_1(int N, int[] weight, int[] value, int C) {  
        //设状态为 dp[i][j] ： 代表第 i 件物品放入背包容量为 j 的最大价值（背包此时有前 i 件物品）
        int[][] dp = new int[N][C + 1];
        //将第一个物品放入作为初始化条件
        for (int k = weight[0]; k <= C; k++) {
            dp[0][k] = value[0];
        }
        //把其他的也一个一个装进去
        for (int i = 1; i < N; i++) {
            for (int j = 0; j <= C; j++) {
                if(j >= weight[i]) {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i]);
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[N - 1][C];
    }

    //优化代码：省略初始化 就是多加一行 自动初始化为 0， 但是 i 代表是第 i - 1 件物品
    public int getMaxValue_01BackPack_2(int N, int[] weight, int[] value, int C) {
        int[][] dp = new int[N + 1][C + 1];

        for (int i = 1; i <= N; i++) {
            for (int j = 0; j <= C; j++) {
                if(j >= weight[i - 1]) {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - weight[i - 1]] + value[i - 1]);
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[N - 1][C];
    }
//--------------------------背包问题一维写法分割线------------------------------------------

    //代码优化：将二维数组转化为一维数组，01 背包问题需要从后往前遍历
    public int getMaxValue_01BackPack_3(int N, int[] weight, int[] value, int C) {
        int[] dp = new int[C + 1];
        for (int i = 0; i < N; i++) {
            for (int j = C; j >= weight[i]; j--) {  // <weight[i] 的部分就自动保持之前的值
                dp[j] = Math.max(dp[j], dp[j - weight[i]]+ value[i]);
            }
        }
        return dp[C];
    }
}
```



#### 3.2 Mutiple Knapsack

```java
 /**
 *多重背包问题 ： 没个东西不再是一个，而是给你 nums 数组代表每个东西的数量
 */
  
    //01背包转化的完全背包 （麻烦）扩充 N 即可
    //getMaxValue_MultiBackPack_1

    //增加for循环写成的多重背包
    public int getMaxValue_MultiBackPack_2(int N, int[] weight, int[] value, int[] nums, int C) {
        int[] dp = new int[C + 1];
        for (int i = 0; i < N; i++) {
            for (int j = C; j >= weight[i]; j--) {
                for (int k = 0; k <= nums[i]; k++) {
                    if (j >= k * weight[i]) {
                        dp[j] = Math.max(dp[j], dp[j - k * weight[i]]+ k * value[i]);
                    }
                }
            }
        }
        return dp[C];
    }
```



#### 3.3 Unbounded Knapsack

```java
/**
 *完全背包问题 ： 没个东西不再是一个，而是给你无限个
*/
    //01背包转化的完全背包 （麻烦）扩充 N 即可，每个东西的数量为包能装下的最大数量
    //getMaxValue_CompleteBackPack_1

    //由多重背包转化的完全背包 （次麻烦）增加 nums 数据即可
    //getMaxValue_CompleteBackPack_2

    //有自己独特思想的的完全背包 （唯一的一维正序写法）
    public int getMaxValue_CompleteBackPack_3(int N, int[] weight, int[] value, int C) {
        int[] dp = new int[C + 1];
        for (int i = 0; i < N; i++) {
            for (int j = weight[i]; j <= C; j++) {
                dp[j] = Math.max(dp[j], dp[j - weight[i]]+ value[i]);
            }
        }
        return dp[C];
    }
```



#### 3.4 Follow up：Print intermediate process

> Use string array, pay attention to classification processing

```java
//follow up : 打印中间过程
public void BackPack_followup(int N, int[] weight, int[] value, int C) {
  int[][] dp = new int[N + 1][C + 1];
  String[][] path = new String[N + 1][C + 1];

  for (int i = 1; i <= N; i++) {
    for (int j = 0; j <= C; j++) {
      if (j < weight[i - 1]) {
        dp[i][j] = dp[i - 1][j];
        if (path[i - 1][j] != null) path[i][j] = path[i - 1][j]; // 注意判空
      } else {
        //不能用 max 而是要分类讨论了!
        if (dp[i - 1][j] >= dp[i - 1][j - weight[i - 1]] + value[i - 1]) {
          dp[i][j] = dp[i - 1][j];
          path[i][j] = path[i - 1][j];
        } else {
          dp[i][j] = dp[i - 1][j - weight[i - 1]] + value[i - 1];
          if (path[i - 1][j - weight[i - 1]] != null) {   // 注意判空
            path[i][j] = path[i - 1][j - weight[i - 1]] + " " + i;
          } else {
            path[i][j] = Integer.toString(i);  //或者 i+""
          }
        }
      }
    }
  }
  for(String[] p : path) {
    System.out.println(Arrays.toString(p)); //打印
  }
}
```



#### 3.5 Other cases

> [322. Coin Change](https://leetcode-cn.com/problems/coin-change/)
>
> You are given an integer array coins representing coins of different denominations and an integer amount representing a total amount of money.
> Return the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.
> You may assume that you have an infinite number of each kind of coin.
>
>     Example 1:
>     Input: coins = [1,2,5], amount = 11
>     Output: 3
>     Explanation: 11 = 5 + 5 + 1

```java
//按照完全背包来写
   public int coinChange_1(int[] coins, int amount) {
       int[] dp = new int[amount + 1]; // 状态代表：i 块时能兑换的最少硬币数
       //初始化
       dp[0] = 0;// 0块 0个硬币
       for (int k = 1; k <= amount; k++) {
           dp[k] = -1; // 用于精准值取得 取不得为-1
       }

       for (int i = 0; i < coins.length; i++) {
           for (int j = coins[i]; j <= amount; j++) {
               if (dp[j - coins[i]] != -1 && dp[j] != -1) {
                   dp[j] = Math.min(dp[j], dp[j - coins[i]] + 1);
               } else if (dp[j] == -1) {
                   dp[j] = dp[j - coins[i]] == -1 ? -1 : dp[j - coins[i]] + 1;
               } //否则就保持原来的值 反正谁不等于-1 就看谁 都不等于-1 可以取最小
           }
       }
       return dp[amount];
   }

//但是其实 dp 问题是很灵活的 不止有一种写法，主要是看你怎么定义这个状态和状态转移方程
//先遍历 amount 的写法：
   public int coinChange_2(int[] coins, int amount) {

       if (coins == null || coins.length == 0) return -1;
       int[] dp = new int[amount + 1];
       for (int i = 1; i < amount + 1; i++) { // dp[0] = 0 from 1
           int min = Integer.MAX_VALUE;
           for (int j = 0; j < coins.length; j++) {
               if (i >= coins[j] && dp[i - coins[j]] != -1) {
                   min = Math.min(min, dp[i - coins[j]] + 1);
               }
               dp[i] = min == Integer.MAX_VALUE ? -1 : min;
           }
       }
       return dp[amount];
   }
```



> [377. Combination Sum IV](https://leetcode-cn.com/problems/combination-sum-iv/)
>
> Given an array of distinct integers nums and a target integer target, return the number of possible combinations that add up to target.
>
> The answer is guaranteed to fit in a 32-bit integer.
>
> Example 1:
>
> ```
> Input: nums = [1,2,3], target = 4
> Output: 7
> Explanation:
> The possible combination ways are:
> (1, 1, 1, 1)
> (1, 1, 2)
> (1, 2, 1)
> (1, 3)
> (2, 1, 1)
> (2, 2)
> (3, 1)
> Note that different sequences are counted as different combinations.
> ```

```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int num : nums) {
                if (i - num >= 0) {
                    dp[i] += dp[i - num];
                }
            }
        }
        return dp[target];
    }       
}

// Follow up: different sequences are counted as same combinations
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 0; i < nums.length; i++) {
            for (int j = nums[i]; j <= target; j++) {
                dp[j] += dp[j - nums[i]];
            }
        }
        return dp[target];
    }       
}
```



### 4 Pattern2：Substring and Subsequence

> Tips：注意条件 --- 矩阵对角一半 j<i

#### 4.1 Word break

>[139. Word Break](https://leetcode-cn.com/problems/word-break/)
>
>Given a string s and a dictionary of strings wordDict, return true if s can be segmented into a space-separated sequence of one or more dictionary words.
>
>Note that the same word in the dictionary may be reused multiple times in the segmentation.
>
>```
>Example 1:
>
>Input: s = "leetcode", wordDict = ["leet","code"]
>Output: true
>Explanation: Return true because "leetcode" can be segmented as "leet code".
>```

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        boolean[] dp = new boolean[s.length() + 1];
        dp[0] = true;
        for (int i = 1; i <= s.length(); i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && wordDict.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[s.length()];
    }
}
```

#### 4.2 Palindrome

> [5. Longest Palindromic Substring](https://leetcode-cn.com/problems/longest-palindromic-substring/)
>
> Given a string s, return the longest palindromic substring in s.
>
> ```
> Example 1:
> 
> Input: s = "babad"
> Output: "bab"
> Note: "aba" is also a valid answer.
> ```

```java
class Solution {
    public String longestPalindrome(String s) {
        int len = s.length();
        if (len < 2) {
            return s;
        }
        int maxLen = 1;
        int begin = 0;
        boolean[][] dp = new boolean[len][len];

        for (int i = 0; i < s.length(); i++) {    
            for (int j = 0; j <= i; j++) {
                dp[i][j] = s.charAt(i) == s.charAt(j) && ((i - j <= 2) || dp[i - 1][j + 1]);//画矩阵理解 j在后<i; i 在前面
                if (dp[i][j] && i - j + 1 > maxLen) {
                    maxLen = i - j + 1;
                    begin = j;
                }
            }
        }
        return s.substring(begin, begin + maxLen);
    }
}
```

![Screen Shot 2021-06-13 at 6.43.18 PM](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/images/Screen%20Shot%202021-06-13%20at%206.43.18%20PM.png)

#### 4.3 Subsequece

>[300. Longest Increasing Subsequence](https://leetcode-cn.com/problems/longest-increasing-subsequence/)
>
>Given an integer array `nums`, return the length of the longest strictly increasing subsequence.
>
>A **subsequence** is a sequence that can be derived from an array by deleting some or no elements without changing the order of the remaining elements. For example, `[3,6,2,7]` is a subsequence of the array `[0,3,1,6,2,2,7]`.
>
>**Example 1:**
>
>```
>Input: nums = [10,9,2,5,3,7,101,18]
>Output: 4
>Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.
>```

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            dp[i] = 1;
        }
        int maxLen = 1;  // 注意不是最后一个一定是最大的 而是中间过程就要判断
        for (int i = 1; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) dp[i] = Math.max(dp[i], dp[j] + 1);
            }
            maxLen = Math.max(maxLen, dp[i]);
        }
        return maxLen;
    }
}
```



#### 4.4 Decode Ways (optimize two-dimension to 1)

>[91. Decode Ways](https://leetcode-cn.com/problems/decode-ways/)
>
>A message containing letters from A-Z can be encoded into numbers using the following mapping:
>
>'A' -> "1"
>'B' -> "2"
>...
>'Z' -> "26"
>To decode an encoded message, all the digits must be grouped then mapped back into letters using the reverse of the mapping above (there may be multiple ways). For example, "11106" can be mapped into:
>
>"AAJF" with the grouping (1 1 10 6)
>"KJF" with the grouping (11 10 6)
>Note that the grouping (1 11 06) is invalid because "06" cannot be mapped into 'F' since "6" is different from "06".
>
>Given a string s containing only digits, return the number of ways to decode it.
>
>The answer is guaranteed to fit in a 32-bit integer.
>
>
>Example 1:
>
>```
>Input: s = "226"
>Output: 3
>Explanation: "226" could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).
>```
>
>Example 2:
>
>```
>Input: s = "06"
>Output: 0
>Explanation: "06" cannot be mapped to "F" because of the leading zero ("6" is different from "06").
>```

```java
//two-dimension(time exceed)
class Solution {
    public int numDecodings(String s) {
        int[] dp = new int[s.length() + 1];
        dp[0] = 1;
        for (int i = 1; i <= s.length(); i++) {
            for (int j = 0; j < i; j++) {
                if (s.charAt(j) != '0' && Integer.parseInt(s.substring(j, i)) <= 26) {
                    dp[i] += dp[j];
                }
            }
        }
        return dp[s.length()];
    }
}
//1-dimension
class Solution {
    public int numDecodings(String s) {
        int[] dp = new int[s.length() + 1];
        dp[0] = 1;
        for (int i = 1; i <= s.length(); i++) {
            if (s.charAt(i - 1) != '0') {
                dp[i] += dp[i - 1];
            }
            if (i > 1 && s.charAt(i - 2) != '0' && Integer.parseInt(s.substring(i - 2, i)) <= 26){
                dp[i] += dp[i - 2];
            }
        }
        return dp[s.length()];
    }
}
//fixed
class Solution {
    public int numDecodings(String s) {
        int a = 1;
        int b = s.charAt(0) == '0'? 0:1;
        for (int i = 2; i <= s.length(); i++) {
            int temp = 0;
            if (s.charAt(i - 1) != '0') {
                temp += b;
            }
            if (s.charAt(i - 2) != '0' && Integer.parseInt(s.substring(i - 2, i)) <= 26){
                temp += a; 
            }
            a = b;
            b = temp;
        }
        return b;
    }
}
```



### 5 Pattern3：Grid

> [62. Unique Paths](https://leetcode-cn.com/problems/unique-paths/)
>
> A robot is located at the top-left corner of a `m x n` grid (marked 'Start' in the diagram below).
>
> The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).
>
> How many possible unique paths are there?

```java

class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        for (int i = 0; i < m; ++i) {
            dp[i][0] = 1;
        }
        for (int j = 0; j < n; ++j) {
            dp[0][j] = 1;
        }
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```



### 6 Pattern4：Matching 

#### 6.1 Easy Matching

> Tips: Notice the difference between subarray and subsequence.

> [1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)
>
> Given two strings `text1` and `text2`, return *the length of their longest **common subsequence**.* If there is no **common subsequence**, return `0`.
>
> A **subsequence** of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters.
>
> - For example, `"ace"` is a subsequence of `"abcde"`.
>
> A **common subsequence** of two strings is a subsequence that is common to both strings.
>
> **Example 1:**
>
> ```
> Input: text1 = "abcde", text2 = "ace" 
> Output: 3  
> Explanation: The longest common subsequence is "ace" and its length is 3.
> ```

```java
class Solution {
     //time : O(n*m) space: O(n*m) 
    public int longestCommonSubsequence(String text1, String text2) {
        //dp[i][j] represents the longest Common Subsequence of text1[0:i] & text2[0:j]
        int[][] dp = new int[text1.length() + 1][text2.length() + 1];
        for (int i = 0; i < text1.length() + 1; i++) {
            dp[i][0] = 0;
        }
        for (int i = 0; i < text2.length() + 1; i++) {
            dp[0][i] = 0;
        }
        for (int i = 1; i < text1.length() + 1; i++) {
            for (int j = 1; j < text2.length() + 1; j++) {
                
                if (text1.charAt(i-1) == text2.charAt(j-1)) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                     dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
                }
                
            }
        }
        return dp[text1.length()][text2.length()];    
    }
}
```

>[718. Maximum Length of Repeated Subarray](https://leetcode-cn.com/problems/maximum-length-of-repeated-subarray/)
>
>Given two integer arrays `nums1` and `nums2`, return *the maximum length of a subarray that appears in **both** arrays*.
>
>**Example 1:**
>
>```
>Input: nums1 = [1,2,3,2,1], nums2 = [3,2,1,4,7]
>Output: 3
>Explanation: The repeated subarray with maximum length is [3,2,1].
>```

```java
class Solution {
    public int findLength(int[] nums1, int[] nums2) {
        int max = 0; // 注意不是最后一个一定是最大的 而是中间过程就要判断
        int[][] dp = new int[nums1.length + 1][nums2.length + 1];
        for (int i = 0; i < nums1.length + 1; i++) {
            dp[i][0] = 0;
        }
        for (int i = 0; i < nums2.length + 1; i++) {
            dp[0][i] = 0;
        }
        for (int i = 1; i < nums1.length + 1; i++) {
            for (int j = 1; j < nums2.length + 1; j++) {
                if (nums1[i-1] == nums2[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                    max = Math.max(max, dp[i][j]);
                }  
            }
        }
        return max;
    }
}
```



#### 6.2 Regular Expression

>[10. Regular Expression Matching](https://leetcode-cn.com/problems/regular-expression-matching/)
>
>Given an input string (s) and a pattern (p), implement regular expression matching with support for '.' and '*' where: 
>
>'.' Matches any single character.
>'*' Matches zero or more of the preceding element.
>The matching should cover the entire input string (not partial).
>
>Example 1:
>
>```
>Input: s = "aa", p = "a"
>Output: false
>Explanation: "a" does not match the entire string "aa".
>```

```java
class Solution {
    public boolean isMatch(String s, String p) {
        //状态 dp 
        boolean[][] dp = new boolean[s.length() + 1][p.length() + 1];
        //初始化： "" and "c*c*" c* == empty
        dp[0][0] = true;
        for (int i = 2; i <= p.length(); i++) {
            if (p.charAt(i - 1) == '*') {
                dp[0][i] = dp[0][i - 2];
            }
        }
       /*状态转移方程: 1.如果相等或者匹配. 看dp[i - 1][j - 1];
                     2.如果匹配*: 1)如果上一个字符如果相等或者匹配.: 三种情况(匹配一个a a*;匹配多个 aa a*;匹配 0 个 b bb*);
                     3.如果其他情况（就是两个字符不相等也没有字符匹配）,不作处理默认为false*/
        for (int i = 1; i <= s.length(); i++) {
            for (int j = 1; j <= p.length(); j++) {
                if (s.charAt(i - 1) == p.charAt(j - 1) || p.charAt(j - 1) == '.') {
                    dp[i][j] = dp[i - 1][j - 1];
                }
                if (p.charAt(j - 1) == '*') {
                    if (s.charAt(i - 1) == p.charAt(j - 2) || p.charAt(j - 2) == '.') {
                        dp[i][j] = dp[i][j - 1] || dp[i - 1][j] || dp[i][j - 2];
                    } else {
                        dp[i][j] = dp[i][j - 2];
                    } 
                }
            }
        }
        //return 最后的两字符串结尾
        return dp[s.length()][p.length()];
    }
}
```



>[44. Wildcard Matching](https://leetcode-cn.com/problems/wildcard-matching/) (10题的简单版)
>
>Given an input string (s) and a pattern (p), implement wildcard pattern matching with support for '?' and '*' where:
>
>'?' Matches any single character.
>'*' Matches any sequence of characters (including the empty sequence).
>The matching should cover the entire input string (not partial).
>
>Example 1:
>
>```
>Input: s = "aa", p = '*' 
>Output: true
>```

```java
class Solution {
    public boolean isMatch(String s, String p) {
        boolean[][] dp = new boolean[s.length() + 1][p.length() + 1];
        dp[0][0] = true;
        // "" and "*****"
        for (int i = 1; i <= p.length(); i++) {
            if(p.charAt(i - 1) == '*' ) dp[0][i] = dp[0][i-1];
        }
        for (int i = 1; i<= s.length(); i++) {
            for (int j = 1; j <= p.length(); j++) {
                if (s.charAt(i - 1) == p.charAt(j - 1) || p.charAt(j - 1) == '?') {
                    dp[i][j] = dp[i - 1][j - 1]; 
                }
                if (p.charAt(j - 1) == '*' ) {
                    //aaaa "*" || aa "a*" || aa  "aa*"
                    dp[i][j] = (dp[i - 1][j] || dp[i - 1][j - 1] || dp[i][j - 1]) ; 
                }
            }
        }
         return dp[s.length()][p.length()];
    }
}
```



####  6.3 Edit 

> [72. Edit Distance](https://leetcode.com/problems/edit-distance/)
>
> Given two strings `word1` and `word2`, return *the minimum number of operations required to convert `word1` to `word2`*.
>
> You have the following three operations permitted on a word:
>
> - Insert a character
> - Delete a character
> - Replace a character
>
> **Example 1:**
>
> ```
> Input: word1 = "horse", word2 = "ros"
> Output: 3
> Explanation: 
> horse -> rorse (replace 'h' with 'r')
> rorse -> rose (remove 'r')
> rose -> ros (remove 'e')
> ```



```java
class Solution {
    public int minDistance(String word1, String word2) {
        int[][] dp = new int[word1.length() + 1][word2.length() + 1];
        dp[0][0] = 0;
        for (int i = 1; i <= word1.length(); i++) {
            dp[i][0] = i;
        }
        for (int j = 1; j <= word2.length(); j++) {
            dp[0][j] = j;
        }
        for (int i = 1; i<= word1.length(); i++) {
            for (int j = 1; j <= word2.length(); j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1) ) {
                    dp[i][j] = dp[i - 1][j - 1]; 
                } else {
                    //                  delete               insert             replace        
                    dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1; 
                }
            }
        }
         return dp[word1.length()][word2.length()];
    }
}
```



> 其他练习：
>
> 匹配问题 ：115、97
>
> 网格问题：70、63、64、174、221
>
> 字符串问题：132
>
> hard 题：312、375
>
> 其他结合：96、95、354
>
> 实现题&有趣套题：279、股票问题（121、122、123、188、309）、打家劫舍（198、213）、装修（256、265、276）

