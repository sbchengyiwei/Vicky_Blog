



## Content

- [1 Definations](#1-definations)

- [2 Implementations](#2-implementations)

- [3 Use cases](#3-use-cases)
  * [3-1 Check cycle](#3-1-check-cycle)
  * [3-2 Get the number of components](#3-2-get-the-number-of-components)
  * [3-3 Check connectivity](#3-3-check-connectivity)
  
- [4 Advanced: Add weighed edges to Unionfind](#4-advanced--add-weighed-edges-to-unionfind)

  

## 1 Definations

> What is graph : Similar to the concept of LinkedList, the data in memory is not necessarily continuous, which is composed of the reference of each node. There are three ways to construct a graph : **Adajcentcy list / Edge list / Adajcentcy matrix.**
>
> A [*disjoint-set data structure*](http://en.wikipedia.org/wiki/Disjoint-set_data_structure) is a data structure that keeps track of a set of elements partitioned into a number of disjoint (non-overlapping) subsets. A [*union-find algorithm*](http://en.wikipedia.org/wiki/Disjoint-set_data_structure) is an algorithm that performs two useful operations on such a data structure:
> ***Find:*** Determine which subset a particular element is in. This can be used for determining if two elements are in the same subset.
> ***Union:*** Join two subsets into a single subset.



- **Union-Find API**

   The following API encapsulates the basic operations that we need.

<div align=center><img src="https://algs4.cs.princeton.edu/15uf/images/uf-api.png" width="500" height="130" /></div>



## 2 Implementations

```java
/*
                          constructor    union  find 

quick-find                    N             N     1

quick-union                   N      tree height tree height

weighted quick-union          N           lg N   lg N

path compression               N            <5     <5
*/
```

- Quick Find 

  ![Quick find overview](https://algs4.cs.princeton.edu/15uf/images/quick-find-overview.png)

- Quick Union

  ![Quick union overview](https://algs4.cs.princeton.edu/15uf/images/quick-union-overview.png)

- Weighted quick-union

  ![Weighted quick union overview](https://algs4.cs.princeton.edu/15uf/images/weighted-quick-union-overview.png)

- **Weighted quick-union with path compression.**

  > There are a number of easy ways to improve the weighted quick-union algorithm further. Ideally, we would like every node to link directly to the root of its tree, but we do not want to pay the price of changing a large number of links. We can approach the ideal simply by making all the nodes that we do examine directly link to the root.

  ```java
  class WeightedQuickUnionPathCompressionUF {
      private int[] id;
    	private int[] sz;
      private int count;
      
      public UnionFind(int n) {
          count = n;
          id = new int[n];
        	sz = new int[n]; //weighted quick-union
          for (int i = 0; i <n; i++) {
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
      
      public int count() {
          return count;
      }
    
    	public boolean connected(int x, int y) {
        return find(x) == find(y);
      }
  }
  ```

  

## 3 Use cases

### 3-1 Check cycle

> Check whether a given (undirected) graph contains a cycle or not.

- [684. Redundant Connection](https://leetcode-cn.com/problems/redundant-connection/)

  ```java
  //Union find Solution in java
  class UnionFind {
  	// Same as WeightedQuickUnionPathCompressionUF class in [2-Implementations].
  }
  
  class Solution {
      //time: O(2n*α);  space: O(2n).
      public int[] findRedundantConnection(int[][] edges) {
         if (edges == null || edges.length == 0) return 0;
          UnionFind uf = new UnionFind(edges.length * 2);
          for (int[] edge : edges) {
              if (uf.connected(edge[0],edge[1])) {return edge;} // if already connected, this edge is a Redundant Connection, this graph has at least one cycle.
              else {uf.union(edge[0], edge[1]);} 
          }
          return new int[]{-1, -1};
      }
  }
  ```



### 3-2 Get the number of components

- [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)

  ```java
  //Union find Solution in java
  class UnionFind {
  	// Same as WeightedQuickUnionPathCompressionUF class in [2-Implementations].
  }
  
  class Solution {
      //time: O(m*n*α);  space: O(m*n).
      int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
      
      public int numIslands(char[][] grid) {
          if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
          int row = grid.length;
          int col = grid[0].length;
          int waters = 0;
          UnionFind uf = new UnionFind(grid);
          for (int i = 0; i < row; i++) {
              for (int j = 0; j < col; j++) {
                  if (grid[i][j] == '0') {
                      waters++;
                  } else {
                      for (int[] dir : dirs) {
                          int x = i + dir[0], y = j + dir[1];
                          if (x >= 0 && y >= 0 && x < grid.length && y < grid[0].length && grid[x][y] == '1') {
                              uf.union(i * col + j, x * col + y); // convert grid to array
  
                          }
                      }
                  }
              }
          }
          return uf.count() - waters;
      }
  }
  ```



- [547. 省份数量](https://leetcode-cn.com/problems/number-of-provinces/)

  ```java
  //Union find Solution in java
  class UnionFind {
  	// Same as WeightedQuickUnionPathCompressionUF class in [2-Implementations].
  }
  
  class Solution {
    //time: O(2n*α);  space: O(2n).
      public int findCircleNum(int[][] isConnected) {
          if (isConnected == null || isConnected.length == 0) return 0;
          UnionFind uf = new UnionFind(isConnected.length);
          for (int i = 0; i < isConnected.length; i++) {
              for (int j = 0; j < isConnected[0].length; j++) {
                  if (isConnected[i][j] == 1) uf.union(i, j);  // not '1', notice the data type
              }
          }
          return uf.count();
      }
  }
  ```

- [1319. 连通网络的操作次数](https://leetcode-cn.com/problems/number-of-operations-to-make-network-connected/)

  ```java
   //Union find Solution in java
  class UnionFind {
  	// Same as WeightedQuickUnionPathCompressionUF class in [2-Implementations].
  }
  
  class Solution {
    //time: O(2n*α);  space: O(2n).
    //The number of edges to move is the number of components - 1
      public int makeConnected(int n, int[][] connections) {
          if (connections.length < n - 1) return -1; // If the number connections is larger than (the number of computers - 1), all computers can not be connected together.
          UnionFind uf = new UnionFind(n);
          for (int[] connection : connections) {
              uf.union(connection[0], connection[1]);
          }
          int count = uf.count();
          return count - 1;
      }
  }
  ```

  

### 3-3 Check connectivity

> Keep track of connectivity of each element in a particular subset or connectivity of subsets with each other. / Do operation to the connected or not connected set. / Connect sets and return .

- [721. 账户合并](https://leetcode-cn.com/problems/accounts-merge/)

  ```java
  //Union find Solution in java
  class UnionFind {
  	// Same as WeightedQuickUnionPathCompressionUF class in [2-Implementations].
  }
  
  class Solution {
      public List<List<String>> accountsMerge(List<List<String>> accounts) {
          //time: O(m*n*α);  space: O(m*n).
        	if (accounts == null) return new ArrayList<>();
          //construct the graph
          Map<String, String> emailToName = new HashMap<>(); // each email -> name
          Map<String, Integer> emailToId = new HashMap<>();  // each email -> id
          
          int id = 0;
          UnionFind uf = new UnionFind(10000); // 1000 * 10 
        
          for (List<String> account : accounts) {
              String name = account.get(0);
              for (int i = 1; i < account.size(); i++) {
                  emailToName.putIfAbsent(account.get(i), name);
                  emailToId.putIfAbsent(account.get(i), id++);
                  uf.union(emailToId.get(account.get(1)), emailToId.get(account.get(i)));
              }
          }
          //each email has an id , if email is union then they have the same root! id
          // each email has a name,  but we cannot determine whehter they are same person, name can repeat.
          // but we must can arrange the same id's email a right name, don' worry.
          Map<Integer, List<String>> res = new HashMap<>();
          for (String email : emailToId.keySet()) {
              int eid = uf.find(emailToId.get(email));  // use root id!
              res.computeIfAbsent(eid, k -> new ArrayList<>()).add(email);
          }
          for (List<String> component : res.values()) {
              Collections.sort(component);
              component.add(0, emailToName.get(component.get(0)));
          }
          return new ArrayList(res.values());   
          
      }
  }
  ```
  
  
  
- [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

  ```java
  //Union find Solution in java
  class UnionFind {
  	// Same as WeightedQuickUnionPathCompressionUF class in [2-Implementations].
  }
  
  class Solution {
   	 //time: O(m*n*α);  space: O(m*n).
      int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
      public void solve(char[][] board) {
          int row = board.length;
          int col = board[0].length;
          
          // 用一个虚拟节点, 边界上的O 的父节点都是这个虚拟节点 (decrease time complexity! same as percolation)
          UnionFind uf = new UnionFind(rows * cols + 1);
          int dummyNode = rows * cols;
  
          for (int i = 0; i < row; i++) {
              for (int j = 0; j < col; j++) {
                  if (board[i][j] == 'O') {
                      if (i == 0 || i == row - 1 || j == 0 || j == col - 1) {uf.union(i * cols + j, dummyNode);}
                      else {
                        for (int[] dir : dirs) {
                          int x = i + dir[0], y = j + dir[1];
                          if (x >= 0 && y >= 0 && x < board.length && y < board[0].length && board[x][y] == 'O') {
                              uf.union(i * col + j, x * col + y); // convert board to array
                          }
                      	}
                      }
                  }
              }
          }
          
          for (int i = 0; i < rows; i++) {
              for (int j = 0; j < cols; j++) {
                  if (uf.isConnected(node(i, j), dummyNode)) {
                      // 和dummyNode 在一个连通区域的,那么就是O；
                      board[i][j] = 'O';
                  } else {
                      board[i][j] = 'X';
                  }
              }
          }
  
      }
  }
  ```
  
  
  
- [1631. 最小体力消耗路径](https://leetcode-cn.com/problems/path-with-minimum-effort/)

  ```java
  //Union find Solution in java
  class UnionFind {
  	// Same as WeightedQuickUnionPathCompressionUF class in [2-Implementations].
  }
  
  // One edge is composed of edge=[x,y,d], where d is the absolute value of the difference between x and y
  class Edge {
      int x, y, len;
      public Edge(int x, int y, int len) {
          this.x = x;
          this.y = y;
          this.len = len;
      }
  }
  
  // 2.根据边列表中的 各点差的绝对值 从小到大排序
  
  // 3.依次遍历edges， 并将各点进行连通， 当最左上与最左下 第一次连通时结束，并输出 所遍历过edges中的最大d
  
  class Solution {
      //time: O(m*n*α);  space: O(m*n).
      
      public int minimumEffortPath(int[][] heights) {
      	// corner case...
          int row = heights.length, col = heights[0].length;
  
          // 将所有的边存入list
          List<Edge> list = new ArrayList<>();
          for (int i = 0; i < row; i++) {
              for (int j = 0; j < col; j++) {
              	// 分别存一下向右走和向下走的边 就可以了！ 就能保证全部连通
                  if (j < col - 1) {
                      list.add(new Edge(i * col + j, i * col + j + 1, Math.abs(heights[i][j] - heights[i][j + 1])));
                  }
                  if (i < row - 1) {
                      list.add(new Edge(i * col + j, (i + 1) * col + j, Math.abs(heights[i][j] - heights[i + 1][j])));
                  }
              }
          }
          
          // Sort the edge list from small to large according to len
          Collections.sort(list, (e1, e2) -> (e1.len - e2.len));
          
          UnionFind uf = new UnionFind(row * col);
          
          int start = 0, end = row * col - 1;
          
          for (int i = 0; i < list.size(); i++) {
              Edge edge = list.get(i);
              uf.union(edge.x, edge.y);
              // 发现起点终点连通了，则返回当前边的权
              if (uf.find(start) == uf.find(end)) {
                  return edge.len;
              }
          }
          
          // 这条语句走不到，因为答案一定存在
          return 0;
      }  
  
  }       
  ```
  



## 4 Advanced: Add weighted edges to Unionfind

- [399. 除法求值](https://leetcode-cn.com/problems/evaluate-division/)

  ```java
  // 并查集结构——底层通过数组实现
  class UnionFind {
      // 指向父节点的id
      private int[] id;
  
      // 指向父节点的权值
      private double[] weight;
  
      public UnionFind(int n) {
          id = new int[n];
          weight = new double[n];
          for (int i = 0; i < n; i++) {
              id[i] = i;
              id[i] = 1.0d;
          }
      }
  
      /**
       * 结点合并
       * @param x  未知结点id
       * @param y
       * @param value  两个结点连通的权值
       */
      public void union(int x, int y, double value) {
          // 首先获得两个结点的根节点的id
          int rootX = find(x);
          int rootY = find(y);
          if (rootX == rootY) {
              // 本身就是连通分量
              return;
          }
  
          id[rootX] = rootY;
          weight[rootX] = weight[y] * value / weight[x];
      }
  
      /**
       * 路径压缩 —— 递归实现
       * @param x
       * @return  根节点的id
       */
      public int find(int x) {
          if (x != id[x]) {
              int origin = id[x];  
              id[x] = find(id[x]);  // 递归调用找到最终根节点  不能用 while 必须用递归好好的一层一层找 * weight 
              weight[x] *= weight[origin];  // post order 因为前面要用传上来的数
          }
          return id[x];
      }
  
      /**
       * 并查集——查询方法
       * @param x
       * @param y
       * @return
       */
      public double isConnectedOrFraction(int x, int y) {
          int rootX = find(x);
          int rootY = find(y);
          if (rootX == rootY) {
              return weight[x] / weight[y];
          }else {
              return -1.0d;
          }
      }
  }
  
  
  class Solution {
      public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
          //corner case
          //union
          Map<String, Integer> map = new HashMap<>();
          int id = 0;
          UnionFind uf = new UnionFind(2 * equations.size());
          for (int i = 0; i < equations.size(); i++) {
              String var1 = equations.get(i).get(0);
              String var2 = equations.get(i).get(1);
              map.putIfAbsent(var1, id++);
              map.putIfAbsent(var2, id++);
              uf.union(map.get(var1), map.get(var2), values[i]);
          }
          
          // query
          double[] res = new double[queries.size()]; 
          for (int i = 0; i < queries.size(); i++) {
              String var1 = queries.get(i).get(0);
              String var2 = queries.get(i).get(1);
              Integer id1 = map.get(queries.get(i).get(0));
              Integer id2 = map.get(queries.get(i).get(1));
  
              if (id1 == null || id2 == null) {
                  res[i] = -1.0; // Return the answers to all queries. If a single answer cannot be determined, return -1.0.
              }else {
                  res[i] = uf.isConnectedOrFraction(id1, id2);
              }
          }
          return res;
      }   
  }
  ```

  


