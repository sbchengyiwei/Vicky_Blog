### How to solve the Interval Problems?

Way1: Boundary + PQ + Sweep line : 使用一般题型 可以用 count 来求出结果的

Way2: Arrays.sort +  Sweep line：需要知道起点终点具体对应那个区间 不能仅仅通过 count 求出结果的题

Way3: Two Pointers：已经给我们排好序了 时间复杂度为 O(n)



### Way1: Meeting Room

#### [56. Merge Intervals](https://leetcode-cn.com/problems/merge-intervals/)

```java
class Boundary {
    int num, type;
    public Boundary(int num, int type) {
        this.num = num;
        this.type = type;
    }   
}
public class Solution {
    /**
     * @param intervals: interval list.
     * @return: A new interval list.
     */
    public List<Interval> merge(List<Interval> intervals) {
        //if not valid return 0
        PriorityQueue<Boundary> pq = new PriorityQueue<>((a, b) -> (a.num == b.num ? a.type - b.type : a.num - b.num));
        for (Interval interval : intervals) {
            pq.offer(new Boundary(interval.start, -1));
            pq.offer(new Boundary(interval.end, 1));
        }
        int count = 0, left = 0, right = 0;
        List<Interval> res = new ArrayList<>();
        while (!pq.isEmpty()) {  
            Boundary cur = pq.poll();
            if (count == 0) left = cur.num;
            count += cur.type;
            if (count == 0) {right = cur.num; res.add(new Interval(left, right));}
        }
        return res;
    }
}
```



#### [253. Meeting Rooms II](https://leetcode-cn.com/problems/meeting-rooms-ii/)

```java
//sweep line
/**
 * Definition of Interval:
 * public classs Interval {
 *     int start, end;
 *     Interval(int start, int end) {
 *         this.start = start;
 *         this.end = end;
 *     }
 * }
 */

class Boundary {
   intth
    public Boundary(int num, int type) {
        this.num = num;
        this.type = type;
    }   
}

public class Solution {
    /**
     * @param intervals: an array of meeting time intervals
     * @return: the minimum number of conference rooms required
     */
    public int minMeetingRooms(List<Interval> intervals) {
        //if not valid return 0
        PriorityQueue<Boundary> pq = new PriorityQueue<>((a, b) -> (a.num == b.num ? a.type - b.type : a.num - b.num));
        for (Interval interval : intervals) {
            pq.offer(new Boundary(interval.start, 1));
            pq.offer(new Boundary(interval.end, -1));
        }
        int count = 0, res = 0;
        while (!pq.isEmpty()) {  
            
            count += pq.poll().type;
            res = Math.max(res, count);
        }
        return res;
    }
}
```



### Way2: Intervals merging and intersection

#### [1288. Remove Covered Intervals](https://leetcode-cn.com/problems/remove-covered-intervals/)

```java
class Solution {
    public int removeCoveredIntervals(int[][] intervals) {
        Arrays.sort(intervals, (a, b)->(a[0] == b[0] ? b[1] - a[1] : a[0] - b[0]));//Note that the ends should be arranged in descending order otherwise repeated intervals as shown in the opening diagram are treated as intersecting errors(as the following picture)

        int end = intervals[0][1];
        int cover = 0;
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < end && intervals[i][1] <= end) {
                cover++;
            } else {
                end = intervals[i][1];
            }
        }
        return intervals.length - cover;
    }
}
```

![Image](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/images/Screen%20Shot%202021-06-20%20at%201.16.17%20AM.png)



**贪心算法**

#### [435. Non-overlapping Intervals](https://leetcode-cn.com/problems/non-overlapping-intervals/)

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> (a[1] - b[1]));
        int canWork = 1;
        int end = intervals[0][1];
        for (int i = 1; i < intervals.length; i++) {
            if (end <= intervals[i][0]) {
                canWork++;
                end = intervals[i][1];
            }
        }
        return intervals.length - canWork;
    }
}
```



### Way3: Two Pointers

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