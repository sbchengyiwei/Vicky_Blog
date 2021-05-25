## Contents



## 1 What is Monotone Stack？

Stack we all know that it has the characteristic of First-in-Last-out, and Monotonic Stack is that the elements in the stack maintain Monotonic (monotonic). 

- Every time a new element is added to the stack, the elements in the stack remain Monotonic (it can be monotonically increasing or monotonically decreasing). 

- **Advantages**: Time complexity is linear, since each elements only be put in one time, once out of the stack, it will not be put in again. 

- **Use cases:** 

  - Monotonic stack often be used to deal with a typical problem called **Next Greater Element**.
  - How to **get the greatest(smallest) array(string)** by the given conditions (remaining size k/ no duplicate).
  - **Farest Greater Element** (leetcode 1124). 

  

  ​	 *PS: Next Greater Element problems are like how you find the next higher people after you (↓). Either the      height or the seat number can be push into Monotonic Stack.* *(Picture from https://haogroot.com/2020/09/01/monotonic-stack-leetcode/#more-108)*		![img](https://haogroot.com/wp-content/uploads/2020/08/AED2DDB5-15FD-4440-9430-FDC1A9439E86-1024x628.jpeg)

  

## 2 Monotone Stack Template

### 2.1 Next Greater Element

Q: Given a array nums[], find the next element of each number in this array.

```java
public int[] nextGreaterElement(int[] nums) {
    Stack<Integer> stack = new Stack<>();
    int[] res = new int[nums.length];
    for (int i = 0; i < nums.length; i++){
      // Maintain a monotone stack by only adding a element when it is smaller than the top elements of stack.
        if(stack.isEmpty() || nums[stack.peek()] > nums[i]){
            stack.push(i);
        }
        else{
          	//If find a greater element, calculate and pop the smaller numbers in the stack one by one:)
            while (!stack.isEmpty() && nums[stack.peek()] < nums[i]){
                res[stack.peek()] = i;
                stack.pop();
            }
            stack.push(i); // still monotonic
        }
    }
    return res;
}
```


### 2.2 Smallest String of size k

Q: Given a array nums[], cut the size to k, find the Smallest String remaining.

```java
public String getMinString(String num, int k) {
    Stack<Character> stack = new Stack<>();
    for (int i = 0; i < num.length(); i++) {
       // if there are enough elements left to delete and the next element is smaller, pop the larger one. (345 <-3   343 is smaller than 345)
        while (stack.size() + num.length() - i  > k  && !stack.empty() && stack.peek() > num.charAt(i)) 
            stack.pop();
       // only add when the size if smaller than k (1234567 size 4, only add the first 4 elements)
        //Or if the size is smaller than k, elements are add even we don't pop the larger elements. (9876543 size 4, 6543 all add to the stack)
        if (stack.size() < k) 
            stack.push(num.charAt(i));  
    }
  	// give the result to a string
    StringBuilder sb = new StringBuilder();
    while (!stack.empty()) {
        sb.append(stack.pop()); 
    }
    StringBuilder res = sb.reverse();
    //corner case! 10200
    while (res.length() > 0 && res.charAt(0) == '0') res.deleteCharAt(0);
    return res.length() == 0 ? "0" : res.toString();
}
```



### 2.3 Farest Greater Element

Q: Given a array nums[], find the Farest Distance between a number and its Greater Element in this array.

```java
public int farestGreaterElement(int[] hours) {
    //Monotonic stack : only push smaller number
    Stack<Integer> stack = new Stack<>();
    for(int i=0;i<len;i++){
        if(stack.isEmpty() || tired[i] < tired[stack.peek()]){
            stack.push(i);
        }
    }
    //Find the longest upward slope(connect two points, if the slope is positive, then its a upward slope)
    int res = 0;
    for(int i=len;i>0;i--){
        while(!stack.isEmpty() && tired[i] > tired[stack.peek()]){
            res = Math.max(res, i - stack.pop());
        }
    }
    return res;
}
```



## 3 Uses of the templates

###### Easy

### 3.1 Next Greater Element

- [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)

  ```java
  class Solution {
      //time : O(n) space : O(n)
      //To find the least number of days we need to wait, just use stack to save temperatures every day, if there is a warmer day, calculate all the possible results before it.
      public int[] dailyTemperatures(int[] T) {
          Stack<Integer> stack = new Stack<>();
          int[] res = new int[T.length];
        
          for (int i = 0; i < T.length; i++){
              if(stack.isEmpty() || T[stack.peek()] > T[i]){
                  stack.push(i);
              }
              else{
                  while (!stack.isEmpty() && T[stack.peek()] < T[i]){
                      // if temperature between warmer, calculate all the result before it in the stack.
                      res[stack.peek()] = i - stack.peek();
                      stack.pop();
                  }
                  stack.push(i);
              }
          }
          return res;
      }
  }
  /*
  
  |  |
  ||||
  ||||  result is 3 2 1 0  
  */
  ```

- [496. Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/)

  ```java
  class Solution {
     //Time : O(N + M)   Space: O(N)
      public int[] nextGreaterElement(int[] nums1, int[] nums2) {
          int len1 = nums1.length;
          int len2 = nums2.length;
  
          Stack<Integer> stack = new Stack<>();
          Map<Integer, Integer> map = new HashMap<>();
      
          //Go through the nums2, and store the correspondence in a hash table
          for (int i = 0; i < len2; i++) {
              //Monotonic stack : if there is a increment, pop elements in the stack and create the correspondences.
              while (!stack.isEmpty() && stack.peek() < nums2[i]) {
                  map.put(stack.pop(), nums2[i]);
              }
              stack.push(nums2[i]);
          }
  
          // Go through the nums1 to get the result.
          int[] res = new int[len1];
          for (int i = 0; i < len1; i++) {
              res[i] = map.getOrDefault(nums1[i], -1);
          }
          return res;
      }
  }
  ```

###### Middle

- [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

  ```java
  // Cannikin Law : the lowest block quantify the capacity of the bucket.
  class Solution {
      //Time : O(n) Space : O(n)
      public int trap(int[] height) {
          //use stack store the index
          Stack<Integer> stack = new Stack<>();
          int res = 0;
        
          for (int i = 0; i < height.length; i++) {
              //Once the length on the right is higher than the left, it means that water can be stored
              while (!stack.isEmpty() && height[i] > height[stack.peek()]) {
                  int bottom = stack.pop();//pop out the height of the bottom of the bucket
                  if (stack.isEmpty()) break; // no left block to save water...
                  int dis = i - stack.peek() - 1; // bottom diameter
                  //height[stack.peek()] is the left side height, minimal bucket height determine how much wate can be trapped
                  int h = Math.min(height[i], height[stack.peek()]) - height[bottom]; 
                  res += h * dis;
              } 
              stack.push(i);
          }
          return res;
      }
  }
  ```

  

![img](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)

- [84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)

  ```java
  class Solution {
      //Time : O(n) Space : O(n)
      public int largestRectangleArea(int[] heights) {
          int res = 0;
          //use stack store the index
          Stack<Integer> stack = new Stack<>();
          for (int i = 0; i <= heights.length; i++) {
              int hi = i == heights.length ? 0 : heights[i];
              // Once the length on the right is lower than the left, we calculate the maximal rectangle before i index
              while (!stack.isEmpty() && hi < heights[stack.peek()]) {
                  int high = heights[stack.pop()]; // high is the height of the rectangle
                  int area = high * (stack.isEmpty() ? i : i - stack.peek() - 1);
                  res = Math.max(res, area);
              }
              stack.push(i);
          }
          return res; 
      }
  }
  ```

  

  ![img](https://assets.leetcode.com/uploads/2021/01/04/histogram.jpg)

- [85. Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)

  ```java
  class Solution {
      //Time : O(n^2) Space : O(n)
      public int maximalRectangle(char[][] matrix) {
          if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return 0;
          int[] heights = new int[matrix[0].length + 1];
          int res = 0;
          for (int i = 0; i < matrix.length; i++) {
              //use stack store the index
              Stack<Integer> stack = new Stack<>();
              // Convert a two-dimensional array to a one-dimensional
              for (int j = 0; j < heights.length; j++) {
                  if (j != matrix[0].length && matrix[i][j] == '1') {
                      heights[j] += 1;
                  } else heights[j] = 0; // reset the height to 0
                  // Once the length on the right is lower than the left, we calculate the maximal rectangle before j index
                  while (!stack.isEmpty() && heights[j] < heights[stack.peek()]) {
                      int high = heights[stack.pop()]; // high is the height of the rectangle
                      int area = high * (stack.isEmpty() ? j : j - stack.peek() - 1);  
                      res = Math.max(res, area);
                  }
                  stack.push(j);
              }
          }
          return res;
      }
  }
  ```

  

  ![img](https://assets.leetcode.com/uploads/2020/09/14/maximal.jpg)



### 3.2 Smallest String of size k

###### Middle

- [402. Remove K Digits](https://leetcode.com/problems/remove-k-digits/)

  ```java
  class Solution {
      //time : O(n)  space : O(n) 
      //Same as leetcode 321
      public String removeKdigits(String num, int k) {
          int len = num.length() - k;
          Stack<Character> stack = new Stack<>();
        
          for (int i = 0; i < num.length(); i++) {
              //stack.size() + num.length() - i  > len (leetcode 321)
              while (i - stack.size() < k && !stack.empty() && stack.peek() > num.charAt(i)) 
                  stack.pop();
              if (stack.size() < len) 
                  stack.push(num.charAt(i));  
          }
        
          StringBuilder sb = new StringBuilder();
          while (!stack.empty()) {
              sb.append(stack.pop()); 
          }
          StringBuilder res = sb.reverse();
          //corner case!
          while (res.length() > 0 && res.charAt(0) == '0') res.deleteCharAt(0);
          return res.length() == 0 ? "0" : res.toString();
      }
  }
  /*
  Input: num = "1432219", k = 3
  Output: "1219"
  */
  ```



###### Hard

- [321. Create Maximum Number](https://leetcode-cn.com/problems/create-maximum-number/)

  ```java
  class Solution {
      //time ：O(k(m+n+k^2))  space : O(k) 
      public int[] maxNumber(int[] nums1, int[] nums2, int k) {
          int n = nums1.length;
          int m = nums2.length;
          int[] ans = new int[k];
          for (int i = Math.max(0, k - m); i <= k && i <= n; ++i){
            //find the max array of each nums[] with the given len (i or k - i), then merge them
              int[] candidate = merge(maxArray(nums1, i), maxArray(nums2, k - i), k);
            // update the res, if tmp is larger than res
              if (greater(candidate, 0, ans, 0))
                  ans = candidate;
          }
          return ans;
      }
    
    	//1. Get the largest array of size k
      private int[] maxArray(int[] nums, int len) {
          Stack<Integer> stack = new Stack<Integer>();
          for (int i = 0; i < nums.length; i++) {
             // Pop the smaller numbers in the stack is the elements is enough
              while (stack.size() + nums.length - i > len && !stack.empty() && stack.peek() < nums[i]) 
                  stack.pop();
              if (stack.size() < len) 
                  stack.push(nums[i]);  
          }
          int[] result = new int[len];
          for (int i = len - 1; i >= 0; i--) {
              result[i] = stack.pop(); 
          }
          return result;
      }
    
      //2. Merge and also get the largest one.
      private int[] merge(int[] nums1, int[] nums2, int k) {
          int[] ans = new int[k];
          for (int i = 0, j = 0, r = 0; r < k; ++r)
              ans[r] = greater(nums1, i, nums2, j) ? nums1[i++] : nums2[j++]; 
          return ans;
      }
  
    
      //3. Compare two array by lexicographic order
      private boolean greater(int[] A, int index1, int[] B, int index2){
  
          // if two numbers is equal, compare the next
          while(index1 < A.length && index2 < B.length && A[index1] == B[index2]){
              index1++;
              index2++;
          }
          return index2 == B.length || (index1 < A.length && A[index1] > B[index2]);
      }
  }
  
  ```

  - [a good explain](https://www.bilibili.com/video/BV1yK4y1K7eC)



### 3.3 Farest Greater Element

- [1124. Longest Well-Performing Interval](https://leetcode.com/problems/longest-well-performing-interval/)

  ```java
  class Solution {
      public int longestWPI(int[] hours) {
          //1.Change array to PreSum array
          int len = hours.length;
          int[] tired = new int[len+1];
          tired[0] = 0;
          for(int i = 0; i < len; i++){
              tired[i+1] = tired[i] + (hours[i] > 8 ? 1 : -1); // if larger than 8 + 1, else - 1
          }
          //2.Monotonic stack : only push smaller number
          Stack<Integer> stack = new Stack<>();
          for(int i=0;i<len;i++){
              if(stack.isEmpty() || tired[i] < tired[stack.peek()]){
                  stack.push(i);
              }
          }
          //3.Find the longest upward slope(connect two points, if the slope is positive, then its a upward slope)
          int res = 0;
          for(int i=len;i>0;i--){
              while(!stack.isEmpty() && tired[i] > tired[stack.peek()]){
                  res = Math.max(res, i - stack.pop());
              }
          }
          return res;
      }
  }
  ```

  *This progress can be illuminated as the following picture:*

  ![image](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/images/Screen%20Shot%202021-05-25%20at%208.47.42%20PM.png)

  Since it's maybe hard to convert it to monotone stack problem, we can also use HashMap to achieve the progress upwards:

  ```java
  class Solution {
      public int longestWPI(int[] hours) {
          HashMap<Integer, Integer> map = new HashMap();
          int sum = 0;
          int res = 0;
          for (int i = 0; i < hours.length; i++) {
              sum += hours[i] > 8 ? 1 : -1;
            	//Two Situations: > 0 | <=0
  						//> 0 
              if (sum > 0) res = i + 1;
              else {
                  map.putIfAbsent(sum, i); // Only add the first(make the period longest)
                  // <= 0 
                  if (map.containsKey(sum - 1)) res = Math.max(res, i - map.get(sum - 1));
              }
          }
          return res;
      }
  }
  
  ```

  - [a good explain](https://www.bilibili.com/video/BV1Wt411G7vN?from=search&seid=8416005481709189881)

  

### 3.4 Combine with remove duplicate

- [316. Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters/)

  ```java
  class Solution {
      public String removeDuplicateLetters(String s) {
           //time : O(n)  space : O(n) / O(1) : since there only 256 characters
          
        	Stack<Character> stack = new Stack<>();
          //indicates the whether characters already in stack
          boolean[] inStack = new boolean[256];
        
          // indicates the times that each character in string appears
          int[] count = new int[256];
          for (int i = 0; i < s.length(); i++) {
              count[s.charAt(i)]++;
          }
  
          for (char c : s.toCharArray()) {
              // Each time a character is traversed, the corresponding count is decremented by one
              count[c]--;
              if (inStack[c]) continue; // already in stack, don't push(pop is only executed by the following smaller characters) (acdc acbc)
              while (!stack.isEmpty() && stk.peek() > c) {
                  // If there are same characters afterwards, pop can be executed
                  if (count[stack.peek()] != 0) {
                     inStack[stack.pop()] = false;
                  } else break; // can not pop, still on the top of stack --> infinite loop
              }
              stack.push(c);
              inStack[c] = true;
          }
          
          StringBuilder sb = new StringBuilder();
          while (!stack.empty()) {
              sb.append(stack.pop());
          }
          return sb.reverse().toString();
      }
  }
  ```

  - [a good explain](https://leetcode-cn.com/problems/remove-duplicate-letters/solution/you-qian-ru-shen-dan-diao-zhan-si-lu-qu-chu-zhong-/)

  