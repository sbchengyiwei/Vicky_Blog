

#### [78. Subsets](https://leetcode-cn.com/problems/subsets/)

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if(nums == null || nums.length == 0) return res;
        helper(nums, res, new ArrayList<>(), 0);
        return res;
    }

    private void helper(int[] nums, List<List<Integer>> res, List<Integer> list, int index) {
        
        res.add(new ArrayList<>(list));
        for (int i = index; i < nums.length; i++) {
            list.add(nums[i]);
            helper(nums, res, list, i + 1);
            list.remove(list.size() - 1);
        }
    }
}
```

