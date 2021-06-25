#### FaceBook

#### [139. Word Break](https://leetcode-cn.com/problems/word-break/)

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if (s == null) return false;
        boolean[] dp = new boolean[s.length() + 1];
        dp[0] = true;
        for (int i = 1; i <= s.length(); i++) {
            for (int j = 0; j < i; j++) {
                dp[i] = dp[j] && wordDict.contains(s.substring(j, i));
                if (dp[i] == true) break;
            }
        }
        return dp[s.length()];
    }
}
```

#### [1269. Number of Ways to Stay in the Same Place After Some Steps](https://leetcode-cn.com/problems/number-of-ways-to-stay-in-the-same-place-after-some-steps/)

```java
//draw the grid and optimize space complexity
class Solution {
    public int numWays(int steps, int arrLen) {
        final int MODULO = 1000000007;
        int[][] dp = new int[2][arrLen];
        dp[0][0] = 1;
        for (int i = 1; i <= steps; i++) {
            for (int j = 0; j <= i && j < arrLen; j++) {
                dp[i % 2][j] = dp[(i - 1) % 2][j];
                if (j - 1 >= 0) dp[i % 2][j] = (dp[i % 2][j] + dp[(i - 1) % 2][j - 1]) % MODULO;
                if (j + 1 < arrLen) dp[i % 2][j] = (dp[i % 2][j] + dp[(i - 1) % 2][j + 1]) % MODULO;
            }
        }
        return dp[steps % 2][0];
    }
}
```

