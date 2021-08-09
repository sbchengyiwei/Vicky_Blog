### Template

Three are three templates for Binart Search:

>  Mainly focus on the items which pointers is on when the loop is over, whether they are judged!

1 left <= right  ; left = 0 right = size - 1; right = mid - 1 left = mid + 1 

2 left < right  ; left = 0 right = size;    right = mid   left = mid + 1 (正常情况下取 lower_bound, 要取 upper_bound需要input+1 然后结果-1)

3 left  + 1/1e-5 < right  ; left = 0 right = size - 1; right = mid   left = mid     



`适用性：` length >= 2 就都可以用 length == 1 只有 1 可以直接用 其余两个要另外判断

2 适用于找出分界线并返回的题（lower or upper）或者整数二分答案时使用 

3 比较普适放心用  或者 浮点数二分答案时使用

1 不需要额外再判断 注意范围越界问题



### 使用两个 bound 解题

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



### 不能用 bound 解的 Hard 题

- LeetCode 4 Median of Two Sorted Arrays

![](https://trello-attachments.s3.amazonaws.com/6072d0d2385f2e81619166e2/607f13164b1c1033bf5a2ca8/x/8debdec2289e82ae283511bfab609273/Screen_Shot_2021-04-19_at_6.29.48_PM.png)

> 时间复杂度是 log(min((m, n)): 因为总数组长度固定 中位数的位置是一半总长 那么只要知道最小的数组划分到左边的长度 就知道长的数组划分到左边的长度了

```java
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



- LeetCode 33 Search in Rotated Sorted Array

> 重点是如何理解只要看 `nums[low] < nums[mid] ` 就能判断左边是否单调：画图：见p1
>
> 注意：
> 1）All values of nums are unique 
> 2）orignal array are sorted in ascending order.

```java
class Solution {
    public int search(int[] nums, int target) {
        int low = 0;
        int high = nums.length - 1;
        while(low + 1 < high) {
            int mid = (high - low) / 2 + low;
            if (target == nums[mid]) return mid;
           // 先看单调的区域  如果nums[low] < nums[mid] 那么左边单调 否则右边单调 然后就可以判断 target 在不在这个区域了
            if (nums[low] <= nums[mid]) {
                if (target < nums[mid] && target >= nums[low]) {
                    high = mid - 1;
                } else low = mid + 1;
            } else {
                if (target <= nums[high] && target > nums[mid]) {
                    low = mid + 1;
                } else high = mid - 1;
            }
        }
        if (nums[low] == target) return low;
        if (nums[high] == target) return high;
        return -1;  
    }
}
```

![](https://trello-attachments.s3.amazonaws.com/6072d0d2385f2e81619166e2/607f0e4ba0547e2990089d95/x/12b63cc152604461f33bbb764be01cf2/IMG_15CD7576DFE7-1.jpeg)





### 二分答案

[617 · Maximum Average Subarray II](https://www.lintcode.com/problem/617/description?_from=collection&fromId=178)

> Given an array with positive and negative numbers, find the `maximum average subarray` which length should be greater or equal to given length `k`.
>
> It's guaranteed that the size of the array is greater or equal to *k*.
>
> Example 1:
>
> ```
> Input:
> [1,12,-5,-6,50,3]
> 3
> Output:
> 15.667
> Explanation:
>  (-6 + 50 + 3) / 3 = 15.667
> ```

#### 算法：二分答案

本题看到以后先想到暴力，即枚举所有可能子数组，时间复杂度O(N^2)会超时，于是我们考虑更低复杂度的做法，是否有时间复杂度O(NlogN)级别的做法呢？我们考虑二分答案来解决问题。

##### 算法思路

- 我们考虑二分平均值，那么我们需要一个`check`函数，能在O(N)复杂度内判断是否存在一个子数组的平均值大于等于我们二分出来的平均值
- 对于一个平均数`avg`，我们先**将`nums`数组每个数减去`avg`**，那么只要存在一个长度大于`k`的子数组和大于等于0，就说明平均数`avg`可行，这可以在O(N)时间内完成

##### 代码思路

1. 设置二分的左右边界分别为数组中的最小值和最大值
2. 判断平均值`mid`是否可行，若可行则说明答案大于等于`mid`，那么左边界等于`mid`，否则说明答案小于`mid`，右边界等于`mid`

- 如何判断平均值`mid`是否可行：
  1. 将`nums`数组每个数减去`mid`
  2. 求`nums`数组的前缀和数组`pre`
  3. 设置指针`index`等于k
  4. 那么在`nums[0:index]`中，长度大于`k`的子数组，区间和最大为`pre[index - 1] - min{pre[0 : index - k]}`
  5. 将`index`不断右移直到指向数组末端，若中间区间和最大值大于等于`0`，`check`函数直接返回`True`，结束后还为返回值则返回`False`

1. 不断重复 2 直到 `left + 1e-5 == right` 退出
2. 返回左边界

#### 复杂度分析

N*N*表示`nums`数组长度，max\_nums*ma**x*_*n**u**m**s*和min\_nums*min*_*n**u**m**s*分别表示数组中最大值和最小值

- 空间复杂度：O(1)

实际处理时不需要记录下整个前缀和数组，只需记录当前的前缀和和左侧最小的前缀和

- 时间复杂度：O(Nlog(max\_nums - min\_nums))

```java
public class Solution { //二分答案法
    /**
     * @param nums: an array with positive and negative numbers
     * @param k: an integer
     * @return: the maximum average
     */
    public double maxAverage(int[] nums, int k) {
        double max = nums[0];
        double min = nums[0];
        for (int num : nums) {
            max = Math.max(num, max);
            min = Math.min(num, min);
        }
        while (min + 1e-5 < max) { //否则会一直二分下去
            double mid = min + (max - min) / 2;
            if (check(mid, nums, k)) min = mid;
            else max = mid;  
        }
        return min;
    }

    private boolean check(double avg, int[] nums, int k) {
        //rightSum表示当前指向位置的前缀和
        //leftSum表示当前指向位置左侧k个位置的前缀和
        //minLeftSum表示左侧最小的前缀和

        double rightSum = 0, leftSum = 0, minLeftSum = 0;
        for (int i = 0; i < k; i++) {
            rightSum += nums[i] - avg;
        }
        if (rightSum - minLeftSum >= 0) {
                return true;
        }

        for (int i = k; i < nums.length; i++) {
            rightSum += nums[i] - avg;
            leftSum += nums[i - k] - avg;
            minLeftSum = Math.min(minLeftSum, leftSum);

            if (rightSum - minLeftSum >= 0) {
                return true;
            }
        }
        return false;

    } 
}
```

[1507 · Shortest Subarray with Sum at Least K](https://www.lintcode.com/problem/1507/description?_from=collection&fromId=178)

> Return the **length** of the shortest, non-empty, contiguous subarray of `A` with sum at least `K`.
>
> If there is no non-empty subarray with sum at least `K`, return `-1`.
>
> **Example 1:**
>
> ```
> Input: A = [1], K = 1
> Output: 1
> ```
>
> **Example 2:**
>
> ```
> Input: A = [1,2], K = 4
> Output: -1
> ```

```java
public class Solution {
    /**
     * @param A: the array
     * @param K: sum
     * @return: the length
     */
    public int shortestSubarray(int[] A, int K) {
        // Write your code here.
        int[] preSum = new int[A.length + 1];
        preSum[0] = 0;
        for (int i = 0; i < A.length; i++) {   
            preSum[i + 1] = A[i] + preSum[i];
        }
        int min = 1, max = A.length + 1;
        while (min < max) {
            int mid = min + (max - min) /  2;
            if (check(mid, preSum, K)) max = mid;
            else min = mid + 1;
        }
        if (min == A.length+1) return -1;
        else return min;
    }

    private boolean check(int len, int[] preSum, int K) {
        TreeSet<Integer> set = new TreeSet<>();
        for (int i = 0; i < len; i++) {
            set.add(preSum[i]);
        }
        for (int i = len; i < preSum.length; i++) {
            if (preSum[i] - set.first() >= K) return true;
            set.remove(preSum[i - len]);
            set.add(preSum[i]);
        }
        return false;
    }
}
```


