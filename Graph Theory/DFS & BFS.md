## Content

graph 问题上关于 bfs dfs uf 的选择 ： 首先如果都能用就和面试官逐个分析一下，如果是特殊问题（比如island2里cell不断地增加)uf只需对付新增的部分即可对付这些问题就要用uf， 其次 bfs 和 dfs 的时空复杂度差不多 通常先用dfs一般代码简洁些速度略快  有的题dfs不行必须bfs 那再用 bfs


582. Kill Process
实现题：从暴力解到 DFS 和 BFS

```
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