



## 1 Definations

> A [*disjoint-set data structure*](http://en.wikipedia.org/wiki/Disjoint-set_data_structure) is a data structure that keeps track of a set of elements partitioned into a number of disjoint (non-overlapping) subsets. A [*union-find algorithm*](http://en.wikipedia.org/wiki/Disjoint-set_data_structure) is an algorithm that performs two useful operations on such a data structure:
> ***Find:*** Determine which subset a particular element is in. This can be used for determining if two elements are in the same subset.
> ***Union:*** Join two subsets into a single subset.

**Union-Find API**

 The following API encapsulates the basic operations that we need.

![Union-find API](https://algs4.cs.princeton.edu/15uf/images/uf-api.png)



## 2 Implementations

1. Quick Find

    ![Quick find overview](https://algs4.cs.princeton.edu/15uf/images/quick-find-overview.png)

2. Quick Union

   ![Quick union overview](https://algs4.cs.princeton.edu/15uf/images/quick-union-overview.png)

3. Weighted quick-union

   ![Weighted quick union overview](https://algs4.cs.princeton.edu/15uf/images/weighted-quick-union-overview.png)

4. Weighted quick-union with path compression.

   > There are a number of easy ways to improve the weighted quick-union algorithm further. Ideally, we would like every node to link directly to the root of its tree, but we do not want to pay the price of changing a large number of links. We can approach the ideal simply by making all the nodes that we do examine directly link to the root.

   ```java
   public WeightedQuickUnionPathCompressionUF(int n) {
           count = n;
           parent = new int[n];
           size = new int[n];
           for (int i = 0; i < n; i++) {
               parent[i] = i;
               size[i] = 1;
           }
       }
   
       public int count() {
           return count;
       }
     
       public int find(int p) {
           validate(p);
           int root = p;
           while (root != parent[root])
               root = parent[root];
           while (p != root) {
               int newp = parent[p];
               parent[p] = root;
               p = newp;
           }
           return root;
       }
   
       public boolean connected(int p, int q) {
           return find(p) == find(q);
       }
   
       private void validate(int p) {
           int n = parent.length;
           if (p < 0 || p >= n) {
               throw new IllegalArgumentException("index " + p + " is not between 0 and " + (n-1));  
           }
       }  
   
       public void union(int p, int q) {
           int rootP = find(p);
           int rootQ = find(q);
           if (rootP == rootQ) return;
   
           // make smaller root point to larger one
           if (size[rootP] < size[rootQ]) {
               parent[rootP] = rootQ;
               size[rootQ] += size[rootP];
           }
           else {
               parent[rootQ] = rootP;
               size[rootP] += size[rootQ];
           }
           count--;
       }
   
   /*
                             constructor    union  find 
   
   quick-find                    N             N     1
   
   quick-union                   N      tree height tree height
   
   weighted quick-union          N           lg N   lg N
   
   path compresson               N            <5     <5
   */
   ```

   

## 3 Use cases

1. Keep track of connectivity of each element in a particular subset or connectivity of subsets with each other. (examples in next section)

2. Check whether a given (undirected) graph contains a cycle or not.

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



## 4 LeetCode Quesions

###### 

1. [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)

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
- [547. 省份数量](https://leetcode-cn.com/problems/number-of-provinces/)
- [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)
- [1631. 最小体力消耗路径](https://leetcode-cn.com/problems/path-with-minimum-effort/)
- [399. 除法求值](https://leetcode-cn.com/problems/evaluate-division/)
- [1319. 连通网络的操作次数](https://leetcode-cn.com/problems/number-of-operations-to-make-network-connected/)
- [684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/)

try

![]()
