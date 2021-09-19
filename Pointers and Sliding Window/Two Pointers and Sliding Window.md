### BASIC

> Two pointers make up a sliding window

Two moving ways:  Same direction and opposite direction

Why two pointers: save time(same as hash and stack)

**How to solve these problem:**

1. initailize two pointers (set the original position)
2. move i first ( Same direction usually use for loop)
3. Make it valid(by move j) and update result

###  Template 1:  求longest or shortest子串的长度

```java
//1.longest- 第一个合法子区间
int j = 0;
for (int i = 0; i < n; i++) {
	while (不满足条件) {
		j += 1;
  }
	res = Math.max()
  
}
//2.shortest - 最后一个合法子区间
int j = 0;
for (int i = 0; i < n; i++) {
	while (满足条件) {
  	res = Math.min()
		j += 1;
  }
}
```



### Template 2: 求至少/最多包含 k 个不同字符的子串个数

```java
//1. at least k distinct characters
int res = 0;
int left = 0;
for (int i = 0; i < n; i++) {
	while (state is vaild) { 
		left += 1;
  } // 循环到不满足搭配为止
	res += left;  //left 前面都是满足的子数组
}

//2.at most k distinct characters
int res = 0;
int left = 0;
for (int i = 0; i < n; i++) {
	while (state is not vaild) { 
		left += 1;
  } // 循环到满足搭配为止
	res += i - left + 1; //从 left 到 i 都是满足的子数组
}

```



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
                count[s.charAt(j)]--;
              	j++;
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
                count[s.charAt(j) - 'A']--;
              	j++;
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

Transform:

#### [5862. Minimum Number of Operations to Make Array Continuous](https://leetcode-cn.com/problems/minimum-number-of-operations-to-make-array-continuous/)

You are given an integer array nums. In one operation, you can replace any element in nums with any integer.

nums is considered continuous if both of the following conditions are fulfilled:

All elements in nums are unique.
The difference between the maximum element and the minimum element in nums equals nums.length - 1.
For example, nums = [4, 2, 5, 3] is continuous, but nums = [1, 2, 3, 5, 6] is not continuous.

Return the minimum number of operations to make nums continuous.

```java
class Solution {
/*
1. erase the repeated numbers
2. sort the new array
3. find the equation

 1  2 3  5  9
    |    |
    L    R   (: the index of the new array without repeated number)

(R-L + 1) : real amount of elements inside the interval
a[R] - a[L] + 1  :  the expected amount 
n - (R-L + 1): amount of elements outsides the interval 

inequation: a[R] - a[L] + 1  <= (R-L + 1) + n - (R-L + 1) = n
a[R] - a[L] <= n-1 

4. sliding window question : find the maximum window fullfill the length smaller or equal to the n - 1
*/

    public int minOperations(int[] nums) {
        //去重
        int n = nums.length;
        Set<Integer> us = new HashSet<>();
        for (int x : nums)
            us.add(x);
        
        int an = us.size();
        int [] a = new int [an];
        int i = 0;
        for (int x : us)
            a[i++] = x;
        //排序
        Arrays.sort(a);

        //滑动窗口
        int len = 0;
        for (int r = 0, l = 0; r < an; r ++){
            while (l < an && a[r] - a[l] > n - 1)
                l++;
            len = Math.max(len, r - l + 1);
        }

        return n - len;
    }
}
```



#### Use total for extra counter

#### [340. Longest Substring with At Most K Distinct Characters](https://leetcode-cn.com/problems/longest-substring-with-at-most-k-distinct-characters/)

```java
class Solution {
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        int[] count = new int[128];
        int total = 0;
        int maxLen = 0;
        for (int i = 0, j = 0; i < s.length(); i++) {
            count[s.charAt(i)]++;
            if (count[s.charAt(i)] - 1 == 0) total++; //total++的条件：第一次增加到窗口
            while (total > k) {
                count[s.charAt(j)]--;
                if (count[s.charAt(j)] == 0) total--;//total变化的条件：最后一个也出了窗口
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
               	count[s.charAt(j)]++;
                if (count[s.charAt(j)] > 0) total--;  
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



### Pattern2: Get the number of subarray with the restriction --- at most k or at least k 

> At most : res += right - left
>
> At least : res += left

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
            }//满足后退出
            res += i - j + 1;
        }
        return res;
    }
}
```

#### [1375 · Substring With At Least K Distinct Characters](https://www.lintcode.com/problem/1375/)

> Given a string `S` with only lowercase characters.
>
> Return the number of substrings that contains at least `k` distinct characters.
>
> **Example 1:**
>
> ```
> Input: S = "abcabcabca", k = 4
> Output: 0
> Explanation: There are only three distinct characters in the string.
> ```

```java
public class Solution {
    public long kDistinctCharacters(String s, int k) {
        // Write your code here
        if (s == null || s.length() == 0 || s.length() < k) return 0;
        int total = 0;
        int[] count = new int[128];
        long res = 0;
        for (int i = 0, j = 0; i < s.length(); i++) {
            if (count[s.charAt(i)]++ == 0) total++;
            while (total >= k) {
                if (--count[s.charAt(j)] == 0) total--;
                j++;
            }//一直到不满足退出
            res += j * 1L;
        }
        return res;
    }
}
```

#### [Number of substrings with count of each character as k](https://www.geeksforgeeks.org/number-substrings-count-character-k/)

Given a string and an integer k, find the number of substrings in which all the different characters occur exactly k times. 

**Examples:** 

```
Input : s = "aabbcc"
        k = 2 
Output : 6
The substrings are aa, bb, cc,
aabb, bbcc and aabbcc.

Input : s = "aabccc"
        k = 2
Output : 3
There are three substrings aa, 
cc and cc
```

```java

	static int count_substrings(String s, int k)
	{
		int count = 0;
		int distinct = 0;
		boolean[] have = new boolean[26];
		Arrays.fill(have, false);
		for (int i = 0; i < s.length(); i++) {
			have[((int)(s.charAt(i) - 'a'))] = true;
		}
		for (int i = 0; i < 26; i++) {
			if (have[i]) {
				distinct++;
			}
		}
    
    //fixed window lens(from 1*k to distinct*k)
		for (int length = 1; length <= distinct; length++) {
			int window_length = length * k;
			int[] freq = new int[26];
			Arrays.fill(freq, 0);
			int window_start = 0;
			int window_end
				= window_start + window_length - 1;
			for (int i = window_start;
				i <= Math.min(window_end, s.length() - 1);
				i++) {
				freq[((int)(s.charAt(i) - 'a'))]++;
			}
			while (window_end < s.length()) {
				if (have_same_frequency(freq, k)) {
					count++;
				}
				freq[(
					(int)(s.charAt(window_start) - 'a'))]--;
				window_start++;
				window_end++;
				if (window_end < s.length()) {
					freq[((int)(s.charAt(window_end)
								- 'a'))]++;
				}
			}
		}
		return count;
	}

	static boolean have_same_frequency(int[] freq, int k)
	{
		for (int i = 0; i < 26; i++) {
			if (freq[i] != 0 && freq[i] != k) {
				return false;
			}
		}
		return true;
	}

```





### Pattern3: Two sum(sorted)

> Opposite direction, 根据条件判断移动 i 还是 j
>
> for 多用 hash 优化， 有序用 pointers 继续优化

- LeetCode 167 Two Sum II - Input array is sorted

  ```java
  class Solution {
      public int[] twoSum(int[] numbers, int target) {
          //two pointers:one from left, one from right
          if (numbers == null || numbers.length < 2) {
              return new int[]{-1, -1};
          }
          int left = 0;
          int right = numbers.length -1;
          while(left < right) {
              int sum = numbers[left] + numbers[right];
              if (target == sum) {
                  return new int[]{left + 1, right +1};
              } else if (sum < target) {
                  left++;
              } else {
                  right--;
              }
          }
          return new int[]{-1, -1};
      }
      
  }
  ```

- LeetCode 15 3Sum

  > 1. Two sum `降维` :  `int sum = 0 - nums[i];  if (nums[low] + nums[high] == sum) `
  > 2. 注意：`去重` : ` if (i > 0 && nums[i] == nums[i - 1]) continue;`   `while (low < high && nums[low] == nums[low + 1]) low++; while (low < high && nums[high] == nums[high - 1]) high--;`
  > 3. `↑` while (low < high && nums[low] == nums[low + 1]) 记得判断`low < high`

  ```java
  class Solution {
      public List<List<Integer>> threeSum(int[] nums) {
          List<List<Integer>> res = new ArrayList<>();
          if (nums == null || nums.length < 3) return new ArrayList<>(res);
          Arrays.sort(nums);
          for (int i = 0; i < nums.length - 2; i++) {
              if (i > 0 && nums[i] == nums[i - 1]) continue;
              int low = i + 1;
              int high = nums.length - 1;
              while(low < high) {
                  if (nums[i] + nums[low] + nums[high] == 0) {
                      res.add(Arrays.asList(nums[i], nums[low], nums[high]));
                      while (low < high && nums[low] == nums[low + 1]) low++;
                      while (low < high && nums[high] == nums[high - 1]) high--;
                      low++;
                      high--;
                  } else if (nums[i] + nums[low] + nums[high] < 0) {
                      low++;         
                  } else {  
                      high--; 
                  }
              }
          }
          return res;
      }
  }
  ```

  

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



[1879 · Two Sum VII](https://www.lintcode.com/problem/1879/?_from=collection&fromId=178)

```java
public class Solution {
    /**
     * @param nums: the input array
     * @param target: the target number
     * @return: return the target pair
     */
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

    public int nextleft(int left, int[] nums) {
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



#### [11. Container With Most Water](https://leetcode-cn.com/problems/container-with-most-water/)

```java
class Solution {
    public int maxArea(int[] height) {
        //corner case
       //if (height == null && height.length < 2) return 0;
        int ans = 0;
        int l = 0, r = height.length - 1;
        while(l < r) {
            int area = Math.min(height[l], height[r]) * (r - l);
            ans = Math.max(ans, area);
            if (height[l] <= height[r]) {
                ++l;
            } else {++r;}
        }
        return ans;     
    }
}
```

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)



### Pattern4: Sort Colors

#### [75. Sort Colors](https://leetcode-cn.com/problems/sort-colors/)

> Opposite direction 结合一个遍历指针和一个swap函数

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

