## Content
- [1 What is Dynamic Programming](#1-what-is-dynamic-programming)
- [2 One dimensional DP](#2-one-dimensional-dp)
    + [Optimization from Two-dimension to one-dimension](#optimization-from-two-dimension-to-one-dimension)
- [3 Pattern1：Knapsack](#3-pattern1-knapsack)
  * [3.1 0-1 Knapsack](#31-0-1-knapsack)
  * [3.2 Mutiple Knapsack](#32-mutiple-knapsack)
  * [3.3 Unbounded Knapsack](#33-unbounded-knapsack)
  * [3.4 Follow up：Print/output intermediate process](#34-follow-up-print-output-intermediate-process)
  * [3.5 Other cases](#35-other-cases)
- [4 Pattern2：Prefix -- Substring](#4-pattern2-prefix----substring)
  * [4.1 Word break](#41-word-break)
  * [4.2 Palindrome](#42-palindrome)
  * [4.4 Decode Ways (optimize two-dimension to 1)](#44-decode-ways--optimize-two-dimension-to-1-)
- [5 Pattern3：Prefix --Subsequence](#5-pattern3-prefix---subsequence)
- [6 Pattern4：Prefix --Matching](#6-pattern4-prefix---matching)
  * [6.1 Easy Matching](#61-easy-matching)
  * [6.2 Regular Expression](#62-regular-expression)
  * [6.3 Edit](#63-edit)
- [7 Pattern5：Grid](#7-pattern5-grid)
- [8 Pattern6: probability](#8-pattern6--probability)
- [9 Series Question](#9-series-question)
  * [9.1 House Robber](#91-house-robber)
  * [9.2 Stock](#92-stock)
  * [9.3 Painting](#93-painting)
- [10 Much more:](#10-much-more-)



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
        int[] dp = new int[triangle.size()]; //最后一行的长度等于 triangle 的 size
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
 *多重背包问题 ： 每个东西不再是一个，而是给你 nums 数组代表每个东西的数量
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



#### 3.4 Follow up：Print/output intermediate process

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



[1800 · Float Combination Sum](https://www.lintcode.com/problem/1800/description)

> Given an array of `A`, a non-negative integer `target`. Each decimal in `A` needs to be operated by ceil or floor, and finally an integer array is obtained, requiring the sum of all integers that are in the array to be equal to `target`, and requiring adjustments sum of the decimal array is minimum.
> Such as ceil(1.2),adjustment is 0.8,floor(1.2),adjustment is 0.2. return the integer array.
>
> > 1.`1<=A.length<=300`
> >
> > 2.`0<=target<=15000`
> >
> > 3.`0.0<=A[i]<=100.0`
> >
> > 4.Datas guarantees the existence of answers.
> > There are still something wrong in the c++ and python. We will fix it as soon as possible.
>
> **Example 1:**
>
> ```
> Input：A=[1.2,1.3,2.3,4.2],target=9
> Output：[1,1,3,4]
> Explanation：1.2->1,1.3->1,2.3->3,4.2->4.
> ```

```java
public class Solution {
    /**
     * @param A: A float array
     * @param target: A non-negative integer
     * @return: Return an integer array which sum equals target
     */
    public int[] getArray(double[] A, int target) {
        int n = A.length;
        double[][] dp = new double[n + 1][target + 1];
        int[][] prev = new int[n + 1][target + 1];
        for (int i = 0; i < n + 1; i++) {
            for (int j = 0; j < target + 1; j++) {
                dp[i][j] = 300;
            }
        }
        dp[0][0] = 0;
        for (int i = 1; i < n + 1; i++) {
            int ceil = (int)Math.ceil(A[i - 1]);
            int floor = (int)Math.floor(A[i - 1]);
            for (int j = 0; j < target + 1; j++) {
                if (j >= ceil) {
                    dp[i][j] = dp[i - 1][j - ceil] + ceil - A[i - 1];
                    prev[i][j] = 1;}
                if (j >= floor && dp[i - 1][j - floor]  + A[i - 1] - floor < dp[i][j]) {
                    dp[i][j] = dp[i - 1][j - floor]  + A[i - 1] - floor;
                    prev[i][j] = 2;
                }
            }
        }
        int[] res = new int[n];
        
        for (int i = n; i > 0; i--) {
            if (prev[i][target] == 1) res[i - 1] = (int)Math.ceil(A[i - 1]);
            else res[i - 1] = (int)Math.floor(A[i - 1]);
            target -= res[i - 1];
        }
        return res;
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



>[279. Perfect Squares](https://leetcode-cn.com/problems/perfect-squares/)
>
>Given an integer n, return the least number of perfect square numbers that sum to n.
>
>A perfect square is an integer that is the square of an integer; in other words, it is the product of some integer with itself. For example, 1, 4, 9, and 16 are perfect squares while 3 and 11 are not.
>
>Example 1:
>
>```
>Input: n = 12
>Output: 3
>Explanation: 12 = 4 + 4 + 4.

```java
//coins change : coins is equal to i*i. Coins must contain 1, so the initialization is easier
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1];
        for (int i = 0; i < n + 1; i++) dp[i] = i;
        for (int i = 1; i * i <= n; i++) {
            for (int j = i * i; j <= n; j++) {
                dp[j] = Math.min(dp[j], dp[j - i * i] + 1);
            }
        }
        return dp[n];
    }
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



**方案数 = 不做i 项目的方案数 + 做 i 项目的方案数**

> [1607 · Profitable Schemes](https://www.lintcode.com/problem/1607/)
>
> There are `G` criminals are going to plan a series of criminal activity.
> Given two arrays named `groups` and `profit` represent the i-th criminal activity needs `groups[i]` members and it will get a profit `profit[i]`.
> A member can only participate in one criminal activity at the same time.
> The purpose of the gang is to get at least p profit. Your task is to calculate how many options are available.
> You need to return the answer module 10^{9}+7.
>
> **Example 1:**
>
> ```
> Input: G = 5, P = 3, group = [2,2], profit = [2,3]
> Output: 2
> Explanation: 
> To make a profit of at least 3, the gang could either commit crimes 0 and 1, or just crime 1.
> In total, there are 2 schemes.
> ```

```java
public class Solution {
    /**
     * @param G: The people in a gang.
     * @param P: A profitable scheme any subset of these crimes that generates at least P profit.
     * @param group: The i-th crime requires group[i] gang members to participate.
     * @param profit: The i-th crime generates a profit[i].
     * @return: Return how many schemes can be chosen.
     */
    private int MOD = 1000000007;
    public int profitableSchemes(int G, int P, int[] group, int[] profit) {
        // dp[n][G][P] 代表 前 n 个犯罪项目用了 G 个人 获得至少 P 盈利的方案数
        //前i个方案数 用 dp[i-1][啥也不做] + dp[i-1][减去当前的一些操作]表示
        int n = group.length;
        int[][][] dp = new int[2][G + 1][P + 1];
        dp[0][0][0] = 1;
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <=G; j++) {
                for (int k = 0; k <= P; k++) {
                    dp[i % 2][j][k] = dp[(i - 1) % 2][j][k];
                    if (j >= group[i - 1]) {
                        int preK = Math.max(k - profit[i - 1], 0);
                        dp[i % 2][j][k] = (dp[i % 2][j][k] + dp[(i - 1) % 2][j - group[i - 1]][preK]) % MOD;
                    }
                }
            }
        }
        int res = 0;
        for (int g = 0; g <= G; g++) {
            res = (res + dp[n % 2][g][P]) % MOD;
        }
        return res;
    }
}
```



### 4 Pattern2：Prefix -- Substring 

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

> much more : 132



### 5 Pattern3：Prefix --Subsequence

> 状态一般是以 i 为结束位置

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
  	//time : o(n)
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



> [1702 · Distinct Subsequences II](https://www.lintcode.com/problem/1702/)
>
> Given a string `S`, count the number of distinct, non-empty subsequences of `S` .
>
> Since the result may be large, return the answer modulo `10^9 + 7`.
>
> 1. `S` contains only lowercase letters.
> 2. `1 <= S.length <= 2000`
>
> **Example 1:**
>
> ```
> Input: "abc"
> Output: 7
> Explanation: The 7 distinct subsequences are "a", "b", "c", "ab", "ac", "bc", and "abc".
> ```

```java
public class Solution {
    /**
     * @param S: The string s
     * @return: The number of distinct, non-empty subsequences of S.
     */
    private int MOD = 1000000007;
    public int distinctSubseqII(String S) {
       
        // Write your code here
        //思路：就是以 i 未结尾的有多少个 但是要去重！去重的原则就是起点选择第一次出现的字符，拼接也不去选择和自己字符相等的前面的字符
        int len = S.length();
        int[] dp = new int[len];
        HashSet<Character> visited = new HashSet<>();
        for (int i = 0; i < len; i++) {
            if (visited.add(S.charAt(i))) dp[i] = 1;
        }
        for (int i = 0; i < len; i++) {
            for (int j = i - 1; j >= 0; j--) {
                dp[i] = (dp[i] + dp[j]) % MOD;
                if (S.charAt(i) == S.charAt(j)) break;
            }
        }
        int res = 0;
        for (int i = 0; i < len; i++) {
            res = (res + dp[i]) % MOD;
        }
        return res;
    }
}
```





### 6 Pattern4：Prefix --Matching 

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



> [115. Distinct Subsequences](https://leetcode-cn.com/problems/distinct-subsequences/)
>
> Given two strings s and t, return the number of distinct subsequences of s which equals t.
>
> A string's subsequence is a new string formed from the original string by deleting some (can be none) of the characters without disturbing the remaining characters' relative positions. (i.e., "ACE" is a subsequence of "ABCDE" while "AEC" is not).
>
> It is guaranteed the answer fits on a 32-bit signed integer.
>
> Example 1:
>
> ```Input: s = "babgbag", t = "bag"
> Input: s = "babgbag", t = "bag"
> Output: 5
> ```
>
> [Reference Video](https://www.bilibili.com/video/BV1R4411T7sV?from=search&seid=15071372781045463717)

```java
class Solution {
    public int numDistinct(String s, String t) {
        int[][] dp = new int[t.length() + 1][s.length() + 1];
        for (int i = 0; i <= s.length(); i++) {
            dp[0][i] = 1;
        }
        for (int i = 1; i <= t.length(); i++) {
            for (int j = 1; j <= s.length(); j++) {
                if (t.charAt(i - 1) == s.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + dp[i][j - 1]; //bbb * a + (ba)
                } else {
                     dp[i][j] = dp[i][j - 1]; //no mind
                }
            }
        }
        return dp[t.length()][s.length()];
    }
}
```



> [7. Interleaving String](https://leetcode-cn.com/problems/interleaving-string/)
>
> Given strings s1, s2, and s3, find whether s3 is formed by an interleaving of s1 and s2.
>
> An interleaving of two strings s and t is a configuration where they are divided into non-empty substrings such that:
>
> s = s1 + s2 + ... + sn
> t = t1 + t2 + ... + tm
> |n - m| <= 1
> The interleaving is s1 + t1 + s2 + t2 + s3 + t3 + ... or t1 + s1 + t2 + s2 + t3 + s3 + ...
> Note: a + b is the concatenation of strings a and b. 
>
> Example 1:
>
> ![img](https://assets.leetcode.com/uploads/2020/09/02/interleave.jpg)
>
> ```
> Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
> Output: true
> ```

```java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        if (s1.length() + s2.length() != s3.length()) return false;
        //dp : represent whether the s1[0..i] and s2[0..j] can configure the s3[0..i + j]
        boolean[][] dp = new boolean[s1.length() + 1][s2.length() + 1];
        //初始化
        dp[0][0] = true;
        for (int i = 1; i <= s1.length(); i++) {
            dp[i][0] = dp[i - 1][0] && s1.charAt(i - 1) == s3.charAt(i - 1);
        }
        for (int i = 1; i <= s2.length(); i++) {
            dp[0][i] = dp[0][i - 1] && s2.charAt(i - 1) == s3.charAt(i - 1);
        }
        //状态转移方程：前面的不包含当前元素 是不是能构成 能的话看当前元素和s3 对应元素是不是相等
        for (int i = 1; i <= s1.length(); i++) {
            for (int j = 1; j <= s2.length(); j++) {
                dp[i][j] = (dp[i - 1][j] && s1.charAt(i - 1) == s3.charAt(i + j - 1)) || (dp[i][j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1));
            }
        }
        return dp[s1.length()][s2.length()];
    }
}
```



### 7 Pattern5：Grid

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

> [1861 · Rat Jump](https://www.lintcode.com/problem/1861/)
>
> There is a mouse jumping from the top of a staircase with height `n`. This mouse can jump 1, 3 or 4 steps in an even number of jumps and 1, 2, or 4 steps in an odd number of times. Some steps have glue,if the mouse jump those steps,it will be directly stuck and cannot continue to jump.You need to solve how many ways the mouse can reach the ground ( level 0 ) from the top of this staircase.It also can be reached if it exceeds the ground. For example, jumping from 1 to -1 is another plan for jumping from 1 to 0.The state of the stairs with or without glue is input from high to low, that is, `arr[0]` is the top of the stairs.
> `arr[i] == 0` represents that there is no glue on the i-th position, `arr[i] == 1` represents that there is glue on the i-th position.
>
> **Example1:**
>
> ```
> Input:
> [0,0,0]
> Output:
> 5
> Explanation:
> There are 3 steps.
> The step 2  is the starting point without glue.
> Step 1, no glue.
> Step 0, no glue.
> The mouse jump plans is:
> 2--odd(1)-->1--even(1)-->0
> 2--odd(1)-->1--even(3)-->-2
> 2--odd(1)-->1--even(4)-->-3
> 2--odd(2)-->0
> 2--odd(4)-->-2
> ```

```java
public class Solution {
    /**
     * @param arr: the steps whether have glue
     * @return: the sum of the answers
     */
    private int MOD = 1000000007; 
    
    public int ratJump(int[] arr) {
        int n = arr.length;

        // state: dp[i][0] 代表从 0 的位置跳偶数步后到 i 的位置的方案数
        //        dp[i][1] 代表从 0 的位置跳奇数步后到 i 的位置的方案数
        int[][] dp = new int[n][2]; // 可以令 n = 5 使用滚动数组 这个滚动数组计算的是前面符合条件的方案数的和 并不需要用到自己本来的数 所以算之前把自己清空 ： dp[i % 5][0] =  dp[i % 5][1] = 0;
        // initialization: 一开始站在 0 的位置, 已经跳过了 0 步(偶数步)
        dp[0][0] = 1;
        
        int[] evenJumps = {1, 3, 4};
        int[] oddJumps = {1, 2, 4};
        
        // function: dp[i][0] = dp[i - 1][1] + dp[i - 3][1] + dp[i - 4][1]
        //           dp[i][1] = dp[i - 1][0] + dp[i - 2][0] + dp[i - 4][0]
        for (int i = 1; i < n - 1; i++) {
            if (arr[i] == 1) {
                continue;
            }
            for (int jump: evenJumps) {
                if (i - jump >= 0) {
                    dp[i][0] = (dp[i][0] + dp[i - jump][1]) % MOD;
                }
            }
                
            for (int jump: oddJumps) {
                if (i - jump >= 0) {
                    dp[i][1] = (dp[i][1] + dp[i - jump][0]) % MOD;
                }
            }
        }
            
        
        // answer: 研究跳到地板之前的那一步是从哪儿出发的，跳了多远
        int plans = 0;
        for (int jump: evenJumps) {
            for (int i = Math.max(0, n - jump - 1); i < n - 1; i++) {
                plans = (plans + dp[i][1]) % MOD;
            }
        }
            
        for (int jump: oddJumps) {
            for (int i = Math.max(0, n - jump - 1); i < n - 1; i++) {
                plans = (plans + dp[i][0]) % MOD;
            }
        }
        return plans;
    }
}
```



> much more : 70、63、64、174、221



### 8 Pattern6: probability

> [20 · Dices Sum](https://www.lintcode.com/problem/20)
>
> Throw `n` dices, the sum of the dices' faces is `S`. Given `n`, find the all possible value of `S` along with its probability.
>
> **Example 1:**
>
> Input:
>
> ```
> n = 2
> ```
>
> Output:
>
> ```
> [[2,0.03],[3,0.06],[4,0.08],[5,0.11],[6,0.14],[7,0.17],[8,0.14],[9,0.11],[10,0.08],[11,0.06],[12,0.03]]
> ```

```java
public class Solution {
    /**
     * @param n an integer
     * @return a list of Map.Entry<sum, probability>
     */
    public List<Map.Entry<Integer, Double>> dicesSum(int n) {
        // Write your code here
        // Ps. new AbstractMap.SimpleEntry<Integer, Double>(sum, pro)
        // to create the pair

        if (n == 0) return new ArrayList<>();
        double[][] dp = new double[n + 1][6*n + 1];
        for (int i = 1; i <= 6; i++) {
            dp[1][i] = 1.0 / 6;
        }
        for (int i = 2; i <= n; i++) {
            for (int j = i; j <= 6 * n; j++) {
                for (int k = 1; k <= 6; k++) {
                    if (j > k) {
                        dp[i][j] += dp[i - 1][j - k] * (1.0 / 6);
                    }
                }
            }
        }
        List<Map.Entry<Integer, Double>> list = new ArrayList<>();
        for (int i = n ; i <= 6 * n ; i++) {
            list.add(new AbstractMap.SimpleEntry<Integer, Double>(i, dp[n][i]));
        }
        return list;
    }
}
```

> [1084 · Knight Probability in Chessboard](https://www.lintcode.com/problem/1084/)
>
> On an `N`x`N` chessboard, a knight starts at the `r`-th row and `c`-th column and attempts to make exactly `K` moves. The rows and columns are 0 indexed, so the top-left square is `(0, 0)`, and the bottom-right square is `(N-1, N-1)`.
>
> A chess knight has 8 possible moves it can make, as illustrated below. Each move is two squares in a cardinal direction, then one square in an orthogonal direction.
>
> ![knight](https://assets.leetcode.com/uploads/2018/10/12/knight.png)
>
> Each time the knight is to move, it chooses one of eight possible moves uniformly at random (even if the piece would go off the chessboard) and moves there.
>
> The knight continues moving until it has made exactly `K` moves or has moved off the chessboard. Return the probability that the knight remains on the board after it has stopped moving.
>
> **Example 1:**
>
> ```
> Input: 3, 2, 0, 0
> Output: 0.06
> Explanation: There are two moves (to (1,2), (2,1)) that will keep the knight on the board.
> From each of those positions, there are also two moves that will keep the knight on the board.
> The total probability the knight stays on the board is 0.06.
> ```

```java
public class Solution {
    /**
     * @param N: int
     * @param K: int
     * @param r: int
     * @param c: int
     * @return: the probability
     */
    int[][] dirs = {{1,2},{1,-2},{-1,2},{-1,-2},{2,1},{-2,1},{2,-1},{-2,-1}};
    public double knightProbability(int N, int K, int r, int c) {
        // Write your code here.
        double[][][] dp = new double[K + 1][N][N];
        dp[0][r][c] = 1;
        for (int k = 1; k < K + 1; k++) {
            for (int i = 0; i < N; i++) {
                for (int j = 0; j < N; j++) {
                    for (int[] dir : dirs) {
                        int x = i + dir[0]; 
                        int y = j + dir[1]; 
                        if (valid(x, y, N)) {
                            dp[k][i][j] += dp[k - 1][x][y] * 0.125;
                        }
                    }
                }
            }
        }
        double res = 0;
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                res += dp[K][i][j];

            }
        }
        return res;
    }
    private boolean valid(int x, int y, int N) {
        if (x < 0 || y < 0 || x >= N || y >= N) return false;
        return true;
    }
}
```



### 9 Series Question

#### 9.1 House Robber

198、213、337

```java
class Solution {
    public int rob(int[] nums) {
        if (nums.length <= 1) return nums.length == 0 ? 0 : nums[1];
      	int[] dp = new int[nums.length + 1];
        dp[0] = 0;
        dp[1] = nums[0];
        for (int i = 2; i <= nums.length; i++) {
            dp[i] = Math.max(dp[i - 2] + nums[i - 1], dp[i - 1]);
        }
        return dp[nums.length];
    }
}


//分三种情况 不抢第一家 不抢最后一家；抢第一家 不抢最后一家；不抢第一家 抢最后一家； 

class Solution {
    public int rob(int[] nums) {
        if (nums.length <= 1) return nums.length == 0 ? 0 : nums[0];
        int[] nums1 = Arrays.copyOfRange(nums, 1, nums.length - 1);
        int[] nums2 = Arrays.copyOfRange(nums, 0, nums.length - 1);
        int[] nums3 = Arrays.copyOfRange(nums, 1, nums.length);
        return Math.max(rob1(nums1), Math.max(rob1(nums2), rob1(nums3)));
    }
    public int rob1(int[] nums) {
        if (nums.length <= 1) return nums.length == 0 ? 0 : nums[0];
      	int[] dp = new int[nums.length + 1];
        dp[0] = 0;
        dp[1] = nums[0];
        for (int i = 2; i <= nums.length; i++) {
            dp[i] = Math.max(dp[i - 2] + nums[i - 1], dp[i - 1]);
        }
        return dp[nums.length];
    }
}


  // 树的dp （dfs）：分两种情况。一种是抢这个节点 一种是不抢这个; 
class Solution {
    public int rob(TreeNode root) {
        int[] rootDp = dfs(root);
        return Math.max(rootDp[0], rootDp[1]); //rootDp[0] 代表不抢劫 root 这家；rootDp[1] 代表抢劫 root 这家 
    }
    private int[] dfs(TreeNode node) {
        if (node == null) return new int[]{0, 0};

        int[] lDp = dfs(node.left);
        int[] rDp = dfs(node.right);
        int nonNode = Math.max(lDp[1], lDp[0]) + Math.max(rDp[1], rDp[0]);
        int robNode = node.val + lDp[0] + rDp[0];
        
        return new int[]{nonNode, robNode};
    }
}
```



#### 9.2 Stock

188 123  121 122  714 309

```java
class Solution {
    //at most k, the time of transactions can less than k
    public int maxProfit(int k, int[] prices) {
        int day = prices.length;
        if (k > day / 2) k = day / 2;
        int[][][] dp = new int[day + 1][k + 1][2];
        //初始化 要合理化第 0 天手里有股票的状态，值是min_value/10 （防止溢出）是一个肯定比后面要小的值 max 的时候就被替换了
        for (int i = 0; i <= k; i++) {
            dp[0][i][1] = Integer.MIN_VALUE / 10;
        }

        int max = 0;// 如果一次都不买卖 那就保持 0
        for (int i = 1; i <= day; i++) {
            for (int j = 1; j <= k; j++) {
                dp[i][j][0] = Math.max(dp[i - 1][j][0], dp[i - 1][j][1] + prices[i - 1]);
                dp[i][j][1] = Math.max(dp[i - 1][j][1], dp[i - 1][j - 1][0] - prices[i - 1]);
                max = Math.max(max, dp[i][j][0]);
            }
        }
        return max;
    }
}

//空间优化1，因为后面一天的只是由前面一天推出来的 所以可以只设置一个变量 每天覆盖即可
class Solution {
    //at most k, the time of transactions can less than k
    public int maxProfit(int k, int[] prices) {
        int day = prices.length;
        if (k > day / 2) k = day / 2;
        int[][] dp = new int[k + 1][2];
        //初始化 要合理化第 0 天手里有股票的状态，值是min_value/10 （防止溢出）是一个肯定比后面要小的值 max 的时候就被替换了
        for (int i = 0; i <= k; i++) {
            dp[i][1] = Integer.MIN_VALUE / 10;
        }

        int max = 0;// 如果一次都不买卖 那就保持 0
        for (int i = 1; i <= day; i++) {
            for (int j = 1; j <= k; j++) {
                dp[j][0] = Math.max(dp[j][0], dp[j][1] + prices[i - 1]);
                dp[j][1] = Math.max(dp[j][1], dp[j - 1][0] - prices[i - 1]);
                max = Math.max(max, dp[j][0]);
            }
        }
        return max;
    }
}

//123 k <= 2 了就
class Solution {
    public int maxProfit(int[] prices) {
        int day = prices.length;
        int k = day / 2 >= 2 ? 2 : day / 2;
        int[][] dp = new int[k + 1][2];
        for (int i = 0; i <= k; i++) {
            dp[i][1] = Integer.MIN_VALUE / 10;
        }
        int max = 0;// 如果一次都不买卖 那就保持 0
        for (int i = 1; i <= day; i++) {
            for (int j = 1; j <= k; j++) {
                dp[j][0] = Math.max(dp[j][0], dp[j][1] + prices[i - 1]);
                dp[j][1] = Math.max(dp[j][1], dp[j - 1][0] - prices[i - 1]);
                max = Math.max(max, dp[j][0]);
            }
        }
        return max;   
    }
}
// 121 k = 1 了就
class Solution {
    public int maxProfit(int[] prices) {
        int day = prices.length;
        if (day < 2) return 0;
        int[][] dp = new int[2][2];
        dp[0][1] = Integer.MIN_VALUE / 10;
        dp[1][1] = Integer.MIN_VALUE / 10;
        int max = 0;// 如果一次都不买卖 那就保持 0
        for (int i = 1; i <= day; i++) {
            dp[1][0] = Math.max(dp[1][0], dp[1][1] + prices[i - 1]);
            dp[1][1] = Math.max(dp[1][1], dp[0][0] - prices[i - 1]);
            max = Math.max(max, dp[1][0]);
        }
        return max;
    }
}

//714
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int day = prices.length;
        int[] dp = new int[2];
        dp[1] = Integer.MIN_VALUE / 10;
        int max = 0;// 如果一次都不买卖 那就保持 0
        for (int i = 1; i <= day; i++) {
            dp[0] = Math.max(dp[0], dp[1] + prices[i - 1] - fee);  // 122 k无限 就是要扣个 fee
            dp[1] = Math.max(dp[1], dp[0] - prices[i - 1]);
                max = Math.max(max, dp[0]);
        }
        return max;   
    }
}

//309 
class Solution {
    public int maxProfit(int[] prices) {
        int day = prices.length;
        int[][] dp = new int[day + 1][2];

        //初始化 要合理化第 0 天手里有股票的状态，值是min_value/10 （防止溢出）是一个肯定比后面要小的值 max 的时候就被替换了
        dp[0][1] = Integer.MIN_VALUE / 10;
        dp[1][1] = - prices[0]; //第一天手里有股票那一定是 -prices[0]

        int max = 0;// 如果一次都不买卖 那就保持 0
        for (int i = 2; i <= day; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i - 1]);
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 2][0] - prices[i - 1]);// 只能卖掉两天前买的
            max = Math.max(max, dp[i][0]);
        }
        return max;
    }
}

```



#### 9.3 Painting

256、265、276

[515. Paint House](https://www.lintcode.com/problem/515/solution)

```java
public class Solution {
    /**
     * @param costs: n x 3 cost matrix
     * @return: An integer, the minimum cost to paint all houses
     */
    public int minCost(int[][] costs) {
        if (costs == null || costs.length == 0) return 0;
        // write your code here
        int n = costs.length;
        int[][] dp = new int[2][3];
        dp[0][0] = costs[0][0];
        dp[0][1] = costs[0][1];
        dp[0][2] = costs[0][2];
        for (int i = 1; i < n; i++) {
            dp[i % 2][0] = costs[i][0] + Math.min(dp[(i-1) % 2][1], dp[(i-1) % 2][2]);
            dp[i % 2][1] = costs[i][1] + Math.min(dp[(i-1) % 2][0], dp[(i-1) % 2][2]);
            dp[i % 2][2] = costs[i][2] + Math.min(dp[(i-1) % 2][1], dp[(i-1) % 2][0]);
        }
        return Math.min(dp[(n - 1) % 2][0],  Math.min(dp[(n - 1) % 2][1], dp[(n - 1) % 2][2]));
    }
}
```



### 10 Much more:

> hard ：312、375
>
> 96、95、354



[1869 .Count Square Submatrices with All Ones](https://www.lintcode.com/problem/1869/)

> Given a m * n matrix of ones and zeros, please count and return the number of square submatrix completely composed of 1.
>
>  1 <= arr.length <= 300
>
>  1 <= arr[0].length <= 300
>
> **Example 1:**
>
> ```
> Input: 
> matrix =
> [
>   [0,1,1,1],
>   [1,1,1,1],
>   [0,1,1,1]
> ]
> Output: 
> 15
> Explanation: 
> There are 10 squares of side 1.
> There are 4 squares of side 2.
> There is  1 square of side 3.
> Total number of squares = 10 + 4 + 1 = 15.
> ```

```java
//Tips: 状态：use dp[i][j] to count the size of the square 1 submatrix whose right bottom is matrix[i][j], and the size of submatrix is just the number of square submatrix it contains.
// 方程 ：dp[i][j] = min(dp[i-1][j-1], up[i-1][j], left[i][j-1]) up 和 left 代表(包括自己)连续上面1 的个数以及左边连续 1 的个数
// 初始化：可以把左边和上面的边都初始化为 matrix 值（很好理解）

public class Solution {
    /**
     * @param matrix: a matrix
     * @return: return how many square submatrices have all ones
     */
    public int countSquares(int[][] matrix) {
        int row = matrix.length;
        int col = matrix[0].length;
        int[][] dp = new int[row][col];
        int[][] up = new int[row][col];
        int[][] left = new int[row][col];

        for (int i = 0; i < row; i++) {
            dp[i][0] = left[i][0] = matrix[i][0];
        }
        for (int i = 0; i < col; i++) {
            dp[0][i] = up[0][i] = matrix[0][i];
        }
      
        for (int i = 1; i < row; i++) { 
            for (int j = 1; j < col; j++) {
                if (matrix[i][j] == 0) continue;
                    left[i][j] = left[i][j - 1] + 1;
                    up[i][j] = up[i - 1][j] + 1;
                    dp[i][j] = Math.min(dp[i - 1][j - 1]+1, Math.min(left[i][j], up[i][j]));
            }
        }
        int res = 0;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                res += dp[i][j];
            }
        }
        return res;
    }
}
```



区间型动态规划有点太难了 有时间做stone game 系列
