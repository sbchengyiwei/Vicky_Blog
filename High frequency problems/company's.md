## Content

- [FaceBook -- Dp](#facebook----dp)
  - [139. Word Break](https://leetcode-cn.com/problems/word-break/)
  - [1269. Number of Ways to Stay in the Same Place After Some Steps](https://leetcode-cn.com/problems/number-of-ways-to-stay-in-the-same-place-after-some-steps/)
- [Microsoft -- Two Pointers](#microsoft----two-pointers)
  - [1879 · Two Sum VII](https://www.lintcode.com/problem/1879/)
- [Amazon -- Shortest path(BFS)](#amazon----shortest-path-bfs-)
  - [1563 · Shortest path to the destination](https://www.lintcode.com/problem/1563)
- [Google -- Dp &Two Pointers & Partition](#google----dp--two-pointers---partition)
  - [1833 · pen box](https://www.lintcode.com/problem/1833/)
  - [123. Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/) (similar)



### FaceBook -- Dp

[139. Word Break](https://leetcode-cn.com/problems/word-break/)

> Given a string `s` and a dictionary of strings `wordDict`, return `true` if `s` can be segmented into a space-separated sequence of one or more dictionary words.
>
> **Note** that the same word in the dictionary may be reused multiple times in the segmentation.
>
> **Example 1:**
>
> ```
> Input: s = "leetcode", wordDict = ["leet","code"]
> Output: true
> Explanation: Return true because "leetcode" can be segmented as "leet code".
> ```

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if (s == null) return false;
        boolean[] dp = new boolean[s.length() + 1];
        dp[0] = true;
        for (int i = 1; i <= s.length(); i++) {
            for (int j = 0; j < i; j++) {
                dp[i] = dp[j] && wordDict.contains(s.substring(j, i));
                if (dp[i] == true) break;
            }
        }
        return dp[s.length()];
    }
}
```

[1269. Number of Ways to Stay in the Same Place After Some Steps](https://leetcode-cn.com/problems/number-of-ways-to-stay-in-the-same-place-after-some-steps/)

```java
//draw the grid and optimize space complexity
class Solution {
    public int numWays(int steps, int arrLen) {
        final int MODULO = 1000000007;
        int[][] dp = new int[2][arrLen];
        dp[0][0] = 1;
        for (int i = 1; i <= steps; i++) {
            for (int j = 0; j <= i && j < arrLen; j++) {
                dp[i % 2][j] = dp[(i - 1) % 2][j];
                if (j - 1 >= 0) dp[i % 2][j] = (dp[i % 2][j] + dp[(i - 1) % 2][j - 1]) % MODULO;
                if (j + 1 < arrLen) dp[i % 2][j] = (dp[i % 2][j] + dp[(i - 1) % 2][j + 1]) % MODULO;
            }
        }
        return dp[steps % 2][0];
    }
}
```



### Microsoft -- Two Pointers

[1879 · Two Sum VII](https://www.lintcode.com/problem/1879/)

>Given an array of integers that is already **sorted in ascending absolute order**, find two numbers so that the sum of them equals a specific number.
>
>The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Note: the subscript of the array starts with 0
>
>You are not allowed to sort this array.
>
>**Example**
>
>```
>Input: 
>[0,-1,2,-3,4]
>1
>Output: 
>[[1,2],[3,4]]
>Explanation: 
>nums[1] + nums[2] = -1 + 2 = 1, nums[3] + nums[4] = -3 + 4 = 1
>You can return [[3,4],[1,2]], the system will automatically help you sort it to [[1,2],[3,4]]. But [[2,1],[3,4]] is invaild.
>```

```java
//The ascending order of the absolute value of negative numbers is just the opposite of positive numbers.
//Use two function to find the least number and largest number.(ues the principle up there)
public class Solution {
    // O(n) O(1)
    public List<List<Integer>> twoSumVII(int[] nums, int target) {
        // write your code here
        List<List<Integer>> ans = new ArrayList<>();
        if (nums.length == 0) return ans;
        int left = 0, right = 0;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > nums[right]) right = i;
            if (nums[i] < nums[left]) left = i;
        }
        while (nums[left] < nums[right]) {
            if (nums[left] + nums[right] < target) {
                left = nextleft(left, nums);
                if (left == -1) break;
            } else if (nums[left] + nums[right] > target) {
                right = nextright(right, nums);
                if (right == -1) break;
            } else {
                List<Integer> temp = new ArrayList<>();
                if (left < right) {
                    temp.add(left);
                    temp.add(right);
                } else {
                    temp.add(right);
                    temp.add(left);
                }
                ans.add(temp);
                left = nextleft(left, nums);
                if (left == -1) break;
            }
        }
        return ans;
    }

    public int nextleft(int left, int[] nums) {  // not O(n) the amortized time is O(1)
        if (nums[left] < 0) {
            for (int i = left - 1; i >= 0; i--) {
                if (nums[i] < 0) return i;
            } 
            for (int i = 0; i < nums.length; i++) {
                if (nums[i] >= 0) return i;
            }
            
        }
        for (int i = left + 1; i < nums.length; i++) {
            if (nums[i] >= 0) return i;
        }
        return -1;
    }

    public int nextright(int right, int[] nums) {
        if (nums[right] > 0) {
            for (int i = right - 1; i >= 0; i--) {
                if (nums[i] > 0) return i;
            } 
            for (int i = 0; i < nums.length; i++) {
                if (nums[i] <= 0) return i;
            }
        }
        for (int i = right + 1; i < nums.length; i++) {
            if (nums[i] <= 0) return i;
        }
        
        return -1;
    }
}

```



### Amazon -- Shortest path(BFS)

[1563 · Shortest path to the destination](https://www.lintcode.com/problem/1563)

> Given a 2D array representing the coordinates on the map, there are only values `0, 1, 2` on the map. `value 0` means that it can pass, `value 1` means not passable, `value 2` means target place. Starting from the coordinates `[0,0]`,You can only go up, down, left and right. Find the shortest path that can reach the destination, and return the length of the path.
>
> **Example 1**
>
> ```
> Input:
> [
>  [0, 0, 0],
>  [0, 0, 1],
>  [0, 0, 2]
> ]
> Output: 4
> Explanation: [0,0] -> [1,0] -> [2,0] -> [2,1] -> [2,2]
> ```

```java
/*
BFS
1.directions
2.queue
3.visited
4.step

Code:
queue.offer(start)
visited(start)
while (not empty) {
    int size
    for (size)
        pop
        if destination reutrn step
        for dirs 
            if valid!!!!
                queue.add()
                visited+

    step++;
}
*/
public class Solution {
    int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    public int shortestPath(int[][] targetMap) {
        // Write your code here
        int row = targetMap.length;
        int col = targetMap[0].length;
        Queue<int[]> queue = new ArrayDeque<>();
        boolean[][] visited = new boolean[row][col];
        int step = 0;
        queue.offer(new int[]{0,0}); //from[0][0]
        visited[0][0] = true; //from[0][0]
        while(!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int[] cur = queue.poll();
                if(targetMap[cur[0]][cur[1]] == 2) return step;
                for (int[] dir : dirs) {
                    int x = cur[0] + dir[0], y = cur[1] + dir[1];
                    if (x >= 0 && y >= 0 && x < row && y < col && targetMap[x][y] != 1 && visited[x][y] == false) { // not '1'
                        queue.offer(new int[]{x, y});
                        visited[x][y] = true; //
                    }
                }
            }
            step++; 
        }
        return -1;
    }
}
```



### Google -- Dp & PreSum & Two Pointers & Partition

**PreSum & Two Pointers 结合的时候 presum可以在过程中用 sum 存储 不一定要用数组另外一个 for 循环存储**

[1833 · pen box](https://www.lintcode.com/problem/1833/)

> Given you an array boxes and a target. Boxes[i] means that there are boxes[i] pens in the ith box. Subarray [i, j] is valid if sum(boxes[i] + boxes[i+1] + ... + boxes[j]) == target. Please find two not overlapped valid subarrays and let the total length of the two subarrays minimum. Return the minimum length. If you can not find such two subarrays, return -1.
>
> boxes.length <= 10 ^ 6 and boxes[i] > 0
>
> **Example 1**
>
> ```
> Input:
> boxes = [1,2,2,1,1,1],
> target = 3
> Output: 
> 4
> ```

```java
public class Solution {
    /**
     * partition two part and calculate preSumLeft[i] and preSumRight[i + 1] separately
     */
  
  	// 5 for loops, but time complexity still is O(n)
    public int minimumBoxes(int[] boxes, int target) {
        if (boxes == null && boxes.length < 2) return -1;
        int n = boxes.length;
        int[] preSumLeft = new int[n + 1];
        int[] preSumRight = new int[n + 1];
        preSumLeft[0] = 0;
        preSumRight[0] = 0;
        for (int i = 1; i < n + 1; i++) {
            preSumLeft[i] += boxes[i - 1] + preSumLeft[i - 1];
        }
        for (int i = n - 1; i >= 0; i--) {
            preSumRight[i] += boxes[i] + preSumLeft[i + 1];
        }

        int[] minLeft = new int[n + 1];  // add minLeft from left
        Arrays.fill(minLeft, Integer.MAX_VALUE / 10); // will plus after, prevent memory overflow
        for(int i = 1, j = 0; i < n + 1; i++) {
            while (preSumLeft[i] - preSumLeft[j] > target) {
                j++;
            }
            if (preSumLeft[i] - preSumLeft[j] == target) minLeft[i] = Math.min(minLeft[i - 1], i - j);
        }
        int[] minRight = new int[n + 1];  // add minLeft from left
        Arrays.fill(minRight, Integer.MAX_VALUE / 10); // will plus after, prevent memory overflow
        for(int i = n - 1, j = n - 2; i >= 0; i--) {
            while (preSumRight[i] - preSumRight[j] > target) {
                j--;
            }
            if (preSumRight[i] - preSumRight[j] == target) minRight[i] = Math.min(minRight[i + 1], i - j);
        }

        int min = Integer.MAX_VALUE;
        for(int i = 0; i < n + 1; i++) {
            min = Math.min(min, preSumLeft[i] + preSumRight[i + 1]);
        }
        return min >= Integer.MAX_VALUE / 10 ? -1 : min;  
    }
}
```

```java
//Merge steps to reduce time complexity
//use dp[] replace preSumRight[i]
//sum plus and minus when calculate the len
//use ans save preSumLeft[i]
//only two while loops
public class Solution {
    public int minimumBoxes(int[] boxes, int target) {
        //partation
        //use space save time
        if (boxes == null || boxes.length < 2) return -1;
        
        int[] dp = new int[boxes.length];
        Arrays.fill(dp, Integer.MAX_VALUE / 2);
        int sum = 0;
        for (int i = 0, j = 0; i < boxes.length; i++) {
            sum += boxes[i];
            while (sum > target) {
                sum -= boxes[j++];
            }
            if (i - 1 >= 0) dp[i] = dp[i - 1];
            if (sum == target) {dp[i] = Math.min(dp[i], i - j + 1);}
        }
        sum = 0;
        int ans = Integer.MAX_VALUE;
        for (int i = boxes.length - 1, j = boxes.length - 1; i >= 1; i--) {
            sum += boxes[i];
            while (sum > target) {
                sum -= boxes[j--];
            }
            if (sum == target) {ans = Math.min(j - i + 1 + dp[i - 1], ans);}
        }
        return ans >= Integer.MAX_VALUE / 2 ? -1 : ans; 
    }
}
```



**Similar question**

[123. Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)

> You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.
>
> Find the maximum profit you can achieve. You may complete **at most two transactions**.
>
> **Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again). 
>
> **Example 1:**
>
> ```
> Input: prices = [3,3,5,0,0,3,1,4]
> Output: 6
> Explanation: Buy on day 4 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3.
> Then buy on day 7 (price = 1) and sell on day 8 (price = 4), profit = 4-1 = 3.
> ```

```java
class Solution {
    public int maxProfit(int[] prices) {
        if(prices == null || prices.length < 2) return 0;
        int min = prices[0];
        int[] dp = new int[prices.length];
        dp[0] = 0;
        for (int i = 1; i < prices.length; i++) {
            min = Math.min(min, prices[i]);
            dp[i] = Math.max(dp[i - 1], prices[i] - min);
        }
        
        int max = prices[prices.length - 1];
        int ans = 0;
        for (int i = prices.length - 2; i >= 0; i--) {
            max = Math.max(max, prices[i]);
            if (i - 1 < 0) ans = Math.max(ans,max - prices[i]);
            else ans = Math.max(ans, dp[i - 1] + max - prices[i]);// i - 1 < 0
        }  
        return ans;
    }
}
```

