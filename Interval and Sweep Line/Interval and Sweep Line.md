### How to solve the Interval Problems?

Way1: Boundary + PQ + Sweep line : 使用一般题型 可以用 count 来求出结果的

Way2: Arrays.sort +  Sweep line：需要知道起点终点具体对应那个区间 不能仅仅通过 count 求出结果的题

Way3: Two Pointers：已经给我们排好序了 时间复杂度为 O(n)

Way4: Dp/贪心 （难）

Exclusive



### Way1: Meeting Room

#### [56. Merge Intervals](https://leetcode-cn.com/problems/merge-intervals/) update start and end

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



#### [253. Meeting Rooms II](https://leetcode-cn.com/problems/meeting-rooms-ii/) use priorityqueue

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
   int num, type;
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

#### [1288. Remove Covered Intervals](https://leetcode-cn.com/problems/remove-covered-intervals/) 更新 end(记得 end也要排序)

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

**Tips: overlap = Math.max(a1, b1), Math.min(a2, b2) (if < 0 not overlap)**

#### [986. Interval List Intersections](https://leetcode-cn.com/problems/interval-list-intersections/)

```java
class Solution {
    public int[][] intervalIntersection(int[][] firstList, int[][] secondList) {
        List<int[]> res = new ArrayList<int[]>();
        int i = 0, j = 0;
        while (i < firstList.length && j < secondList.length) {
            int a1 =  firstList[i][0], a2 = firstList[i][1];
            int b1 = secondList[j][0], b2 = secondList[j][1];
            if (Math.max(a1, b1) <= Math.min(a2, b2)) {
                res.add(new int[]{Math.max(a1, b1), Math.min(a2, b2)});
            }
            if (a2 < b2) {i++;}
            else j++;
        }
        return res.toArray(new int[][]{});
    }
}
```



```java
class Solution {

  public int[] meetingPlanner(int[][] slotsA, int[][] slotsB, int dur) {
    int indexp  = 0;
    int indexq = 0;
    int[] p,q;
    while (indexp< slotsA.length && indexq < slotsB.length) {
        q = slotsB[indexq];
        p = slotsA[indexp];
        if (overlap(p, q) >= dur) {      // The key is how to calculate the overlap quickly.
          int start = Math.max(p[0],q[0]);
          return new int[]{start, start+ dur};
        }
        if (p[1] > q[1]) {
          indexq++;
        } else {        
          indexp++;
        }
    }
    return new int[]{};
  }
  
  
  ----
    ----
---
    ---
  ----
----
    // no mater which situation : overlap = min_end - max_start (if <=0: no overlap)
  private int overlap (int[] p, int[] q) {
    int start = Math.max(p[0], q[0]);
    int end = Math.min(p[1], q[1]);
    return end - start;
  }


}
```





### Way4: dp/贪心

#### [1024. Video Stitching](https://leetcode-cn.com/problems/video-stitching/)

```java

class Solution {
    public int videoStitching(int[][] clips, int time) {
        int[] dp = new int[time + 1];
        Arrays.fill(dp, Integer.MAX_VALUE - 1);
        dp[0] = 0;
        for (int i = 1; i <= time; i++) {
            for (int[] clip : clips) {
                if (clip[0] < i && i <= clip[1]) {
                    dp[i] = Math.min(dp[i], dp[clip[0]] + 1);
                }
            }
        }
        return dp[time] == Integer.MAX_VALUE - 1 ? -1 : dp[time];
    }
}
```

```java

class Solution {
    public int videoStitching(int[][] clips, int time) {
        int[] maxn = new int[time];
        int last = 0, ret = 0, pre = 0;
        for (int[] clip : clips) {
            if (clip[0] < time) {
                maxn[clip[0]] = Math.max(maxn[clip[0]], clip[1]);
            }
        }
        for (int i = 0; i < time; i++) {
            last = Math.max(last, maxn[i]);
            if (i == last) {
                return -1;
            }
            if (i == pre) {
                ret++;
                pre = last;
            }
        }
        return ret;
    }
}
```





### Exclusive part

Tips:  we don't care the intervals length above the cur interval

(don't care part)
  ---   cur
-------  stack.peek

#### [636. Exclusive Time of Functions](https://leetcode-cn.com/problems/exclusive-time-of-functions/)

```java
/*
question: give us a log and n return each nth funtion's elcusize time
input : int n (100)and List logs (500) timestamp(10^9) -> linear time
output is a int[] 
----
stack : 1. compare the end with start 
        2. sustract the last time length : lastend - laststart;
        3. add ith fuctions time to res[i]
    !!!!4. sustract ith len from the under interval
   
---
 n = 2, logs = ["0:start:0","1:start:2","1:end:5","0:end:6"]

stack {}

1: last from 2 to 5 
res[1] += 4
res[0] -= 4

0: last from 0 to 6
res[0] += 7

!!!!sustract cur's len from the under interval!!!!

*/


class Solution {
    public int[] exclusiveTime(int n, List<String> logs) {
        int[] res = new int[n];
        Stack<String[]> stack = new Stack<>();
 
        for (String log : logs) {
            String[] s = log.split(":");
            if (s[1].equals("start"))stack.push(s);
            else {
                String[] cur = stack.pop();
                int wholeLen=  Integer.parseInt(s[2]) - Integer.parseInt(cur[2]) + 1;
                res[Integer.parseInt(s[0])] += wholeLen;
                if (!stack.isEmpty()) res[Integer.parseInt(stack.peek()[0])] -= wholeLen;
            }
        }
        return res;
    }
}
```



