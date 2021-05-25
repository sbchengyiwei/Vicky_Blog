## Contents

  * + + 

## 1 Principle

### 1.1 What is Monotone Stack？

Stack we all know that it has the characteristic of First-in-Last-out, and Monotonic Stack is that the elements in the stack maintain Monotonic (monotonic). 

- Every time a new element is added to the stack, the elements in the stack remain Monotonic (it can be monotonically increasing or monotonically decreasing). 

- **Advantage**: Time complexity is linear, since each elements only be put in one time, nce out of the stack, it will not be put in again. 

- **Use case:** The monotonic stack is not very versatile, and only deals with a typical problem called **Next Greater Element**. This article uses an algorithm template that explains monotonic queues to solve this type of problem.

   - Next Greater Element problems are like how you find the next higher people after you (↓) [picture from https://haogroot.com/2020/09/01/monotonic-stack-leetcode/#more-108]

     ![img](https://haogroot.com/wp-content/uploads/2020/08/AED2DDB5-15FD-4440-9430-FDC1A9439E86-1024x628.jpeg)

     * Either the height or the seat number can be push into Monotonic Stack.



