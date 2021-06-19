### How to solve the Interval Problems?

**1. 排序**。常见的排序方法就是按照区间起点排序，或者先按照起点升序排序，若起点相同，则按照终点降序排序。当然，如果你非要按照终点排序，无非对称操作，本质都是一样的。

注意判断是否需要对**终点降序排列**，目的是防止如下情况：

![Image](https://mmbiz.qpic.cn/sz_mmbiz_jpg/gibkIz0MVqdHvEm6Hfybxj5tHaDLbHmMwNQAb9KdpBxJw0mAmLfWLRZNnw84kv11kcaGJEOE2ypn1dqXglI14ZA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**2. 画图**。就是说不要偷懒，勤动手，两个区间的相对位置到底有几种可能，不同的相对位置我们的代码应该怎么去处理。

![Image](https://mmbiz.qpic.cn/sz_mmbiz_jpg/gibkIz0MVqdHvEm6Hfybxj5tHaDLbHmMwOdic80XKAs3ho1hJsby925sEneG24WgN55FJJu4hfrbjZuWRqRiaW4NA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

排序之后，两个相邻区间可能有如下三种相对位置：

![Image](https://mmbiz.qpic.cn/sz_mmbiz_jpg/gibkIz0MVqdHvEm6Hfybxj5tHaDLbHmMwZvyib1gGbacibosib4gCpv1P6VbvBHL4sOxAcJjPiaaPbM2GbzxZYO3A1Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**3. Sweep Line **(for loop)



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
//PQ 解法
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if(intervals == null || intervals.length == 0) return 0;
        PriorityQueue<Integer> queue = new PriorityQueue<>();
        Arrays.sort(intervals, (a,b)->(a[0] - b[0]));  // 注意要排个序
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
        Arrays.sort(intervals, (a, b)->(a[0] == b[0] ? b[1] - a[1] : a[0] - b[0]));//注意中点要降序排列 否则像开头的图那样 重复区间被当成相交 结果错误
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