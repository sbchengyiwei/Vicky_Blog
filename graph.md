[200. Number of Islands](https://leetcode.com/problems/number-of-islands/)

Question:

Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return *the number of islands*.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

**Example 1:**

```
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

**Example 2:**

```
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

Solutions:

1. DFS (depth first search)

```java
class Solution {
    //time: O(m*n)  space: O(m*n)
    //graph : dfs
    int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    public int numIslands(char[][] grid) {
        int count = 0;
        //no need int[][] visit, just reset the nodes we visited to 0;
        // 1. for all valid nodes, calls dfs();
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++){
                if (grid[i][j] == '1') {
                    count ++;  // increased by 1 when entry a new island.
                    dfs(grid, i, j);
                }
            }
        }
        return count;
    }
    
    private void dfs(char[][] grid, int i, int j) {
        grid[i][j] = '0'; //put nodes to 0 first, prevent revisited;
        //3. recurse on neighbor nodes(if valid and not visited)
        for (int[] dir : dirs) {
            int x = i + dir[0], y = j + dir[1];
            if (x >= 0 && y >= 0 && x < grid.length && y < grid[0].length && grid[x][y] == '1') {
                dfs(grid, x, y);
            }
        }
    }
}

```



2. BFS (breadth first search)

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
  //time: O(m*n)  space: O(m or n)
    int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    public int numIslands(char[][] grid) {
        int count = 0;
        //1. for all valid nodes, calls bfs();
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++){
                if (grid[i][j] == '1') {
                    count ++;  // increased by 1 when entry a new island.
                    bfs(grid, i, j);
                }
            }
        }
        return count;
    }
    
    private void bfs(char[][] grid, int i, int j) {
        //2. recurse on neighbor nodes(if valid and not visited)
        Queue<Point> queue = new LinkedList<>();
        queue.offer(new Point(i, j));
        grid[i][j] = '0'; //put nodes to 0 first, prevent revisited;
        while (!queue.isEmpty()) {
            Point cur = queue.poll();
            for (int[] dir : dirs) {
                int x = cur.x + dir[0], y = cur.y + dir[1];
                if (x >= 0 && y >= 0 && x < grid.length && y < grid[0].length && grid[x][y] == '1') {
                    queue.offer(new Point(x, y));
                    grid[x][y] = '0';
                }
            }
        }

    }
}

```



3. Union Find

```java
class UnionFind {
    private int[] root;
    private int count;
    
    public UnionFind(char[][] grid) {
        int row = grid.length;
        int col = grid[0].length;
        count = row * col;
        root = new int[row * col];
        for (int i = 0; i < row * col; i++) {
            root[i] = i;
        }
    }
    
    public int find(int x) {
        if (x == root[x]) {
            return x;
        }
        return find(root[x]);
    }
    
    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            root[rootX] = rootY;
            count--;
        }
    }
    
    public int getCount() {
        return count;
    }
}

class Solution {
    //time: O(m*n)  space: O(m*n)
    //graph : union find
    int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    public int numIslands(char[][] grid) {
        //corner case stil need to write...
        int row = grid.length;
        int col = grid[0].length;
        int waters = 0;
        UnionFind uf = new UnionFind(grid);
   
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++){
                if (grid[i][j] == '0') {
                    waters++;
                } else {
                  // for all valid nodes, operate find and union;
                  for (int[] dir : dirs) {
                    int x = i + dir[0], y = j + dir[1];
                    if (x >= 0 && y >= 0 && x < grid.length && y < grid[0].length && grid[x][y] == '1') {
                       uf.union(x * col + y, i * col +j) ;
                    }
                  }
                }
            }
        }
        return uf.getCount() - waters;
    }
    
 
}
```

