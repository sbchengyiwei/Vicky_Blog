



## 1 Definations

> A [*disjoint-set data structure*](http://en.wikipedia.org/wiki/Disjoint-set_data_structure) is a data structure that keeps track of a set of elements partitioned into a number of disjoint (non-overlapping) subsets. A [*union-find algorithm*](http://en.wikipedia.org/wiki/Disjoint-set_data_structure) is an algorithm that performs two useful operations on such a data structure:
> ***Find:*** Determine which subset a particular element is in. This can be used for determining if two elements are in the same subset.
> ***Union:*** Join two subsets into a single subset.

**Union-Find API**

 The following API encapsulates the basic operations that we need.

<div align=center><img src="https://algs4.cs.princeton.edu/15uf/images/uf-api.png" width="500" height="130" /></div>



## 2 Implementations

```java
/*
                          constructor    union  find 

quick-find                    N             N     1

quick-union                   N      tree height tree height

weighted quick-union          N           lg N   lg N

path compresson               N            <5     <5
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
public WeightedQuickUnionPathCompressionUF(int n) {
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
    
    public int count() {
        return count;
    }
  
  	public boolean connected(int i, int j) {
      return find(i) == find(j);
    }
}
```



## 3 Use cases

- **Check whether a given (undirected) graph contains a cycle or not.**

```java
// Java Program for union-find algorithm to detect cycle in a graph
import java.util.*;
import java.lang.*;
import java.io.*;

class Graph
{
	int V, E; // V-> no. of vertices & E->no.of edges
	Edge edge[]; // /collection of all edges

	class Edge
	{
		int src, dest;
	};

	// Creates a graph with V vertices and E edges
	Graph(int v,int e)
	{
		V = v;
		E = e;
		edge = new Edge[E];
		for (int i=0; i<e; ++i)
			edge[i] = new Edge();
	}

	// A utility function to find the subset of an element i
	int find(int parent[], int i)
	{
		if (parent[i] == -1)
			return i;
		return find(parent, parent[i]);
	}

	// A utility function to do union of two subsets
	void Union(int parent[], int x, int y)
	{
		parent[x] = y;
	}


	// The main function to check whether a given graph
	// contains cycle or not
	int isCycle( Graph graph)
	{
		// Allocate memory for creating V subsets
		int parent[] = new int[graph.V];

		// Initialize all subsets as single element sets
		for (int i=0; i<graph.V; ++i)
			parent[i]=-1;

		// Iterate through all edges of graph, find subset of both
		// vertices of every edge, if both subsets are same, then
		// there is cycle in graph.
		for (int i = 0; i < graph.E; ++i)
		{
			int x = graph.find(parent, graph.edge[i].src);
			int y = graph.find(parent, graph.edge[i].dest);

			if (x == y)
				return 1;

			graph.Union(parent, x, y);
		}
		return 0;
	}

	// Driver Method
	public static void main (String[] args)
	{
		/* Let us create the following graph
		0
		| \
		| \
		1---2 */
		int V = 3, E = 3;
		Graph graph = new Graph(V, E);

		// add edge 0-1
		graph.edge[0].src = 0;
		graph.edge[0].dest = 1;

		// add edge 1-2
		graph.edge[1].src = 1;
		graph.edge[1].dest = 2;

		// add edge 0-2
		graph.edge[2].src = 0;
		graph.edge[2].dest = 2;

		if (graph.isCycle(graph)==1)
			System.out.println( "graph contains cycle" );
		else
			System.out.println( "graph doesn't contain cycle" );
	}
}

```

- **Keep track of connectivity of each element in a particular subset or connectivity of subsets with each other. (examples in next section)**

## 4 LeetCode Quesions

- [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)

  ```java
  //Union find Solution in java
  class UnionFind {
      int count; // number of components (island and water)
      int[] id;
      int[] sz;
      
      public UnionFind(char[][] grid) {
          int row = grid.length;
          int col = grid[0].length;
          count = col * row;
          id = new int[count];
          sz = new int[count];
        //! initialize the id[] by i and sz[] by 1
          for (int i = 0; i < count; i++) {
              id[i] = i;
              sz[i] = 1;
          }
      }
      
      public int find(int x) {
          while (x != id[x]) {
              id[x] = id[id[x]]; //compress path
              x = id[x];
          }
          return x;
      }
      
      public void union(int p, int q) {
          int rootP = find(p);
          int rootQ = find(q);
  
          if (rootP != rootQ) {
              if (sz[rootP] > sz[rootQ]) {id[rootQ] = rootP; sz[rootP]++;}
              else {id[rootP] = rootQ; sz[rootQ]++;}
              count -= 1;  
             
          }
      }
      
      public int count() {
          return count;
      }
  }
  
  class Solution {
      //time: O(m*n*k)  space: O(m*n)
    	//After the path compression, k is smaller than 5.
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

  

- [721. 账户合并](https://leetcode-cn.com/problems/accounts-merge/)

  ```java
  class UnionFind {
      int[] id;
      int[] sz; 
      public UnionFind(int n) {
          id = new int[n];
          sz = new int[n];
        //! initialize the id[] by i and sz[] by 1
          for (int i = 0; i < n; i++) {
              id[i] = i;
              sz[i] = 1;
          }
      }
      
      public int find(int x) {
          while (x != id[x]) {
              id[x] = id[id[x]]; //compress path
              x = id[x];
          }
          return x;
      }
      
      public void union(int p, int q) {
          int rootP = find(p);
          int rootQ = find(q);
  
          if (rootP != rootQ) {
              if (sz[rootP] > sz[rootQ]) {id[rootQ] = rootP; sz[rootP]++;}
              else {id[rootP] = rootQ; sz[rootQ]++;}  
          }
      }  
  }
  
  
  class Solution {
      public List<List<String>> accountsMerge(List<List<String>> accounts) {
          //union find 
          //HashMap
          //List<List<String>> accounts [list :each people[ list: name, email1, email2...]]
          //construct the graph
          Map<String, String> emailToName = new HashMap<>();
          Map<String, Integer> emailToId = new HashMap<>();
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
          //each email has a id , if email is union then they have the same root! id
          
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

  

- [547. 省份数量](https://leetcode-cn.com/problems/number-of-provinces/)

  ```java
  class UnionFind {
      int count; // number of components (island and water)
      int[] id;
      int[] sz;
      
      public UnionFind(int n) {
  
          count = n;
          id = new int[count];
          sz = new int[count];
        //! initialize the id[] by i and sz[] by 1
          for (int i = 0; i < count; i++) {
              id[i] = i;
              sz[i] = 1;
          }
      }
      
      public int find(int x) {
          while (x != id[x]) {
              id[x] = id[id[x]]; //compress path
              x = id[x];
          }
          return x;
      }
      
      public void union(int p, int q) {
          int rootP = find(p);
          int rootQ = find(q);
  
          if (rootP != rootQ) {            
              if (sz[rootP] > sz[rootQ]) {id[rootQ] = rootP; sz[rootP]++;}
              else {id[rootP] = rootQ; sz[rootQ]++;}
              count -= 1;  
             
          }
      }
      
      public int count() {
          return count;
      }
  }
  
  class Solution {
      public int findCircleNum(int[][] isConnected) {
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

  

- [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

  ```java
  class UnionFind {
  
      int[] id;
      int[] sz;
      
      public UnionFind(int n) {
  
          id = new int[n];
          sz = new int[n];
        //! initialize the id[] by i and sz[] by 1
          for (int i = 0; i < n; i++) {
              id[i] = i;
              sz[i] = 1;
          }
      }
      
      public int find(int x) {
          while (x != id[x]) {
              id[x] = id[id[x]]; //compress path
              x = id[x];
          }
          return x;
      }
      
      public void union(int p, int q) {
          int rootP = find(p);
          int rootQ = find(q);
  
          if (rootP != rootQ) {
              if (sz[rootP] > sz[rootQ]) {id[rootQ] = rootP; sz[rootP]++;}
              else {id[rootP] = rootQ; sz[rootQ]++;}
  
             
          }
      }
  
  }
  
  
  class Solution {
      int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
      public void solve(char[][] board) {
          int row = board.length;
          int col = board[0].length;
          UnionFind uf = new UnionFind(col * row);
          for (int i = 0; i < row; i++) {
              for (int j = 0; j < col; j++) {
                  if (board[i][j] == 'O') {
                      
                      for (int[] dir : dirs) {
                          int x = i + dir[0], y = j + dir[1];
                          if (x >= 0 && y >= 0 && x < board.length && y < board[0].length && board[x][y] == 'O') {
                              uf.union(i * col + j, x * col + y); // convert board to array
                          }
                      }
                  }
              }
          }
          List<int[]> borders = new ArrayList<>();
          for (int i = 0; i < row; i++) {
              for (int j = 0; j < col; j++) {
                  if (board[i][j] == 'O' && (i == 0 || i == row - 1 || j == 0 || j == col - 1)) {
                      borders.add(new int[]{i,j});
                  }
              }
          }
          
          for (int i = 0; i < row; i++) {
              for (int j = 0; j < col; j++) {
                  if (board[i][j] == 'O') {
                      boolean isborder = false;
                      for (int[] border : borders) {
                          if (uf.find(i * col + j) == uf.find(border[0] * col + border[1])) {
                              isborder = true;
                          } 
                      }
                      if (isborder == false) board[i][j] = 'X';
                  }
              }
          }
  
          
      }
  }
  ```

  

- [1631. 最小体力消耗路径](https://leetcode-cn.com/problems/path-with-minimum-effort/)

  ```java
  
  // 1.先构建各点之间的边列表edges。 其中由edge=[x,y,d]组成，d为点x与y差的绝对值
  
  
  // 2.根据边列表中的 各点差的绝对值 从小到大排序
  
  // 3.依次遍历edges， 并将各点进行连通， 当最左上与最左下 第一次连通时结束，并输出 所遍历过edges中的最大d
  
  
  class UnionFind {
  
      int[] id;
      int[] sz;
      
      public UnionFind(int n) {
  
          id = new int[n];
          sz = new int[n];
        //! initialize the id[] by i and sz[] by 1
          for (int i = 0; i < n; i++) {
              id[i] = i;
              sz[i] = 1;
          }
      }
      
      public int find(int x) {
          while (x != id[x]) {
              id[x] = id[id[x]]; //compress path
              x = id[x];
          }
          return x;
      }
      
      public void union(int p, int q) {
          int rootP = find(p);
          int rootQ = find(q);
  
          if (rootP != rootQ) {
              if (sz[rootP] > sz[rootQ]) {id[rootQ] = rootP; sz[rootP]++;}
              else {id[rootP] = rootQ; sz[rootQ]++;}
  
             
          }
      }
  
  }
  
  class Edge {
      int x, y, len;
  
      public Edge(int x, int y, int len) {
          this.x = x;
          this.y = y;
          this.len = len;
      }
  }
  
  
  class Solution {
  
      
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
          
          // 将所有的边排个序
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

  

- [399. 除法求值](https://leetcode-cn.com/problems/evaluate-division/)

union find with value;

```java
// 并查集结构——底层通过数组实现
class UnionFind {
    // 指向父节点的id
    private int[] parent;

    // 指向父节点的权值
    private double[] weight;

    public UnionFind(int n) {
        this.parent = new int[n];
        this.weight = new double[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            weight[i] = 1.0d;
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

        parent[rootX] = rootY;
        weight[rootX] = weight[y] * value / weight[x];
    }

    /**
     * 路径压缩 —— 递归实现
     * @param x
     * @return  根节点的id
     */
    public int find(int x) {
        if (x != parent[x]) {
            int origin = parent[x];  
            parent[x] = find(parent[x]);  // 递归调用找到最终根节点  不能用 while 必须用递归好好的一层一层找 * weight 
            weight[x] *= weight[origin];  // post order 因为前面要用传上来的数
        }
        return parent[x];
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



- [1319. 连通网络的操作次数](https://leetcode-cn.com/problems/number-of-operations-to-make-network-connected/)

  ```java
  class UnionFind {
      int count;
      int[] id;
      int[] sz; 
      public UnionFind(int n) {
          count = n;
          id = new int[n];
          sz = new int[n];
        //! initialize the id[] by i and sz[] by 1
          for (int i = 0; i < n; i++) {
              id[i] = i;
              sz[i] = 1;
          }
      }
      
      public int find(int x) {
          while (x != id[x]) {
              id[x] = id[id[x]]; //compress path
              x = id[x];
          }
          return x;
      }
      
      public void union(int p, int q) {
          int rootP = find(p);
          int rootQ = find(q);
  
          if (rootP != rootQ) {
              if (sz[rootP] > sz[rootQ]) {id[rootQ] = rootP; sz[rootP]++;}
              else {id[rootP] = rootQ; sz[rootQ]++;}  
              count--;
          }
      }  
      
      public int count(){
          return count;
      }
  }
  
  class Solution {
      public int makeConnected(int n, int[][] connections) {
          if (connections.length < n - 1) return -1;
          UnionFind uf = new UnionFind(n);
          for (int[] connection : connections) {
              uf.union(connection[0], connection[1]);
          }
          int count = uf.count();
          return count - 1;
      }
  }
  
  //需要动的边的数目就是 component 的数量 - 1
  // 先判断 connections number is larger than the  ()number of computers - 1)
  ```

  

- [684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/)

```java
class UnionFind {
    int count;
    int[] id;
    int[] sz; 
    public UnionFind(int n) {
        count = n;
        id = new int[n];
        sz = new int[n];
      //! initialize the id[] by i and sz[] by 1
        for (int i = 0; i < n; i++) {
            id[i] = i;
            sz[i] = 1;
        }
    }
    
    public int find(int x) {
        while (x != id[x]) {
            id[x] = id[id[x]]; //compress path
            x = id[x];
        }
        return x;
    }
    
    public void union(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);

        if (rootP != rootQ) {
            if (sz[rootP] > sz[rootQ]) {id[rootQ] = rootP; sz[rootP]++;}
            else {id[rootP] = rootQ; sz[rootQ]++;}  
            count--;
        }
    }  
    
    public int count(){
        return count;
    }
}

class Solution {
    public int makeConnected(int n, int[][] connections) {
        if (connections.length < n - 1) return -1;
        UnionFind uf = new UnionFind(n);
        for (int[] connection : connections) {
            uf.union(connection[0], connection[1]);
        }
        int count = uf.count();
        return count - 1;
    }
}
```

