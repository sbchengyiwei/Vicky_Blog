### 如何等概率从 n 个数等概率抽取 k 个数（n 未知 数据为数据流）

[Reference Article](https://www.jianshu.com/p/7a9ea6ece2af)

**算法思路：**

如果接收的数据量小于k，则依次放入蓄水池。

当接收到第i个数据时(i > k-1)，在[0, i]范围内取以随机数d，若d的落在[0, k-1]范围内(d<k)，则用接收到的第i个数据替换蓄水池中的第d个数据。

重复步骤2。

算法的精妙之处在于：当处理完所有的数据时，蓄水池中的每个数据都是以k/N的概率获得的。



**代码如下：**

```java
public class ReserviorSampling {
    public int[] reserviorSampling(int[] stream, int k){
        Random random = new Random();
        int[] res = new int[k];
        for (int i = 0; i < k; i++) {
            res[i] = stream[i];
        }
        for (int i = k; i < stream.length; i++) {
            int rmd = random.nextInt(i + 1);
            if (rmd < k) res[rmd] = stream[i];
        }
        return res;
    }
}

```

#### [384. Shuffle an Array](https://leetcode-cn.com/problems/shuffle-an-array/)

```java
class Solution {
    int[] nums;
    Random random;
    public Solution(int[] nums) {
        this.nums = nums;
        random = new Random();
    }
    
    /** Resets the array to its original configuration and return it. */
    public int[] reset() {
        return nums;
    }
    
    /** Returns a random shuffling of the array. */
    public int[] shuffle() {
        if (nums == null) return nums;
        int[] newNums = nums.clone();
        for (int i = 0; i < nums.length; i++) {
            int rmd = random.nextInt(i + 1); //找到前 m 个中一个位置的概率为 1/m 不被后面替换的概率为 m/m+1 * m+1/m+2 *..* n-1/n 所以每个元素在当前位置的概率都是 1/n
            swap(newNums, rmd, i);
        }
        return newNums;
    }

    private void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```





PS: 以后抽一个也用蓄水池（节省空间复杂度）

```java
int n = 1;  //从第一个数据开始
int res = 0;//第一个数据的 index = 0 放入蓄水池
for (...) {
  n++; //数据流走到的位置
  if(random.nextInt(n) == 0) res = n;
}
```

#### [398. Random Pick Index](https://leetcode-cn.com/problems/random-pick-index/)

```java
class Solution {
    Random random;
    int[] nums;
    public Solution(int[] nums) {
        this.nums = nums;
        random = new Random();
    }
    
    public int pick(int target) {
        int count = 0;
        int res = -1; //target's index 
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) {
                count++;
                if (random.nextInt(count) == 0) {
                    res = i;
                }
            }
        }
        return res;
    }
}

```



