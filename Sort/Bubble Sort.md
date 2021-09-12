## Bubble Sort

### 1.[Sort Colors ](https://leetcode-cn.com/problems/sort-colors/)(Two Pointers)

```java
class Solution {
    public void sortColors(int[] nums) {
        int i = 0, index = 0;
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

### 2.[Sort Colors II](https://www.lintcode.com/problem/143/)

```java
public class Solution {
    /**
     * @param colors: A list of integer
     * @param k: An integer
     * @return: nothing
     */
    public void sortColors2(int[] colors, int k) {
        // write your code here
        sort(colors, 1, k, 0, colors.length - 1);
    }
    private void sort(int[] colors, int from, int to, int l, int r) {
        if (from == to || l == r) return;
        int mid = from + (to - from) / 2;
        int left = l;
        int right = r;
        while (left <= right) {
            while (left <= right && colors[left] <= mid) {
                left++;
            }
            while (left <= right && colors[right] > mid) {
                right--;
            }
            if (left <= right) {
                swap(colors, left, right);
                left++;
                right--;
            }
        }
        sort(colors, from, mid, l, left-1);
        sort(colors, mid+1, to, right + 1, r);

    }

    private void swap (int[] colors, int i, int j) {
        int temp = colors[j];
        colors[j] = colors[i];
        colors[i] = temp;
    }
}
```

### 3.Sort 4 Colors (google)

```java
public class Solution {
  // 本质和三色排序一样是冒泡排序
    public void sort4Colors(int[] colors) {
    
        // 1111222222222333333334444444
        //     |        |       |
        //   bar[1]   bar[2] bar[3]
        
        int[] bar = new int[4];  //bar的表示如上面所示
        int pointer = 0; //从零开始遍历的一个指针
        while (pointer < colors.length) {
            if (colors[pointer] == 1) {  //1 代表排在第一个的数
                swap(colors, bar[3], pointer);
                swap(colors, bar[2], bar[3]);
                swap(colors, bar[1], bar[2]);
                pointer++;
                bar[3]++;
                bar[2]++;
                bar[1]++;

            } else if(colors[pointer] == 2) { //2 代表排在第2个的数
                swap(colors, bar[3], pointer);
                swap(colors, bar[2], bar[3]);
                pointer++;
                bar[3]++;
                bar[2]++;

            } else if(colors[pointer] == 3) {  //3 代表排在第3个的数
                swap(colors, bar[3], pointer);
                pointer++;
                bar[3]++;

            } else {      //4 代表排在第4个的数
                pointer++;
            }
        }
    }
    private void swap (int[] colors, int i, int j) {
        int temp = colors[j];
        colors[j] = colors[i];
        colors[i] = temp;
    }
}
```

