### 利用“时间复杂度倒推法”和“前缀和”技巧

> 我们来一起一步步分析和解决两道最新的大厂面试真题
>
> LintCode 1844:求和＝k的最短的子数组
>
> LintCode 1507:求和＞＝k最短的子数组



**先修知识点：subarray vs subsequence**

一个长度为n的数组，子数组 SubArray 和子序列 SubSequence 分别有多少个不同的子数组?

A: O (n^2) . O (2^n)



**先修知识点：最大子数组要使得prefixSum[j+1]-prefixSum[i]最大**

对于给定j,找到ifrom 0 to j之间的“最小”的 prefixSum[i]

算法中经常会出现找最大值时要通过最小值去找

LintCode 41: Maximum Subarray

LintCode 545: Top K Largest Number ll



#### [LintCode 1844.求和＝k的最小子数组](https://www.lintcode.com/problem/subarray-sum-equals-to-k-ii/)

暴力算法(n^3 n^2)－＞和面试官交流－＞时间复杂度倒推(n 双指针)－＞bingo!

