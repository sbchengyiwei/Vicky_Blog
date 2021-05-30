## Syllubas

##### 1-1 Course Introduction 
##### 2-1 Dynamic Connectivity 

 ##### 2-2 Quick Find
##### 2-3 Quick Union 
##### 2-4 Quick-Union Improvements 

##### 2-5 Union-Find Applications 

##### 3-1 Analysis of Algorithms Introduction 

##### 3-2 Observations 

##### 3-3 Mathematical Models

##### 3-4 Order-of-Growth Classifications

##### 3-5 Theory of Algorithms 

##### 3-6 Memory

##### 4-1 Stacks 

##### 4-2 Resizing Arrays

##### 4-3 Queues 

##### 4-4 Generics

##### 4-5 Iterators 

### 4-6 Stack and Queue Applications

### 5-1 Sorting Introduction 

### 5-2 Selection Sort 

##### 5-3 Insertion Sort

##### 5-4 Shellsort 

##### 5-5 Shuffling

##### 5-6 Convex Hull

##### 6-1 Mergesort

##### 6-2 Bottom-up Mergesort 

##### 6-3 Sorting Complexity 

##### 6-4 Comparators









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