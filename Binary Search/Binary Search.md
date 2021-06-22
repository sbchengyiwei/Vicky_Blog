### Template

Three are three templates for Binart Search:

>  Mainly focus on the items which pointers is on when the loop is over, whether they are judged!

![](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/images/Screen%20Shot%202021-05-17%20at%203.56.54%20PM.png)

Ps: 从做题感受上来看 第三种模板出错的概率比较小 一般优先考虑 第一种适合简单的题 第二种适合一些特殊的题

所以模板考虑顺序一般是 3 -> 1 -> 2



### Hard 题

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
