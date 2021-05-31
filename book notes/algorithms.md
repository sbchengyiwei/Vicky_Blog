## Syllubas

##### 1-1 Course Introduction 
##### 2-1 Dynamic Connectivity 

 ### 2-2 Quick Find

​	find is quick (all two level tree), but union needs linear time

### 2-3 Quick Union 

​	union is more quick, is tree height which is same as find()

### 2-4 Quick-Union Improvements 

1. Use weight to union : add a size array

2.  Compress path : Id[i] = id[id[i]] (decrease the find time to smaller than 5)

   [sourse code](https://algs4.cs.princeton.edu/15uf/WeightedQuickUnionByHeightUF.java.html)

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



### 2-5 Union-Find Applications 

​	Percolation (use a head root and bottom root to reduce the time complexity)

##### 3-1 Analysis of Algorithms Introduction 

##### 3-2 Observations 

##### 3-3 Mathematical Models

##### 3-4 Order-of-Growth Classifications

##### 3-5 Theory of Algorithms 

##### 3-6 Memory

##### 4-1 Stacks 

##### 4-2 Resizing Arrays

##### 4-3 Queues 

##### 4-4 Generics<T>

##### 4-5 Iterators 

### 4-6 Stack and Queue Applications

```java
/*Dijkstra’s Two-Stack Algorithm for Expression Evaluation
*/
public class Evaluate
{
  public static void main(String[] args)
  {
    Stack<String> ops = new Stack<String>();
    Stack<Double> vals = new Stack<Double>();
    while (!StdIn.isEmpty())
    { // Read token, push if operator.
      String s = StdIn.readString();
      if (s.equals("(")) ;
      else if (s.equals("+")) ops.push(s);
      else if (s.equals("-")) ops.push(s);
      else if (s.equals("*")) ops.push(s);
      else if (s.equals("/")) ops.push(s);
      else if (s.equals("sqrt")) ops.push(s);
      else if (s.equals(")"))
      { // Pop, evaluate, and push result if token is ")".
        String op = ops.pop();
        double v = vals.pop();
        if (op.equals("+")) v = vals.pop() + v;
        else if (op.equals("-")) v = vals.pop() - v;
        else if (op.equals("*")) v = vals.pop() * v;
        else if (op.equals("/")) v = vals.pop() / v;
        else if (op.equals("sqrt")) v = Math.sqrt(v);
        vals.push(v);
      } // Token not operator or paren: push double value.
      else vals.push(Double.parseDouble(s));
    }
    StdOut.println(vals.pop());
  }
}
```



### 5-1 Sorting Introduction 

```java
/*Implement compareTo
*/
public class Date implements Comparable<Date>
{
  private final int day;
  private final int month;
  private final int year;
  public Date(int d, int m, int y)
  { day = d; month = m; year = y; }
  public int day() { return day; }
  public int month() { return month; }
  public int year() { return year; }
  public int compareTo(Date that)
  {
    if (this.year > that.year ) return +1;
    if (this.year < that.year ) return -1;
    if (this.month > that.month) return +1;
    if (this.month < that.month) return -1;
    if (this.day > that.day ) return +1;
    if (this.day < that.day ) return -1;
    return 0;
  }
  public String toString()
  { return month + "/" + day + "/" + year; }
}
```



### 5-2 Selection Sort 

[source code](https://algs4.cs.princeton.edu/21elementary/Selection.java.html)

```java
public class Selection
{
  public static void sort(Comparable[] a)
  { // Sort a[] into increasing order.
    int N = a.length; // array length
    for (int i = 0; i < N; i++)
    { // Exchange a[i] with smallest entry in a[i+1...N).
    	int min = i; // index of minimal entr.
   	 	for (int j = i+1; j < N; j++)
    		if (less(a[j], a[min])) min = j;
    	exch(a, i, min);
    }
  }
  // See page 245 for less(), exch(), isSorted(), and main().
}
```



### 5-3 Insertion Sort

[source code](https://algs4.cs.princeton.edu/21elementary/Insertion.java.html)

```java
public class Insertion
{
  public static void sort(Comparable[] a)
  { // Sort a[] into increasing order.
  	int N = a.length;
  	for (int i = 1; i < N; i++)
  	{ // Insert a[i] among a[i-1], a[i-2], a[i-3]... ..
  	for (int j = i; j > 0 && less(a[j], a[j-1]); j--)
  		exch(a, j, j-1);
  	}
  }
  // See page 245 for less(), exch(), isSorted(), and main().
}
```



### 5-4 Shellsort 

[source code](https://algs4.cs.princeton.edu/21elementary/Shell.java.html)

```java
public class Shell
{
  // time : O(N^4/3)  / O(n(log(n))^2) math proved
  public static void sort(Comparable[] a)
  { // Sort a[] into increasing order.
    int N = a.length;
    int h = 1;
    while (h < N/3) h = 3*h + 1; // 1, 4, 13, 40, 121, 364, 1093, ...increment sequence
      while (h >= 1)
      { // h-sort the array.
      for (int i = h; i < N; i++)
      { // Insert a[i] among a[i-h], a[i-2*h], a[i-3*h]... .
        for (int j = i; j >= h && less(a[j], a[j-h]); j -= h)
        	exch(a, j, j-h);
      }
      h = h/3;
    }
  }
  // See page 245 for less(), exch(), isSorted(), and main().
}
```

### 5-5 Shuffling

​	**Ramdom**

```
The position of every item is random and the probability is 1/n. So if the sequence is increasing one by one, we just need to calculate the probability of the position not be replaced.

Therefore:
1. Why shuffle just select the number before the iterator i?

   1* ½ * ⅔**¾*... = 1/n 

2. How Reservoir Sampling realize the m/n?

   m/m+1 * m+1/m+2 * …. * n-1/n
```

### 5-6 Convex Hull

​	**Graham Scan** : see if there is a counterclockwise turn.

​	![img](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20210322131254/Untitled-Diagram-15.png)

​	**How to compute Orientation?**

​	The idea is to use slope.  

​	![img](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20210322131209/1147.png)

```
Slope of line segment (p1, p2): σ = (y2 - y1)/(x2 - x1)
Slope of line segment (p2, p3): τ = (y3 - y2)/(x3 - x2)
If σ > τ, the orientation is clockwise (right turn)
Using above values of σ and τ, we can conclude that, 
the orientation depends on sign of below expression: 
(y2 - y1)*(x3 - x2) - (y3 - y2)*(x2 - x1)
Above expression is negative when σ < τ, i.e., counterclockwise	
```



### 6-1 Mergesort

[source code](https://algs4.cs.princeton.edu/22mergesort/Merge.java.html)

```java
// ! time : O(nlogn) = log1 + log2 +...+logn = log(n!) = nlogn
public static int[] indexSort(Comparable[] a) {
    int n = a.length;
    int[] index = new int[n];
    for (int i = 0; i < n; i++)
      index[i] = i;

    int[] aux = new int[n];
    sort(a, index, aux, 0, n-1);
    return index;
}

// mergesort a[lo..hi] using auxiliary array aux[lo..hi]
private static void sort(Comparable[] a, int[] index, int[] aux, int lo, int hi) {
    if (hi <= lo) return;
    int mid = lo + (hi - lo) / 2;
    sort(a, index, aux, lo, mid);
    sort(a, index, aux, mid + 1, hi);
    merge(a, index, aux, lo, mid, hi);
}

private static void merge(Comparable[] a, int[] index, int[] aux, int lo, int mid, int hi) {
    // copy to aux[]
    for (int k = lo; k <= hi; k++) {
      aux[k] = index[k]; 
    }

    // merge back to a[]
    int i = lo, j = mid+1;
    for (int k = lo; k <= hi; k++) {
      if      (i > mid)                    index[k] = aux[j++];
      else if (j > hi)                     index[k] = aux[i++];
      else if (less(a[aux[j]], a[aux[i]])) index[k] = aux[j++];
      else                                 index[k] = aux[i++];
    }
}
```



##### 6-2 Bottom-up Mergesort 

##### 6-3 Sorting Complexity 

##### 6-4 Comparators

##### 6 - 5 - Stability

### 7 - 1 - Quicksort

[source code](https://algs4.cs.princeton.edu/23quicksort/Quick.java.html)

```java
public static void sort(Comparable[] a) {
  StdRandom.shuffle(a);
  sort(a, 0, a.length - 1);
}

// quicksort the subarray from a[lo] to a[hi]
private static void sort(Comparable[] a, int lo, int hi) { 
  if (hi <= lo) return;
  int j = partition(a, lo, hi);
  sort(a, lo, j-1);
  sort(a, j+1, hi);
}

// and return the index j.
private static int partition(Comparable[] a, int lo, int hi) {
  int i = lo;
  int j = hi + 1;
  Comparable v = a[lo];
  while (i <= j) {
    if (nums[i] > pivot && nums[j] < pivot) {
      exch(a, i, j);
    }
    if (nums[i] <= pivot) l++;  // increasing order
    if (nums[r] >= pivot) r--;
  }
  // put partitioning item v at a[j]
  exch(a, lo, j);
  // now, a[lo .. j-1] <= a[j] <= a[j+1 .. hi]
  return j;
}

```

### 7 - 2 - Selection

```java
public static Comparable select(Comparable[] a, int k) {
  if (k < 0 || k >= a.length) {
    throw new IllegalArgumentException("index is not between 0 and " + a.length + ": " + k);
  }
  StdRandom.shuffle(a);
  int lo = 0, hi = a.length - 1;
  while (hi > lo) {
    int i = partition(a, lo, hi);  // previous chapter
    if      (i > k) hi = i - 1;
    else if (i < k) lo = i + 1;
    else return a[i];
  }
  return a[lo];
}

private static int partition(Comparable[] a, int lo, int hi) {
  int i = lo;
  int j = hi + 1;
  Comparable v = a[lo];
  while (i <= j) {
    if (nums[i] > pivot && nums[j] < pivot) {
      exch(a, i, j);
    }
    if (nums[i] <= pivot) l++;  // increasing order
    if (nums[r] >= pivot) r--;
  }
  // put partitioning item v at a[j]
  exch(a, lo, j);
  // now, a[lo .. j-1] <= a[j] <= a[j+1 .. hi]
  return j;
}
```



### 7 - 3 - Duplicate Keys 

 ```java
 // if there are many duplicate KEYs
 public static void sort(Comparable[] a) {
 	StdRandom.shuffle(a);  //
 	sort(a, 0, a.length - 1);
 }
 
 // quicksort the subarray a[lo .. hi] using 3-way partitioning
 private static void sort(Comparable[] a, int lo, int hi) { 
   if (hi <= lo) return;
   int lt = lo, gt = hi;
   Comparable v = a[lo];
   int i = lo + 1;
   while (i <= gt) {
     int cmp = a[i].compareTo(v);
     if      (cmp < 0) exch(a, lt++, i++);
     else if (cmp > 0) exch(a, i, gt--);
     else              i++;
   }
 
   // a[lo..lt-1] < v = a[lt..gt] < a[gt+1..hi]. 
   sort(a, lo, lt-1);
   sort(a, gt+1, hi);
 }	
 ```



### 7 - 4 - System Sorts 

Use quicksort for primitive elements, use mergesort for object (more space)

##### 8 - 1 - APIs and Elementary Implementations

### 8 - 2 - Binary Heaps

**Binary heap properties:**

- Largest key is a[1], which is root of binary tree.

- Can use array indices to move through tree.

- Parent of node at k is at k/2.

- Children of node at k are at 2k and 2k+1.

### 8 - 3 - Heapsort



**summery:**



##### 8 - 4 - Event-Driven Simulation 

##### 9 - 1 - Symbol Table API 

##### 9 - 2 - Elementary Implementations

##### 9 - 3 - Ordered Operations 

##### 9 - 4 - Binary Search Trees

##### 9 - 5 - Ordered Operations in BSTs 

##### 9 - 6 - Deletion in BSTs

##### 10 - 1 - 2-3 Search Trees 

##### 10 - 2 - Red-Black BSTs 

##### 10 - 3 - B-Trees 

##### 11 - 1 - 1d Range Search 

##### 11 - 2 - Line Segment Intersection 

##### 11 - 3 - Kd-Trees 

##### 11 - 4 - Interval Search Trees 

##### 11 - 5 - Rectangle Intersection 

##### 12 - 1 - Hash Functions 

##### 12 - 2 - Separate Chaining 

##### 12 - 3 - Linear Probing

##### 12 - 4 - Hash Table Context 

##### 12 - 5 - Symbol Table Applications Sets 

##### 12 - 6 - Symbol Table Applications Dictionary Clients 

##### 12 - 7 - Symbol Table Applications Indexing Clients 

##### 12 - 8 - Symbol Table Applications Sparse Vectors 