![img](https://assets.leetcode.com/uploads/2021/03/31/maze1-1-grid.jpg)

#### [490. The Maze](https://leetcode-cn.com/problems/the-maze/)

```java
class Point {
    int x;
    int y;
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

class Solution {
    //Time:O(MN) Space:O(MN)
    public boolean hasPath(int[][] maze, int[] start, int[] destination) {
        int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        int row = maze.length;
        int col = maze[0].length;
        Queue<Point> queue = new ArrayDeque<>();
        boolean[][] visited = new boolean[row][col];
        queue.offer(new Point(start[0], start[1]));
        visited[start[0]][start[1]] = true;
        while(!queue.isEmpty()) {
            Point cur = queue.poll();
            if (cur.x == destination[0] && cur.y == destination[1]) return true;
            for (int[] dir : dirs) {
                int x = cur.x;
                int y = cur.y;
                while (valid(maze, x + dir[0], y + dir[1])) {  // the key of this question
                    x += dir[0];
                    y += dir[1];   
                }
                if (!visited[x][y]) {queue.offer(new Point(x,  y)); visited[cur.x][cur.y] = true;} //判断加不加入queue 除了 valid 主要是看是不是 visited 过，BFS 一般 visited 也是加入 queue 就会同步更新（放到上面速度慢），注意和 BestFs 的区别
            }
        }
        return false;
    }
    
    private boolean valid(int[][] maze, int x, int y) {
        int row = maze.length;
        int col = maze[0].length;
        return x >= 0 && y >= 0 && x < row && y < col && maze[x][y] != 1;
    }
}


```

#### [505. The Maze II](https://leetcode-cn.com/problems/the-maze-ii/)

```java
class Point {
    int x;
    int y;
    int dis;
    public Point(int x, int y, int dis) {
        this.x = x;
        this.y = y;
        this.dis = dis;
        
    }
}

class Solution {
    //Time:O(MN*logMN) Space:O(MN)  用到 priorityqueue 每次加入取出需要 logn 时间
    public int shortestDistance(int[][] maze, int[] start, int[] destination) {
        int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        int row = maze.length;
        int col = maze[0].length;
        PriorityQueue<Point> queue = new PriorityQueue<>((a, b) -> (a.dis - b.dis));
        boolean[][] visited = new boolean[row][col];
        queue.offer(new Point(start[0], start[1], 0));
        while(!queue.isEmpty()) {
            Point cur = queue.poll();
            if (cur.x == destination[0] && cur.y == destination[1]) return cur.dis;
            visited[cur.x][cur.y] = true; //visited poll 出来之后才会更新，因为谁先走到的算谁走过这个点，dis 不一样
            for (int[] dir : dirs) {
                int x = cur.x;
                int y = cur.y;
                int dis = cur.dis;
                while (valid(maze, x + dir[0], y + dir[1])) {  // the key of this question
                    x += dir[0];
                    y += dir[1];
                    dis++;
                }
                if (!visited[x][y]) {queue.offer(new Point(x,  y, dis));}
            }
        }
        return -1;
    }
    
    private boolean valid(int[][] maze, int x, int y) {
        int row = maze.length;
        int col = maze[0].length;
        return x >= 0 && y >= 0 && x < row && y < col && maze[x][y] != 1;
    }
}



```



![img](https://assets.leetcode.com/uploads/2021/03/31/maze3-1-grid.jpg)

#### [499. The Maze III](https://leetcode-cn.com/problems/the-maze-iii/)

```java
class Point {
    int x;
    int y;
    int dis;
    String ori;

    public Point(int x, int y, int dis, String ori) {
        this.x = x;
        this.y = y;
        this.dis = dis;
        this.ori = ori;
    }
}

class Solution {
    //Time:O(MN*logMN) Space:O(MN)
    //Differences to MazeII: 1. add orientation 2. change the order of directions 3. break if roll onto the hole
    public String findShortestWay(int[][] maze, int[] ball, int[] hole) {
        int[][] dirs = {{1, 0}, {0, 1}, {0, -1}, {-1, 0}};
        String[] oris = {"d", "r", "l", "u"};
        int row = maze.length;
        int col = maze[0].length;
        PriorityQueue<Point> queue = new PriorityQueue<>((a, b) -> (a.dis == b.dis ? a.ori.compareTo(b.ori) : a.dis - b.dis));
        boolean[][] visited = new boolean[row][col];
        queue.offer(new Point(ball[0], ball[1], 0, ""));
        while(!queue.isEmpty()) {
            Point cur = queue.poll();
            if (cur.x == hole[0] && cur.y == hole[1]) return cur.ori;
            visited[cur.x][cur.y] = true; //visited poll 出来之后才会更新，因为谁先走到的算谁走过这个点，dis 不一样
            for (int i = 0; i < 4; i++) {
                int x = cur.x;
                int y = cur.y;
                int dis = cur.dis;
                String ori = cur.ori;
                while (valid(maze, x + dirs[i][0], y + dirs[i][1])) {  // the key of this question 
                    x += dirs[i][0];
                    y += dirs[i][1];
                    dis++;
                    if (x == hole[0] && y == hole[1]) break;  //break if roll onto the hole
                }

                if (!visited[x][y]) {queue.offer(new Point(x,  y, dis, ori + oris[i]));}
            }
        }
        return "impossible";
    }
    
    private boolean valid(int[][] maze, int x, int y) {
        int row = maze.length;
        int col = maze[0].length;
        return x >= 0 && y >= 0 && x < row && y < col && maze[x][y] != 1;
    }
}
```

