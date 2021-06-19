### How to solve the Interval Problems?

1. **Sorting**: A common way to sort is to sort by the beginning of the interval and pay attention to whether you need to **sort the end points in descending order** when the starting points are the same. The goal is to prevent the following：

![Image](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/images/Screen%20Shot%202021-06-20%20at%201.16.17%20AM.png)

2. **Draw Pictures**: How many different relative positions are there between these two sections, how should our code deal with these different relative positions?

![Image](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/images/Screen%20Shot%202021-06-20%20at%201.16.40%20AM.png)

​	After sorting, two adjacent intervals may have the following three relative positions:

![Image](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/images/Screen%20Shot%202021-06-20%20at%201.16.46%20AM.png)

3. **Sweep Line**: for loop



### Pattern1: Meeting Room

#### [253. Meeting Rooms II](https://leetcode-cn.com/problems/meeting-rooms-ii/)

```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if(intervals == null || intervals.length == 0) return 0;
        int[] starts = new int[intervals.length];
        int[] ends = new int[intervals.length];
        for (int i = 0; i < intervals.length; i++) {
            starts[i] = intervals[i][0];
            ends[i] = intervals[i][1];
        }
        Arrays.sort(starts);
        Arrays.sort(ends);
        int end = 0;
        int res = 1;
        for (int i = 1; i < intervals.length; i++) {
            if (starts[i] < ends[end]) {
                res++;
            } else end++;
        }
        return res;
    }
}
//PQ Way
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if(intervals == null || intervals.length == 0) return 0;
        PriorityQueue<Integer> queue = new PriorityQueue<>();
        Arrays.sort(intervals, (a,b)->(a[0] - b[0]));  // Pay attention to order
        queue.offer(intervals[0][1]);
        int res = 1;
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < queue.peek()) {
                queue.offer(intervals[i][1]);
                res++;
            } else {
                queue.poll();
                queue.offer(intervals[i][1]);
            }
        }
        return res;
    }
}
```



### Pattern2: Intervals merge and intersection

#### [1288. Remove Covered Intervals](https://leetcode-cn.com/problems/remove-covered-intervals/)

```java
class Solution {
    public int removeCoveredIntervals(int[][] intervals) {
        Arrays.sort(intervals, (a, b)->(a[0] == b[0] ? b[1] - a[1] : a[0] - b[0]));//Note that the ends should be arranged in descending order otherwise repeated intervals as shown in the opening diagram are treated as intersecting errors
        int end = intervals[0][1];
        int cover = 0;
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < end) {
                if (intervals[i][1] > end) {end = intervals[i][1];}
                else {cover++;}
            } else {
                end = intervals[i][1];
            }
        }
        return intervals.length - cover;
    }
}
```

#### [56. Merge Intervals](https://leetcode-cn.com/problems/merge-intervals/)

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        Arrays.sort(intervals, (a, b)->(a[0] - b[0]));
        List<int[]> res = new ArrayList<int[]>();
        res.add(intervals[0]);
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] <= res.get(res.size() - 1)[1]) {
                if (intervals[i][1] > res.get(res.size() - 1)[1]) {
                    res.get(res.size() - 1)[1] = intervals[i][1];
                }
            } else {
                res.add(intervals[i]);
            }
        }
        return res.toArray(new int[][]{});
    }
}
```

#### [986. Interval List Intersections](https://leetcode-cn.com/problems/interval-list-intersections/)

```java
class Solution {
    public int[][] intervalIntersection(int[][] firstList, int[][] secondList) {
        List<int[]> res = new ArrayList<int[]>();
        int i = 0, j = 0;
        while (i < firstList.length && j < secondList.length) {
            int a1 =  firstList[i][0], a2 = firstList[i][1];
            int b1 = secondList[j][0], b2 = secondList[j][1];
            if (a2 >= b1 && a1 <= b2) {
                res.add(new int[]{Math.max(a1, b1), Math.min(a2, b2)});
            }
            if (a2 < b2) {i++;}
            else j++;
        }
        return res.toArray(new int[][]{});
    }
}