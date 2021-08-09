## 贪心算法

- 贪心算法：每一个选择都作出当时看起来最佳的选择，总是作局部最优解，”寄希望于这样能取得全局最优解“。

- 应用：最小生成树、dijistra等等。

#### [435. Non-overlapping Intervals](https://leetcode-cn.com/problems/non-overlapping-intervals/)

```java
public int eraseOverlapIntervals(int[][] intervals) {
        /*
        Time: O(nlogn) Space: O(1)
        仿算法导论16.1，在结束时间排序的情况下，最长无重复内容就是每次都取结束时间最早的内容。
        */
        Arrays.sort(intervals, (i1, i2) -> i1[1] - i2[1]);
        int canWork = 1;
        int curr = intervals[0][1];
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] >= curr) {
                canWork++;
                curr = intervals[i][1];
            }
        }
        return intervals.length - canWork;
    }
```

- 贪心思路

设计算法：每次都取最先结束的。（数组已经按照结束时间排序了）

所有与a1兼容的活动都必须在a1结束之后开始。

接下来需要证明的是：最早结束的活动一定是最优解一部分吗?

> 定理16.1: 任意非空子问题Sk, 令am是Sk中结束时间最早的活动，则am在Sk的某个最大兼容子集里。

可以用集合的方法证明：除去am以外的数目应该是n-1，由于am结束最早，其他活动最多也只能有一个，因此最多还是n。



## 贪心算法原理

一般而言，贪心算法可以直接按三步来设计：

1. 将最优化问题转化为这样的形式：对其作出一次选择之后，只剩下一个子问题需要求解；
2. 证明做出贪心选择之后，原问题总是存在最优解，即贪心选择总是安全的；
3. 证明做出贪心选择之后，剩余的子问题满足性质：其最优解与贪心选择组合即可得到原问题的最优解，这样就得到了**最优子结构**。

两个关键点分别是**贪心选择性质与最优子结构。**

- 贪心选择性质

  Greedy-choice-property：我们可以通过局部最优（贪心）选择来构造全局最优解。

  这也是与动态规划不同的地方，动态规划需要先知道子问题的解，而贪心则不这样，直接做出当时看起来最佳的选择，而不是依赖任何将来的选择或者是子问题的解。

  问题就在于：这样的行为都能生成全局最优解。

  如果进行贪心选择时，不得不考虑众多选择，往往说明可以改进贪心选择，使得其更为高效。

- 最优子结构

  如果一个问题的最优解包含其子问题的最优解，则称此问题具有最优子结构性质。

  在贪心算法这里，可以简化为论证：ak与Skj的最优解能否变成Sij的最优解。



## 一些奇奇怪怪的贪心题

#### [134. Gas Station](https://leetcode-cn.com/problems/gas-station/)

```java
//贪心算法：如果 sum >= 0 那么一定有一个 start 如果count 为负数 说明上一个设置的start一定不行 那就把start换到下一个点
class Solution {
    //Time: O(n) Space :O(1) 
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int count = 0;  //看从新设置的起始点会不会出现负数
        int start = 0;  //标记起始点的位置
        int sum = 0;  //如果总的 sum < 0 说明一定不能走完全程
        for (int i = 0; i < gas.length; i++) {
            count += gas[i] - cost[i];
            sum += gas[i] - cost[i];
            if (count < 0) {
                count = 0;
                start = i == gas.length - 1? 0 : i + 1;
            }
        }
        return sum < 0 ? -1 : start;
    }
}
```



#### [5840. Minimum Number of Swaps to Make the String Balanced](https://leetcode-cn.com/problems/minimum-number-of-swaps-to-make-the-string-balanced/)

```java
//贪心：左括号一定比右括号多 所以当right > left时候 就把最右边的左括号换过来()
class Solution {
    public int minSwaps(String s) {
        int left = 0, right = 0;
        int res = 0;
        for (char c : s.toCharArray()) {
            if (c == '[') left++;
            else right++;
            if (right > left) {
                res++;
                left++;
                right--;
            }
        } 
        return res;
    }
}
```



#### [300. Longest Increasing Subsequence](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

```java
//贪心 + 二分法解决最长递增子序列问题
//贪心思想 总是把小数放到数组里面 只有改变了长度才更新答案
//每个数字放入的位置代表以当前数为结尾的递增数组的长度 每个数组都是顺序的 顺序不会错乱
class Solution {
    //Time : O(nlogn)
    //Space: O(n)
    public int lengthOfLIS(int[] nums) {
        List<Integer> list = new ArrayList<>();
        list.add(nums[0]);
        for (int num : nums) {
            int lower_bound = lower_bound(list, num);
            if (lower_bound == list.size()) list.add(num);
            else list.set(lower_bound, num);
        }
        return list.size();
    }

    private int lower_bound(List<Integer> list , int num){
        int left = 0, right = list.size();
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (num > list.get(mid)) left = mid + 1;  
            else right = mid;
        }
        return left;
    }
}
```



#### [5841. Find the Longest Valid Obstacle Course at Each Position](https://leetcode-cn.com/problems/find-the-longest-valid-obstacle-course-at-each-position/)

```java
//贪心 + 二分法解决最长递增子序列问题
class Solution {
    //Time : O(nlogn)
    //Space: O(n)
    public int[] longestObstacleCourseAtEachPosition(int[] obstacles) {
        int[] res = new int[obstacles.length];
        List<Integer> list = new ArrayList<>();
        list.add(obstacles[0]);
        res[0] = 1;
        for (int i = 1; i < obstacles.length; i++) {
            int upper_bound = upper_bound(list, obstacles[i]);
            if (upper_bound == list.size()) list.add(obstacles[i]);
            else list.set(upper_bound, obstacles[i]);
            res[i] = upper_bound + 1;
        }
        return res;
    }

    private int upper_bound(List<Integer> list , int num){
        int left = 0, right = list.size();
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (num >= list.get(mid)) left = mid + 1;  
            else right = mid;
        }
        return left;
    }
}
```

