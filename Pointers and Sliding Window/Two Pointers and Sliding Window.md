### BASIC

> Two pointers make up a sliding window

Two moving ways:  Same direction and opposite direction

Why two pointers: save time(same as hash and stack)

**How to solve these problem:**

1. initailize two pointers (set the original position)

2. move i first ( Same direction usually use for loop)

3. Make it valid(by move j) and update result (or update result each time move j)



### Pattern1: find the longest or mininum area

#### [3. Longest Substring Without Repeating Characters](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int[] count = new int[256];
        int maxLen = 0;
        
        for (int i = 0, j = 0; i < s.length(); i++) {
            count[s.charAt(i)]++;
            while (count[s.charAt(i)] > 1) {
                count[s.charAt(j++)]--;
            }
            maxLen = Math.max(maxLen, i - j + 1);
        }
        return maxLen;
    }
}
```

#### [424. Longest Repeating Character Replacement](https://leetcode-cn.com/problems/longest-repeating-character-replacement/)

```java
//自己写了一个能记得住的
public class Solution {
    public int characterReplacement(String s, int k) {
        int[] count = new int[26];
        int maxLen = 0;
        for (int i = 0, j = 0; i < s.length(); i++) { //移动左边指针
            count[s.charAt(i) - 'A']++;  //增加 count
            while (!valid(count, s, i, j, k)) {  // 保证 sliding window 里的是 vaild
                count[s.charAt(j++) - 'A']--;
            }
            maxLen = Math.max(maxLen, i - j + 1); // 用这个 vaild 的长度更新 maxlen
        }
        return maxLen;
    }
 
    private boolean valid(int[] count, String s, int i, int j, int k) {
        int maxCount = 0;
        for (int index = 0; index < count.length; index++) {
            maxCount = Math.max(maxCount, count[index]);
        }
        return i - j + 1 <= maxCount + k;
    }
}

```

巧妙使用 maxCount，maxLen 里的 i- j 不带表范围 只代表长度！

```java
public class Solution {
    public int characterReplacement(String s, int k) {
        int[] count = new int[26];
        int maxCount = 0;
        int maxLen = 0;
        for (int i = 0, j = 0; i < s.length(); i++) { //移动左边指针
            count[s.charAt(i) - 'A']++;  //增加 count
            maxCount = Math.max(maxCount, count[s.charAt(i) - 'A']); //一直都是保持最大值 即使他代表的那个最大值已经减小了
            if (i - j + 1 > maxCount + k) { // 只要 maxCount 没有更新那么 i++ j就要++
                count[s.charAt(j++) - 'A']--; 
            }
            maxLen = Math.max(maxLen, i - j + 1); // not update  untill the maxCount is updated
        }
        return maxLen;
    }
}
```

#### Use total for extra counter

#### [340. Longest Substring with At Most K Distinct Characters](https://leetcode-cn.com/problems/longest-substring-with-at-most-k-distinct-characters/)

```java
class Solution {
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        int[] count = new int[256];
        int total = 0;
        int maxLen = 0;
        for (int i = 0, j = 0; i < s.length(); i++) {
            if (count[s.charAt(i)]++ == 0) total++;
            while (total > k) {
                if (--count[s.charAt(j)] == 0) total--;
                j++;
            }
            maxLen = Math.max(maxLen, i - j + 1);
        }
        return maxLen;
    }
}
```

#### [76. Minimum Window Substring](https://leetcode-cn.com/problems/minimum-window-substring/)

```java
class Solution {
    public String minWindow(String s, String t) {
        //corner case
        if (t.length() > s.length()) return "";
        int[] count = new int[256];
        for (int i = 0; i < t.length(); i++) {
            count[t.charAt(i)]++;
        }
        int minLen = Integer.MAX_VALUE;
        int total = 0;
        int start = 0;
        for (int i = 0, j = 0; i < s.length(); i++) { 
            if (count[s.charAt(i)]-- > 0) total++;  //注意要判断-之后的 count 是要把--符号写在前面的！  
            while (total == t.length()) {
                if (i - j + 1 < minLen) {start = j; minLen = i - j + 1;}
                if (++count[s.charAt(j)] > 0) total--;  
                j++;
            }
        }
        return minLen == Integer.MAX_VALUE ? "" : s.substring(start, start + minLen);
    }
}
```

#### [438. Find All Anagrams in a String](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> res = new ArrayList<Integer>();
        if (s.length() < p.length()) return res;
        
        int[] count = new int[26];
        int total = p.length();
        for (int i = 0; i < p.length(); i++) {
            count[p.charAt(i) - 'a']++;
        }

        for (int i = 0; i < p.length(); i++) {
            if (count[s.charAt(i) - 'a']-- > 0) total--;
        }
        if (total == 0) res.add(0);

        int end = p.length(), start = 0;
        while(end < s.length()) {  
            if (count[s.charAt(start++) - 'a']++ >= 0) total++;
            if (count[s.charAt(end++) - 'a']-- > 0) total--;
            if (total == 0) res.add(start);
        }
        return res;
    }  
}
```



### Pattern2: Get the number of subarray with the restriction --- at most k or less than k

> res += right - left

#### [713. Subarray Product Less Than K](https://leetcode-cn.com/problems/subarray-product-less-than-k/)

```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        int res = 0;
        int prod = 1;
        for (int i = 0, j = 0; i < nums.length; i++) {
            prod *= nums[i];
            while (j <= i && prod >= k) { //prod 可能永远都满足不了 所以 j 会超过 i 注意排除这种情况
                prod /= nums[j];
                j++;
            }
            res += i - j + 1;
        }
        return res;
    }
}
```



#### [992. Subarrays with K Different Integers](https://leetcode-cn.com/problems/subarrays-with-k-different-integers/)

> Can you imagine this? Well, exactly k is (at most k - at most k -1)

```java
class Solution {
    public int subarraysWithKDistinct(int[] nums, int k) {
        return subarraysWithMaxKDistinct(nums,k) - subarraysWithMaxKDistinct(nums, k - 1);
    }

    private int subarraysWithMaxKDistinct(int[] nums, int k) {
        int[] count = new int[nums.length + 1];
        int total = 0;
        int res = 0;
        for (int i = 0, j = 0; i < nums.length; i++) {
            if (count[nums[i]]++ == 0) total++;
            while (total > k) {
                if (--count[nums[j]] == 0) total--;
                j++;
            }
            res += i - j + 1;
        }
        return res;
    }
}
```



### Pattern3: Two sum

> for 多用 hash 优化， 有序用 pointers 继续优化

#### [18. 4Sum](https://leetcode-cn.com/problems/4sum/)

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length < 4) return res;
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 3; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;  //
            for (int j = i+1; j < nums.length - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;   // i + 1
                int p = j + 1, q = nums.length - 1;
                while (p < q) {
                    if (nums[i] + nums[j] + nums[p] + nums[q] == target) {
                        res.add(Arrays.asList(nums[i],nums[j],nums[p],nums[q]));
                        while(p < q && nums[p + 1] == nums[p]) p++;  // p < q
                        p++;
                        while(p < q && nums[q - 1] == nums[q]) q--;   //
                        q--;
                    } else if ( nums[i] + nums[j] + nums[p] + nums[q] < target) {
                        p++;
                    } else {
                        q--;
                    }
                }

            }
        }
        return res;
    }
}
```



#### [454. 4Sum II](https://leetcode-cn.com/problems/4sum-ii/)

```java
class Solution {
    // two  hashmaps
    public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
        Map<Integer, Integer> map = new HashMap<>();
        //Map<Integer, Integer> map = new HashMap<>();
        int res = 0;
        for(int i = 0;i<A.length;i++){
            for(int j= 0;j<B.length;j++){
                int sumAB = A[i]+B[j];
                if(map.containsKey(sumAB)) map.put(sumAB,map.get(sumAB)+1);
                else map.put(sumAB,1);
            }
        }

        for(int i = 0;i<C.length;i++){
            for(int j = 0;j<D.length;j++){
                int sumCD = -(C[i]+D[j]);
                if(map.containsKey(sumCD)) res += map.get(sumCD);
            }
        }
        return res;
    }
}
```



### Pattern4: Sort Colors

#### [75. Sort Colors](https://leetcode-cn.com/problems/sort-colors/)

```java
class Solution {
    public void sortColors(int[] nums) {
        int i = 0, index = 0;  // 其实是三个指针 还需要一个 index 进行遍历
        int j = nums.length - 1;
        while(index <= j) {
            if (nums[index] == 2) {
                swap(nums, index, j--);
            } else if (nums[index] == 0) {
                swap(nums, index++, i++);
            } else index++;
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

