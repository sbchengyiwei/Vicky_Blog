## Contents



## 1 What is Monotone Stack？

Stack we all know that it has the characteristic of First-in-Last-out, and Monotonic Stack is that the elements in the stack maintain Monotonic (monotonic). 

- Every time a new element is added to the stack, the elements in the stack remain Monotonic (it can be monotonically increasing or monotonically decreasing). 

- **Advantages**: Time complexity is linear, since each elements only be put in one time, once out of the stack, it will not be put in again. 

- **Use cases:** 

  - Monotonic stack often be used to deal with a typical problem called **Next Greater Element**.
  - How to **get the greatest(smallest) array(string)** by the given conditions (remaining size k/ no duplicate).
  - **Farest Greater Element** (leetcode 1124). 

  

  *PS: Next Greater Element problems are like how you find the next higher people after you (↓). Either the height or the seat number can be push into Monotonic Stack.* *(Picture from https://haogroot.com/2020/09/01/monotonic-stack-leetcode/#more-108)*

  ![img](https://haogroot.com/wp-content/uploads/2020/08/AED2DDB5-15FD-4440-9430-FDC1A9439E86-1024x628.jpeg)

  

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
        while (stack.size() + num.length() - i  > k  && !stack.empty() && stack.peek() > num.charAt(i))  // if there are enough elements left to delete and the next element is smaller, pop the larger one. (345 <-3   343 is smaller than 345)
            stack.pop();
        if (stack.size() < k) 
          // only add when the size if smaller than k (1234567 size 4, only add the first 4 elements)
          //Or if the size is smaller than k, elements are add even we don't pop the larger elements. (9876543 size 4, 6543 all add to the stack)
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

This progress can be illuminated as the following picture:



