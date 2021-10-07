Merge Sort ：适用于需要考虑**前后顺序**的，可以**拆分**来解决的。
注意：前后顺序不是不能更改，只是可以在更改前（拆分后的一次次merge 时）就进行结果的判断和存储。



`MergeSort` review:

```java
public static void mergeSort(int[] nums) {
    helper(nums, 0, nums.length - 1);
} 
12345
private static void helper(int[] nums, int lo, int hi) {
    if (lo >= hi) return;
    int mid = lo + (hi - lo) / 2;
    helper(nums, lo, mid);
    helper(nums, mid + 1, hi);
    merge(nums, lo, mid, hi);
}

private static void merge(int[] nums, int lo, int mid, int hi) {
    int[] merge = new int[hi - lo + 1]; int index = 0;
    int p = lo; int q = mid + 1;
    while (q <= hi && p <= mid) {
        if (nums[p] >= nums[q]) {
            merge[index++] = nums[q++];
        } else merge[index++] = nums[p++];
    }
    while (q <= hi) {
        merge[index++] = nums[q++];
    }
    while (p <= mid) {
        merge[index++] = nums[p++];
    }
    System.arraycopy(merge,0,nums,lo,merge.length);
}
```

---



#### [493. Reverse Pairs](https://leetcode-cn.com/problems/reverse-pairs/)

要求 nums[p] > 2L * nums[r]
遍历前面的 p 匹配后面的 r **一个 p 对应一个 r 的范围**

```java
class Solution {
    public int reversePairs(int[] nums) {
        return helper(nums, 0, nums.length - 1);
    }
    private int helper(int[] nums, int lo, int hi) {
        if (lo >= hi) return 0;
        int mid = lo + (hi - lo) / 2;
        int res = helper(nums, lo, mid) + helper(nums, mid + 1,hi);
        // 在 helper() 里计算 res 计算完再 merge
        int p = lo;  int r = mid + 1; 
       // 一般都是遍历前半段 匹配后半段  （反之也可以）
        while (p <= mid) { 
            while ( r <= hi && nums[p] > 2L * nums[r])  r++; 
            res += r - (mid + 1);
            p++;   
        }
        merge(nums, mid, lo, hi);
        return res;
    }
    
    private void merge(int[] nums, int mid, int lo, int hi) {
        int[] merge = new int[hi - lo + 1]; int index = 0;
        int p = lo; int q = mid + 1;
        while (q <= hi && p <= mid) {
            if (nums[p] >= nums[q]) {
                merge[index++] = nums[q++];
            } else merge[index++] = nums[p++];
        }
        while (q <= hi) {
            merge[index++] = nums[q++];
        }
        while (p <= mid) {
            merge[index++] = nums[p++];
        }
        System.arraycopy(merge,0,nums,lo,merge.length);
    }
}


```


#### [327. Count of Range Sum](https://leetcode-cn.com/problems/count-of-range-sum/)

Solution 1：要求 lower <= sum[r] - sum[p] <= upper (参考 429 r 是后面的元素 p 是前面的元素)
遍历前面的 p 匹配后面的r  **一个前面的 p 对应一个后面 r 的范围** 
又因为前面的排好序了 只要知道最小和最大的范围就好

```java
class Solution {
    public int countRangeSum(int[] n, int lower, int upper) {
        long[] nums = new long[n.length + 1];
        for (int i = 1; i <= n.length; i++) nums[i] = nums[i - 1] + n[i - 1];
        return helper(nums, 0, nums.length - 1, lower, upper);
    }
    private int helper(long[] nums, int lo, int hi, int lower, int upper) {
        if (lo >= hi) return 0;
        int mid = lo + (hi - lo) / 2;
        int res = helper(nums, lo, mid,lower, upper) + helper(nums, mid + 1,hi,lower, upper);
        // 在 helper() 里计算 res 计算完再 merge
        int p = lo;  int start = mid + 1; int end = mid + 1; //从后半段开始是 mid + 1
       // 一般都是遍历前半段 匹配后半段  
        while (p <= mid) { 
            while (start <= hi && lower > nums[start] - nums[p]) start++;
            while (end <= hi && nums[end] - nums[p] <= upper) end++;
            res += end - start;
            p++;   
        }
        merge(nums, mid, lo, hi);
        return res;
    }
    
    private void merge(long[] nums, int mid, int lo, int hi) {
        long[] merge = new long[hi - lo + 1]; int index = 0;
        int p = lo; int q = mid + 1;
        while (q <= hi && p <= mid) {
            if (nums[p] >= nums[q]) {
                merge[index++] = nums[q++];
            } else merge[index++] = nums[p++];
        }
        while (q <= hi) {
            merge[index++] = nums[q++];
        }
        while (p <= mid) {
            merge[index++] = nums[p++];
        }
        System.arraycopy(merge,0,nums,lo,merge.length);
    }
}

```


Solution 2: `TreeMap `利用 sum[i] - lower <= sum[j] <= sum[] - upper   (先 i 后 j)在这个范围的sum[j] 到 sum[i] 的和都满足 题目的条件

```java
class Solution {
    public int countRangeSum(int[] n, int lower, int upper) {
        if (n == null || n .length == 0) return 0;
        TreeMap<Long, Long> treeMap = new TreeMap<>();   //前面不要写 Map 因为 Map 没有 submap 的方法  <> 里的 Long 是大写 和写在里面的 Integer 一样
        long sum = 0;
        int res = 0;
        treeMap.put((long)0, (long)1); //前 0 项和为 0
        for (int i = 0; i < n.length; i++) {
            sum += n[i];
            Map<Long, Long> subMap = treeMap.subMap(sum - upper, true, sum - lower, true);
            for (long value : subMap.values()) {
                res += value;
            }
            treeMap.put(sum, treeMap.getOrDefault(sum, (long)0) + 1);
        }
        return res;
    }
}
```



#### [315. Count of Smaller Numbers After Self](https://leetcode-cn.com/problems/count-of-smaller-numbers-after-self/)

```java
class Solution {
    public static List<Integer> countSmaller(int[] nums) {
        Integer[] res = new Integer[nums.length];
        //因为最后要输出准确的结果 nums 本身的顺序变了之后，就不知道原来的index是多少，res[index] 不准
        //所以把 index另外设一个数组，merge也merge index[]，比大小时候用 nums[index[i]]，存结果用res[index[i]] 
        int[] index = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            index[i] = i;
        }
       //Arrays.fill(res, 0);
        helper(nums, res, index,0, nums.length - 1);
        return Arrays.asList(res);
    }
    private static void helper(int[] nums, Integer[] res, int[] index, int lo, int hi) {
        if (lo >= hi) return;
        int mid = lo + (hi - lo) / 2;
        helper(nums, res, index,lo, mid);
        helper(nums, res, index,mid + 1, hi);
        // 在 helper() 里计算 res 计算完再 merge
        int p = lo;  int r = mid + 1;
        // 一般都是遍历前半段 匹配后半段  （反之也可以）
        while (p <= mid) {
            while ( r <= hi && nums[index[p]] > nums[index[r]])  r++;
            res[index[p]] += r - (mid + 1);
            p++;
        }
        merge(nums, index, mid, lo, hi);
    }

    private static void merge(int[] nums, int[] index, int mid, int lo, int hi) {
        int[] merge = new int[hi - lo + 1]; int n = 0;
        int p = lo; int q = mid + 1;
        while (q <= hi && p <= mid) {
            if (nums[index[q]] >= nums[index[p]]) {
                merge[n++] = index[p++];
            } else merge[n++] = index[q++];
        }
        while (q <= hi) {
            merge[n++] = index[q++];
        }
        while (p <= mid) {
            merge[n++] = index[p++];
        }
        System.arraycopy(merge,0,index,lo,merge.length);
    }
}
```

![](https://trello-attachments.s3.amazonaws.com/6072d0d2385f2e81619166e2/608536fa17d49171a6e84851/705a446ca8f8cfe0879ff2fddb2a926e/IMG_0827.jpg)