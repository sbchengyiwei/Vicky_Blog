**Reference**: https://zhuanlan.zhihu.com/p/135094687

**Questions**: 

- [207. Course Schedule](https://leetcode.com/problems/course-schedule/)  (same as the following question)

- [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)

  ```java
  class Solution {
      // time : O(e + v) ; space : O(e)
      public int[] findOrder(int numCourses, int[][] prerequisites) {
          // corner case : numCourses == 1 prerequisites =[] return [0]
          if (prerequisites == null || numCourses == 0) return new int[0]; 
          int[] indegrees = new int[numCourses]; // indegree tables of each node
          for (int[] course : prerequisites) {
              indegrees[course[0]]++;
          }
          Queue<Integer> queue = new ArrayDeque<>();  // bfs
          for (int i = 0; i < numCourses; i++) {
              if (indegrees[i] == 0) {     // entry is the 0-indegree node
                  queue.offer(i);
              }
          }
          int[] res = new int[numCourses];
          int count = 0;
          while (!queue.isEmpty()) {
              int pre = queue.poll();
              res[count++] = pre;
              for (int[] course : prerequisites) {
                  if (course[1] == pre) {
                      indegrees[course[0]]--;  // decrease the indegree of the connected nodes
                      if (indegrees[course[0]] == 0) queue.offer(course[0]);   // if indegree is 0, put into the table
                  }
              }
          }
          return count == numCourses ? res : new int[0];
          
      }
  }
  ```


- [127 · 拓扑排序](https://www.lintcode.com/problem/127/?_from=collection&fromId=178)

  ```java
  /**
   * Definition for Directed graph.
   * class DirectedGraphNode {
   *     int label;
   *     List<DirectedGraphNode> neighbors;
   *     DirectedGraphNode(int x) {
   *         label = x;
   *         neighbors = new ArrayList<DirectedGraphNode>();
   *     }
   * }
   */
  
  public class Solution {
      // time : O(e + v) ; space : O(e)
      public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
          
          ArrayList<DirectedGraphNode> res = new ArrayList();
  
          //Use map record the the number of each node's indgree
          Map<DirectedGraphNode, Integer> indegree = new HashMap();
          for (DirectedGraphNode node : graph) {
              for (DirectedGraphNode nei : node.neighbors) {
                  indegree.putIfAbsent(node, 0);
                  indegree.put(nei, indegree.getOrDefault(nei, 0) + 1);
              }
          }
          // BFS entry is the node with 0 indegree
          Queue<DirectedGraphNode> q = new LinkedList();
          for(DirectedGraphNode key : indegree.keySet()) {
              if(indegree.get(key) == 0) q.add(key);
          }
          
          while(!q.isEmpty()) {
              DirectedGraphNode node = q.poll();
              res.add(node);
              for(DirectedGraphNode nei : node.neighbors) {
                  indegree.put(nei, indegree.get(nei) - 1);
                  if(indegree.get(nei) == 0) q.add(nei);
              }
          }
          return res;
      }
  }
  
  
  ```

  

