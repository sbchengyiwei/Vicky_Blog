## Content

- graph 问题上关于 bfs dfs uf 的选择 ： 首先如果都能用就和面试官逐个分析一下，如果是特殊问题（比如island2里cell不断地增加)uf只需对付新增的部分即可对付这些问题就要用uf， 其次 bfs 和 dfs 的时空复杂度差不多 通常先用dfs一般代码简洁些速度略快  有的题dfs不行必须bfs 那再用 bfs（比如找最短路径、分层递归等）

- 递归主要是使用栈空间 迭代会使用额外的存储空间（一般比栈空间大） 不能上来就说递归空间复杂度更高 因为比如树很深很满 其实迭代使用空间更多 所以还是具体问题具体分析 

- 先分析好时间空间复杂度 并且问清楚数据量多大 栈空间内存空间够不够 然后再做题

582. Kill Process
实现题：从暴力解到 DFS 和 BFS

```java
class Solution {
    //time exceed: O(n^n)! 一般超过 n^3 就要考虑优化了
    public List<Integer> killProcess(List<Integer> pid, List<Integer> ppid, int kill) {
        List<Integer> res = new ArrayList<Integer>();
        res.add(kill);
        for (int i = 0; i < ppid.size(); i++) {
            if (ppid.get(i) == kill) {
                res.addAll(killProcess(pid, ppid, pid.get(i))); 
            }
        }
        return res;
    }

    save time by using hashmap and constructing the tree structure.

    //dfs
    public List<Integer> killProcess(List<Integer> pid, List<Integer> ppid, int kill) {
        // construct the tree with adajcentcy list
        Map<Integer, List<Integer>> map = new HashMap<>();
        for (int i = 0; i < ppid.size(); i++) {
            if (ppid.get(i) > 0) {
                map.computeIfAbsent(ppid.get(i), k -> new ArrayList<>()).add(pid.get(i));
            }
        }
        List<Integer> res = new ArrayList<Integer>();
        res.add(kill);
        //dfs
        dfs(res, map, kill); 
        return res;
    }

    private void dfs(List<Integer> res, Map<Integer, List<Integer>> map, int kill) {
        if (map.containsKey(kill)) {
            for (int id : map.get(kill)) {
                res.add(id);
                dfs(res, map, id);
            }
        }
    }

   //bfs
    public List<Integer> killProcess(List<Integer> pid, List<Integer> ppid, int kill) {
        // construct the tree with adajcentcy list
        Map<Integer, List<Integer>> map = new HashMap<>();
        for (int i = 0; i < ppid.size(); i++) {
            if (ppid.get(i) > 0) {
                map.computeIfAbsent(ppid.get(i), k -> new ArrayList<>()).add(pid.get(i));
            }
        }
        List<Integer> res = new ArrayList<Integer>();
        
        //bfs
        Queue<Integer> queue = new ArrayDeque<>();
        queue.offer(kill);
        while (!queue.isEmpty()) {
            int currentKill = queue.poll();
            res.add(currentKill);
            if (map.containsKey(currentKill)) {  //注意当走到叶子节点的时候就没有 map 的值了
                for (int id : map.get(currentKill)) {
                    queue.offer(id);
                }
            }
        }
        return res;
    }
}
```

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
    private int[] id;
    private int[] sz;
  
    private int count;
    
    public UnionFind(char[][] grid) {
        int row = grid.length;
        int col = grid[0].length;
        count = row * col;
        id = new int[row * col];
        sz = new int[row * col]; //weighted quick-union
        for (int i = 0; i < row * col; i++) {
            id[i] = i;
            sz[i] = 1;  
        }
    }
    
    public int find(int x) {
        while (x != id[x]) {
            id[x] = id[id[x]]; // compress path
            x = id[x];
        }
        return x;
    }
    
    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (sz[rootX] > sz[rootY]) {id[rootY] = rootX; sz[rootX] += sz[rootY];}
            else {id[rootX] = rootY; sz[rootY] += sz[rootX];}
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
        return uf.getCount() - waters; // components minus 0(waters)
    } 
}
```

