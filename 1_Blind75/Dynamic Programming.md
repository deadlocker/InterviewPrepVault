# Dynamic Programming

# [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled.png)

## Thoughts

- Whenever someone asks no.of ways or min/max/optimization of: then at least think about DP in the back of your mind.

## Solutions

### #1 Inefficient Recursion

```java
// TC: O(2^n), SC: O(n)
public int inefficientRecursion(int n) {
    if (n <= 2) return n;
    return inefficientRecursion(n - 1) + inefficientRecursion(n - 2);
}
```

### #2 Top Down Memo

```java
// TC: O(n), SC: O(n)
public int climbStairsMemo(int n) {
    return helper(n, new int[n + 1]);
}

private int helper(int n, int[] memo) {
    if (n <= 2) memo[n] = n;
    if (memo[n] == 0) memo[n] = helper(n - 1, memo) + helper(n - 2, memo);
    return memo[n];
}
```

### #3 Bottom UP DP

```java
// TC: O(n), SC: O(n)
public int climbStairsDP(int n) {
    if (n <= 2) return n;
    int[] dp = new int[n + 1];
    dp[1] = 1;
    dp[2] = 2;
    for (int i = 3; i <= n; i++) dp[i] = dp[i - 1] + dp[i - 2];
    return dp[n];
}
```

### #4 DP Space Optimized

```java
// TC: O(n), SC: O(1), Same as fibonacci
public int climbStairsNoSpace(int n) {
    if (n <= 2) return n;
    int arr[] = {1, 2, 0};
    for (int i = 3; i <= n; i++) {
        arr[2] = arr[1] + arr[0];
        arr[0] = arr[1];
        arr[1] = arr[2];
    }
    return arr[2];
}
```

### #5 Binet’s formula(not needed)

```java
// TC: O(logn), SC: O(1), Math.pow takes logn time
public int binetsFormula(int n) {
    double sqrt5 = sqrt(5);
    double fibn = pow((1 + sqrt5) / 2, n + 1) - pow((1 - sqrt5) / 2, n + 1);
    return (int) (fibn / sqrt5);
}
```

## Common Pitfalls to Avoid

- Handle basecases properly for DP questions.
- Also be careful with n vs n + 1(off by one errors).
- Also be careful with how your update operation runs(for loop forwards or backwards etc)

# [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%201.png)

## Thoughts

- Bruteforce is pretty straight forward.
- But how does one think of dp solution? Definitely the inner for loop from brute force has to optimized somehow. Thats the natural thought process. But how does one go from there to whipping out the dp solution? Was unable to debug the thought process for this. Check with others if anyone has a solution. Hindsight possible explanation(naruto + sasuke example where they are already strong so no need to check if Sakura + Sasuke will be stronger than all combinations)

## Solutions

### #1 BruteForce

```java
// TC: O(n^2), SC: O(1)
public int maxSubArray_BetterBruteForce(int[] nums) {
    int max = Integer.MIN_VALUE;
    for (int start = 0; start < nums.length; start++) {
        int sum = 0;
        for (int end = start; end < nums.length; end++) {
            sum += nums[end];
            max = max(sum, max);
        }
    }
    return max;
}
```

### #2 DP Full Space

```java
// TC: O(n), SC: O(n)
public int maxSubArray_DP_FullSpace(int[] nums) {
    int dp[] = new int[nums.length];
    dp[0] = nums[0];
    for (int i = 1; i < nums.length; i++) {
        dp[i] = max(nums[i], dp[i - 1] + nums[i]);
    }
    return Arrays.stream(dp).max().getAsInt();
}
```

### #3 DP No Space

```java
// TC: O(n), SC: O(1)
public int maxSubArray_DP_NOSpace(int[] nums) {
    int maxEndingHere = nums[0], max = nums[0];
    for (int i = 1; i < nums.length; i++) {
        maxEndingHere = max(nums[i], maxEndingHere + nums[i]);
        max = max(max, maxEndingHere);
    }
    return max;
}

// TC: O(n), SC: O(1), Another way of naming variables
public int maxSubArray_DP_NOSpace(int[] A) {
    if(A.length == 0) return 0;
    int n = A.length, dp[] = new int[n], max = A[0], prev = A[0];
    for(int i = 1; i < n; i++) {
        prev = Math.max(A[i], A[i] + prev);
        max = Math.max(max, prev);
    }
    return max;
}
```

## Common Pitfalls to Avoid

- While returning in dp approach, the result is not dp[n - 1]. Its the maximum in the entire dp array. This is because dp[i] represents the largest sum of a subarray ending at ith index. Therefore the answer cannot(always) be dp[n - 1] because not always will the max subarray end at the last index(aka n - 1).

# [Best Time To Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%202.png)

## Thoughts

- The first thought that comes to mind is bruteForce where you will check pairwise profit(however j should come after i after all you cannot buy in future and sell in the past).
- Then the immediate thought process would be to remove the inner loop somehow. dp[i] is maxProfit for all i elements. dp[i] = max(dp[i-1], A[i] - min); Its saying that the max for i elements is either dp[i-1] or A[i] - minSoFar.

## Solutions

### #1 BruteForce

```java
// TC: O(n^2), SC: O(1)
public int maxProfit_BruteForce(int[] prices) {
    int max = 0;
    for (int i = 0; i < prices.length - 1; i++) {
        for (int j = i + 1; j < prices.length; j++) {
            int profit = prices[j] - prices[i];
            max = Math.max(max, profit);
        }
    }
    return max;
}
```

### #2 DP

```java
// TC: O(n), SC: O(n)
public int maxProfit_DP(int[] prices) {
    int[] dp = new int[prices.length];
    dp[0] = 0;
    int min = prices[0];
    for (int i = 1; i < prices.length; i++) {
        min = Math.min(min, prices[i]);
        dp[i] = Math.max(dp[i - 1], prices[i] - min);
    }
    return dp[prices.length - 1];
}
```

### #3 OnePass (DP Optimized)

```java
// TC: O(n), SC: O(1)
public int maxProfit_DP_NoSpace(int[] prices) {
    if (prices.length == 0) return 0;
    int min = prices[0], max = 0;
    for (int i = 1; i < prices.length; i++) {
        if (prices[i] >= min) max = Math.max(max, prices[i] - min);
        min = Math.min(min, prices[i]);
    }
    return max;
```

## Common Pitfalls to Avoid

- If you draw this as a graph as shown below, one might accidentally think to only return the highest rising slope, but thats wrong. In the pic below, highest rising slope is from x = 2 to x = 3, however the ans is not 4. It should be 5. That is we want the most minimum value and then a max value that **comes after that** and then we need to take **subtraction** of these two.
- Image:

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%203.png)

# [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%204.png)

## Thoughts

- Just based on question, you can see that you need all subsequences which can only be done by recursion.
- Once you explore this path, its evident that you need some sort of top down memoization and therefore bottom up dp as well.

## Solutions

### #1 BruteForce Recursion

```java
// TC: O(2^(m+n)), SC: O(m+n)
public int longestCommonSubsequenceRecursiveBruteForce(String s1, String s2) {
    return helper(s1, s2, s1.length(), s2.length());
}

private int helper(String s1, String s2, int i, int j) {
    if (i * j == 0) return 0;

    if (s1.charAt(i - 1) == s2.charAt(j - 1)) return 1 + helper(s1, s2, i - 1, j - 1);
    return max(helper(s1, s2, i, j - 1), helper(s1, s2, i - 1, j));
}
```

### #2 Top Down Memo

```java
// TC: O(mn), SC: O(mn)
public int longestCommonSubsequenceTopDownDP(String s1, String s2) {
    return helper(s1, s2, s1.length(), s2.length(), new int[s1.length() + 1][s2.length() + 1]);
}

private int helper(String s1, String s2, int i, int j, int[][] memo) {
    if (i * j == 0) return 0;

    if (memo[i][j] != 0) return memo[i][j];

    if (s1.charAt(i - 1) == s2.charAt(j - 1)) memo[i][j] = 1 + helper(s1, s2, i - 1, j - 1, memo);
    else memo[i][j] = max(helper(s1, s2, i, j - 1, memo), helper(s1, s2, i - 1, j, memo));
    return memo[i][j];
}
```

### #3 Dp Bottom UP 2D

```java
// TC: O(mn), SC: O(mn)
public int longestCommonSubsequenceBottomUPDP(String s1, String s2) {
    int[][] dp = new int[s1.length() + 1][s2.length() + 1];

    for (int i = 0; i < dp.length; i++) {
        for (int j = 0; j < dp[0].length; j++) {
            if (i == 0 || j == 0) dp[i][j] = 0;
            else if (s1.charAt(i - 1) == s2.charAt(j - 1)) dp[i][j] = 1 + dp[i - 1][j - 1];
            else dp[i][j] = max(dp[i][j - 1], dp[i - 1][j]);
        }
    }

    return dp[s1.length()][s2.length()];
}
```

### #4 DP Bottom UP 1D Space(Using %2)

```java
// TC: O(mn), SC: O(n)
public int longestCommonSubsequence_DP_BottomUP_Space_Optimized(String s1, String s2) {
    int[][] dp = new int[2][s2.length() + 1];
    for (int i = 1; i <= s1.length(); i++) {
        for (int j = 1; j <= s2.length(); j++) {
            dp[i % 2][j] = s1.charAt(i - 1) == s2.charAt(j - 1)
                    ? 1 + dp[(i - 1) % 2][j - 1]
                    : max(dp[(i - 1) % 2][j], dp[i % 2][j - 1]);
        }
    }
    return dp[s1.length() % 2][s2.length()];
}
```

### #5 DP Bottom UP 1D Single Array(very tricky)

```java
// TC: O(mn), SC: O(min(m,n))
public int longestCommonSubsequence_DP_SingleArray(String s1, String s2) {
    int m = s1.length(), n = s2.length();
    if (m == 0 || n == 0) return 0;
    // make sure s1 is smaller than s2, that way we only allocate smaller length dp array(which is m)
    if (n < m) return longestCommonSubsequence_DP_SingleArray(s2, s1);

    int[] dp = new int[m + 1];
    for (int i = 1; i < n + 1; i++) {
        int prevBest = 0;
        for (int j = 1; j < m + 1; j++) {
            int temp = dp[j];
            dp[j] = (s1.charAt(j - 1) == s2.charAt(i - 1)) ? 1 + prevBest : max(dp[j], dp[j - 1]);
            prevBest = temp;
        }
    }
    return dp[m];
}
```

## Common Pitfalls to Avoid

- N/A

# [Coin Change](https://leetcode.com/problems/coin-change/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%205.png)

## Thoughts

- Just represent it as a tree and then go from there. It will be a very easy recursive solution. From there topdown memo and bottom up dp and any space optimizations later on.

## Solutions

### #1 BruteForce Recursive

```java
// TC: O(S^n), SC: O(n), where S is the amount and n is the length of coins array
public int coinChangeRecursive(int[] coins, int amount) {
    return helper(coins, amount);
}

private int helper(int[] coins, int amount) {
    if (amount == 0) return 0;

    int min = Integer.MAX_VALUE;
    for (int coin : coins) {
        if (coin <= amount) {
            int nextVal = helper(coins, amount - coin);
            if (nextVal != -1)
                min = min(min, 1 + nextVal);
        }
    }
    if (min == Integer.MAX_VALUE) return -1;

    return min;
}
```

### #2 TopDown Memo

```java
// TC: O(S * n), SC: O(n), * n coz of the for loop on coins array
public int coinChangeTopDownMemo(int[] coins, int amount) {
    return helper(coins, amount, new int[amount + 1]);
}

private int helper(int[] coins, int amount, int[] memo) {
    if (amount == 0) return 0;

    if (memo[amount] != 0) return memo[amount];

    int min = Integer.MAX_VALUE;
    for (int coin : coins) {
        if (coin <= amount) {
            int res = helper(coins, amount - coin, memo);
            if (res != -1 && res < min)
                min = 1 + res;
        }
    }

    memo[amount] = min == Integer.MAX_VALUE ? -1 : min;
    return memo[amount];
}
```

### #3 DP BottomUP

```java
// TC: O(S * n), SC: O(S)
public int coinChangeIterativeBottomUp(int[] coins, int amount) {
    int[] dp = new int[amount + 1];
    Arrays.fill(dp, amount + 1);
    dp[0] = 0;
    for (int i = 1; i <= amount; i++) {
        for (int coin : coins) {
            if (coin <= i) dp[i] = min(dp[i], 1 + dp[i - coin]);
        }
    }
    return dp[amount] > amount ? -1 : dp[amount];
}

// TC: O(Sn), SC: O(Sn)
public int coinChange_DP_BottomUp_2D_AnotherWay(int[] coins, int total) {
    int n = coins.length, dp[][] = new int[n][total + 1];
    for (int t = 1; t <= total; t++) dp[0][t] = t % coins[0] == 0 ? (t / coins[0]) : Integer.MAX_VALUE;

    for (int i = 1; i < n; i++) {
        for (int t = 1; t <= total; t++) {
            int includedCoins = t >= coins[i] && dp[i][t - coins[i]] != Integer.MAX_VALUE
                    ? dp[i][t - coins[i]] + 1
                    : Integer.MAX_VALUE;
            int notIncludedCoins = dp[i - 1][t];
            dp[i][t] = min(includedCoins, notIncludedCoins);
        }
    }
    return dp[n - 1][total] == Integer.MAX_VALUE ? -1 : dp[n - 1][total];
}

// TC: O(Sn), SC: O(S*2)
public int coinChange_DP_BottomUP_1D_AnotherWay(int[] coins, int total) {
    int n = coins.length, dp[][] = new int[2][total + 1];
    for (int t = 1; t <= total; t++) dp[0][t] = t % coins[0] == 0 ? (t / coins[0]) : Integer.MAX_VALUE;

    for (int i = 1; i < n; i++) {
        for (int t = 1; t <= total; t++) {
            int includedCoins = t >= coins[i] && dp[i % 2][t - coins[i]] != Integer.MAX_VALUE
                    ? dp[i % 2][t - coins[i]] + 1
                    : Integer.MAX_VALUE;
            int notIncludedCoins = dp[(i - 1) % 2][t];
            dp[i % 2][t] = min(includedCoins, notIncludedCoins);
        }
    }
    return dp[(n - 1) % 2][total] == Integer.MAX_VALUE ? -1 : dp[(n - 1) % 2][total];
}
```

## Common Pitfalls to Avoid

- Be careful with Integer.MAX_VALUE. In bruteforce recursive(and others of course), make sure to return -1 if you are unable to find any better way to form the amount using the given coin denominations.

# [House Robber](https://leetcode.com/problems/house-robber/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%206.png)

## Thoughts

- Just formulate the dp based on the question description and you should be good to go.

## Solutions

### #1 BruteForce Recursive

```java
// TC: O(2^n), SC: O(n)
public int robBruteForceRecursive(int[] nums) {
    return robBruteForceRecursive(nums, nums.length - 1);
}

private int robBruteForceRecursive(int[] nums, int n) {
    if (n < 0) return 0;
    return max(nums[n] + robBruteForceRecursive(nums, n - 2), robBruteForceRecursive(nums, n - 1));
}
```

### #2 Top Down Memo

```java
Integer[] memo;

// Recursive Memo
// TC: O(n), SC: O(n)
public int robMemo(int[] nums) {
    memo = new Integer[nums.length + 1];
    return robMemo(nums, nums.length - 1);
}

private int robMemo(int[] nums, int n) {
    if (n < 0) return 0;
    if (memo[n] == null) memo[n] = max(nums[n] + robMemo(nums, n - 2), robMemo(nums, n - 1));
    return memo[n];
}
```

### #3 DP Bottom UP

```java
// TC: O(n), SC: O(n)
public int rob_DP_Iterative_BottomUP(int[] nums) {
    if (nums.length == 0) return 0;
    if (nums.length == 1) return nums[0];

    int[] dp = new int[nums.length];
    dp[0] = nums[0];
    dp[1] = max(nums[0], nums[1]);
    for (int i = 2; i < nums.length; i++) dp[i] = max(nums[i] + dp[i - 2], dp[i - 1]);
    return dp[nums.length - 1];
}
```

### #4 DP No Space

```java
// Iterative DP.
// TC: O(n), SC: O(1)
public int rob_DP_NoSpace_Better_Variables(int[] num) {
    int prevMax = 0, currMax = 0;
    for (int x : num) {
        int temp = currMax;
        currMax = max(prevMax + x, currMax);
        prevMax = temp;
    }
    return currMax;
}

// TC: O(n), SC: O(1)
public int rob_DP_NoSpace(int[] nums) {
    if (nums.length == 0) return 0;
    if (nums.length == 1) return nums[0];

    int arr[] = {nums[0], max(nums[0], nums[1])};
    for (int i = 2; i < nums.length; i++) {
        int c = max(nums[i] + arr[0], arr[1]);
        arr[0] = arr[1];
        arr[1] = c;
    }
    return arr[1];
}
```

## Common Pitfalls to Avoid

- N/A

# [House Robber II](https://leetcode.com/problems/house-robber-ii/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%207.png)

## Thoughts

- This is similar to House Robber except its cyclic. You can use HouseRobber 1 code itself, but call it two times.

## Solutions

### #1 Using HouseRobberI

```java
// TC: O(n), SC: O(1)
public int rob_TwoLoops(int[] nums) {
    if (nums.length == 0) return 0;
    if (nums.length == 1) return nums[0];
    if (nums.length == 2) return max(nums[0], nums[1]);

    return max(rob(nums, 0, nums.length - 2), rob(nums, 1, nums.length - 1));
}

private int rob(int[] nums, int start, int end) {
    int prevMax = 0, curMax = 0;
    for (int i = start; i <= end; i++) {
        int temp = curMax;
        curMax = max(curMax, prevMax + nums[i]);
        prevMax = temp;
    }
    return curMax;
}
```

### #2 Single Loop(using multiple variables)

```java
// TC: O(n), SC: O(1)
public int rob_SingleLoop(int[] nums) {
    if (nums.length == 0) return 0;
    if (nums.length == 1) return nums[0];
    if (nums.length == 2) return max(nums[0], nums[1]);

    int prevMax1 = 0, prevMax2 = 0, curMax1 = nums[0], curMax2 = nums[1];

    for (int i = 2; i < nums.length; i++) {
        int temp1 = curMax1;
        curMax1 = max(curMax1, prevMax1 + nums[i - 1]);
        prevMax1 = temp1;

        int temp2 = curMax2;
        curMax2 = max(curMax2, prevMax2 + nums[i]);
        prevMax2 = temp2;
    }

    return max(curMax1, curMax2);
}
```

## Common Pitfalls to Avoid

- In Single Loop approach, be careful with multiple variables and their order. It can get a bit tricky.

# [Unique Paths](https://leetcode.com/problems/unique-paths/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%208.png)

## Thoughts

- Just follow the problem description and you will arrive at dp solution

## Solutions

### #1 Recursive BruteForce

```java
// TC: O(2^(m + n)), SC: O(m + n)
public int uniquePaths_RecursiveBruteForce(int m, int n) {
    if (m == 0 || n == 0) return 0;
    if (m == 1 && n == 1) return 1;

    return uniquePaths_RecursiveBruteForce(m - 1, n) + uniquePaths_RecursiveBruteForce(m, n - 1);
}
```

### #2 TopDown Memo

```java
// TC: O(m*n), SC: O(m+n)
public int uniquePaths_DP_TopDown(int m, int n) {
    return helper(m, n, new int[m + 1][n + 1]);
}

private int helper(int m, int n, int memo[][]) {
    if (m == 0 || n == 0) return 0;
    if (m == 1 && n == 1) return 1;

    if (memo[m][n] != 0) return memo[m][n];
   
    memo[m][n] = helper(m - 1, n, memo) + helper(m, n - 1, memo);
    return memo[m][n];
}
```

### #3 DP Bottom UP

```java
// TC: O(m*n), SC: O(m*n)
public int uniquePaths_DP_BottomUP(int m, int n) {
    int dp[][] = new int[m + 1][n + 1];
    dp[1][1] = 1;
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            // skip i = 1 and j = 1 case because you will get dp[1][1] = 0 overriding our outside statement where we set dp[1][1] = 1
            if (i * j != 1) dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
        }
    }
    return dp[m][n];
}
```

## Common Pitfalls to Avoid

- N/A

# [Decode Ways](https://leetcode.com/problems/decode-ways/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%209.png)

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%2010.png)

## Thoughts

- Just follow the problem statement and you will arrive at dp solution.

## Solutions

### #1 TopDownMemo using HashMap

```java
Map<Integer, Integer> memo = new HashMap<>();

// TC: O(2^n), SC: O(n)
public int numDecodingsRecursive(String s) {
    return helper(s, 0);
}

private int helper(String s, int i) {
    // base cases
    if (i == s.length()) return 1;
    if (s.charAt(i) == '0') return 0;
    if (i == s.length() - 1) return 1;

    if (memo.containsKey(i)) return memo.get(i);

    int doubleDigitWays = Integer.parseInt(s.substring(i, i + 2)) <= 26 ? helper(s, i + 2) : 0;
    memo.put(i, helper(s, i + 1) + doubleDigitWays);

    return memo.get(i);
}
```

### #2 TopDown Memo Using Integer[]

```java
// TC: O(n), SC: O(n)
public int numDecodings_TopDownMemo(String s) {
    int n = s.length();
    return dfs(s, 0, new Integer[n + 1]);
}

private int dfs(String s, int i, Integer[] memo) {
    if (i == s.length()) return 1;

    if (memo[i] != null) return memo[i];

    char c = s.charAt(i);
    if (c == '0') return 0;

    int count = dfs(s, i + 1, memo);
    if (i + 1 < s.length()) {
        String next = s.substring(i, i + 2);
        int nextVal = Integer.parseInt(next);
        if (nextVal <= 26) count += dfs(s, i + 2, memo);
    }

    return memo[i] = count;
}
```

### #3 BottomUP DP

```java
// TC: O(n), SC: O(n)
public int numDecodingsIterative(String s) {
    if (s == null || s.length() == 0) return 0;

    int dp[] = new int[s.length() + 1];
    dp[0] = 1;
    dp[1] = s.charAt(0) == '0' ? 0 : 1;

    for (int i = 2; i < dp.length; i++) {
        if (s.charAt(i - 1) != '0') dp[i] += dp[i - 1];

        int twoDigit = Integer.parseInt(s.substring(i - 2, i));
        if (twoDigit >= 10 && twoDigit <= 26) dp[i] += dp[i - 2];
    }
    return dp[s.length()];
}
```

### #4 DP No Space

```java
// TC: O(n), SC: O(1)
public int numDecodingsDPNoSpace(String s) {
    if (s == null || s.length() == 0) return 0;

    int[] arr = {1, s.charAt(0) == '0' ? 0 : 1};

    for (int i = 2; i < s.length() + 1; i++) {
        int numberOfCurrentWays = 0;
        if (s.charAt(i - 1) != '0') numberOfCurrentWays += arr[1];

        int twoDigit = Integer.parseInt(s.substring(i - 2, i));
        if (twoDigit >= 10 && twoDigit <= 26) numberOfCurrentWays += arr[0];

        arr[0] = arr[1];
        arr[1] = numberOfCurrentWays;
    }
    return arr[1];
}
```

## Common Pitfalls to Avoid

- Consider inputs like “06” or “27”.
- Two conditions need to be checked before branching out. If S[i] = ‘0’ then release or return 0; If the two chars formed by substring give an int value greater than 27, then dont consider it.

# [Jump Game](https://leetcode.com/problems/jump-game/)

## Intro

![Untitled](1_Blind75/Dynamic%20Pr%20f6649/Untitled%2011.png)

## Thoughts

- DP is straight forward. But how to derive this?
- Next how to think of the greedy solution?

## Solutions

### #1 Recursive BruteForce

```java
// Check LC for why this is 2^n and not n^n
// TC: O(2^n), SC: O(n)
public boolean canJump_RecursiveBruteForce(int[] nums) {
    return helper_recursiveBruteForce(nums, 0);
}

private boolean helper_recursiveBruteForce(int[] nums, int i) {
    if (i == nums.length - 1) return true;

    for (int j = 1; j <= nums[i]; j++) {
        int index = i + j;
        if (index < nums.length && helper_recursiveBruteForce(nums, index))
            return true;
    }
    return false;
}
```

### #2 Top Down Memo

```java
// TC: O(n^2), SC: O(n)
public boolean canJump_DP_TopDown(int[] nums) {
    return helper_DP_TopDown(nums, 0, new Boolean[nums.length]);
}

private boolean helper_DP_TopDown(int[] nums, int i, Boolean[] memo) {
    if (i == nums.length - 1) return true;

    if (memo[i] != null) return memo[i];

    for (int j = 1; j <= nums[i]; j++) {
        int index = i + j;
        if (index < nums.length && helper_DP_TopDown(nums, index, memo)) {
            memo[i] = true;
            return memo[i];
        }
    }

    memo[i] = false;
    return memo[i];
}
```

### #3 DP Bottom UP

```java
// TC: O(n^2), SC: O(n)
public boolean canJump_DP_BottomUP(int[] nums) {
    if (nums.length == 1) return true;

    boolean[] dp = new boolean[nums.length];
    dp[0] = nums[0] != 0;
    for (int i = 0; i < nums.length; i++) {
        if (dp[i]) {
            for (int j = i + 1; j <= i + nums[i]; j++) if (j < dp.length) dp[j] = true;
        }
    }
    return dp[nums.length - 1];
}
```

### #4 Greedy No Space

```java
// TC: O(n), SC: O(1)
// Check LC explanation. really good.
// todo how to derive this?
public boolean canJump_Best_Greedy_DP_NoSpace(int[] nums) {
    int farthest = nums.length - 1;
    for (int i = nums.length - 1; i >= 0; i--) {
        if (i + nums[i] >= farthest) farthest = i;
    }
    return farthest == 0;
}
```

## Common Pitfalls to Avoid

- N/A

# [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)

## Intro

![Untitled](Untitled%2012.png)

## Thoughts

- This is a classic question. Use DP.
- Whenever palindrome exists, think about expandAroundCenter approach.

## Solutions

### #1 BruteForce

```java
// TC: O(n^3), SC: O(1)
public String longestPalindrome_BruteForce(String s) {
    int[] res = {0, 1};
    for (int start = 0; start < s.length(); start++) {
        for (int end = 0; end <= s.length(); end++) {
            if (isPalindrome(s, start, end) && res[1] - res[0] < end - start) {
                res[0] = start;
                res[1] = end;
            }
        }
    }
    return s.substring(res[0], res[1]);
}

private boolean isPalindrome(String s, int start, int end) {
    while (start < end) {
        if (s.charAt(start++) != s.charAt(end-- - 1)) return false;
    }
    return true;
}
```

### #2 DP BottomUP

```java
// TC: O(n^2), SC: O(n^2)
public String longestPalindrome_DP_BottomUP(String s) {
    if (s.length() <= 1) return s;

    boolean dp[][] = new boolean[s.length()][s.length()];

    for (int i = 0; i < dp.length; i++) dp[i][i] = true;

    int res[] = {0, 1};
    for (int i = s.length() - 1; i >= 0; i--) {
        for (int j = i + 1; j < s.length(); j++) {
            if (s.charAt(i) == s.charAt(j) && (j - i == 1 || dp[i + 1][j - 1])) {
                dp[i][j] = true;
                if (res[1] - res[0] < j - i + 1) {
                    res[0] = i;
                    res[1] = j + 1;
                }
            }
        }
    }
    return s.substring(res[0], res[1]);
}
```

### #3 Expand Around Center

```java
// TC: O(n^2), SC: O(1)
// TODO: relearn how this works
public String longestPalindrome_ExpandAroundCenter(String s) {
    if (s.length() <= 1) return s;

    int start = 0, end = 0;
    for (int i = 0; i < s.length(); i++) {
        int oddLen = eac(s, i, i);
        int evenLen = eac(s, i, i + 1);
        int len = max(oddLen, evenLen);
        if (len > end - start) {
            start = i - (len - 1) / 2;
            end = i + len / 2;
        }
    }
    return s.substring(start, end + 1);
}

private int eac(String s, int l, int r) {
    while (l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)) {
        l--;
        r++;
    }
    return r - l - 1;
}
```

### #4 Manachers Algorithm(not needed)

```java
// todo learn how this works
// TC: O(n), SC: O(n)
public String longestPalindrome_ManachersAlgorithm(String s) {

    int strLen = 2 * s.length() + 3;
    char[] sChars = new char[strLen];

    /*
     * To ignore special cases at the beginning and end of the array
     * "abc" -> @ # a # b # c # $
     * "" -> @#$
     * "a" -> @ # a # $
     */
    sChars[0] = '@';
    sChars[strLen - 1] = '$';
    int t = 1;
    for (char c : s.toCharArray()) {
        sChars[t++] = '#';
        sChars[t++] = c;
    }
    sChars[t] = '#';

    int maxLen = 0, start = 0, maxRight = 0, center = 0;
    int[] p = new int[strLen]; // i's radius, which not includes i
    for (int i = 1; i < strLen - 1; i++) {
        if (i < maxRight) {
            p[i] = Math.min(maxRight - i, p[2 * center - i]);
        }

        // expand center
        while (sChars[i + p[i] + 1] == sChars[i - p[i] - 1]) {
            p[i]++;
        }

        // update center and its bound
        if (i + p[i] > maxRight) {
            center = i;
            maxRight = i + p[i];
        }

        // update ans
        if (p[i] > maxLen) {
            start = (i - p[i] - 1) / 2;
            maxLen = p[i];
        }
    }

    return s.substring(start, start + maxLen);
}
```

## Common Pitfalls to Avoid

- N/A

# [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)

## Intro

![Untitled](Untitled%2013.png)

## Thoughts

- list

## Solutions

### #1 BruteForce

```java
// TC: O(n^3), SC: O(1)
public int countSubstrings_BruteForce(String s) {
    int count = 0;
    for (int start = 0; start < s.length(); start++) {
        for (int end = start + 1; end <= s.length(); end++) {
            if (isPalindrome(s, start, end)) count++;
        }
    }
    return count;
}

private boolean isPalindrome(String s, int start, int end) {
    while (start < end) {
        if (s.charAt(start++) != s.charAt(end-- - 1)) return false;
    }
    return true;
}
```

### #2 DP BottomUP

```java
// TC: O(n^2), SC: O(n^2)
public int countSubstrings_DP_AnotherWay(String s) {
    if (s.length() <= 1) return s.length();
    boolean dp[][] = new boolean[s.length()][s.length()];
    int count = 0;
    for (int i = 0; i < dp.length; i++) {
        dp[i][i] = true;
        count++;
    }

    for (int i = dp.length - 1; i >= 0; i--) {
        for (int j = i + 1; j < dp[i].length; j++) {
            if (j - i < 2) dp[i][j] = s.charAt(i) == s.charAt(j);
            else dp[i][j] = s.charAt(i) == s.charAt(j) && dp[i + 1][j - 1];
            if (dp[i][j]) count++;
        }
    }
    return count;
}

// TC: O(n^2), SC: O(n^2)
public int countSubstrings_DP_BottomUP(String s) {
    int n = s.length(), count = 0;
    if (n <= 0) return count;

    boolean dp[][] = new boolean[n][n];

    // base case: single letter substrings
    for (int i = 0; i < n; i++, count++) dp[i][i] = true;

    // base case: double letter substrings
    for (int i = 0; i < n - 1; i++) {
        dp[i][i + 1] = s.charAt(i) == s.charAt(i + 1);
        count += dp[i][i + 1] ? 1 : 0;
    }

    // all other cases: substrings of length 3 to n
    for (int len = 3; len <= n; len++) {
        for (int i = 0, j = i + len - 1; j < n; i++, j++) {
            dp[i][j] = dp[i + 1][j - 1] && s.charAt(i) == s.charAt(j);
            count += dp[i][j] ? 1 : 0;
        }
    }

    return count;
}
```

### #3 Expand Around Center

```java
// TC: O(n^2), SC: O(1)
public int countSubstrings_ExpandAroundCenter(String s) {
    int count = 0;
    for (int i = 0; i < s.length(); i++) {
        // odd length palindromes, single character center
        count += countPalindromesAroundCenter(s, i, i);

        // even length palindromes, consecutive characters center
        count += countPalindromesAroundCenter(s, i, i + 1);
    }
    return count;
}

private int countPalindromesAroundCenter(String s, int lo, int hi) {
    int count = 0;
    while (lo >= 0 && hi < s.length()) {
        if (s.charAt(lo--) != s.charAt(hi++)) break;
        count++;
    }
    return count;
}
```

## Common Pitfalls to Avoid

- N/A

# [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

## Intro

![Untitled](Untitled%2014.png)

## Thoughts

- DP approach is pretty straightforward.
- There is also some patience sort method which is the best known solution for this problem.

## Solutions

### #1 BruteForce

```java
// TC: O(2^n), SC: O(n)
public int lengthOfLISRecursiveBruteForce(int[] nums) {
    return helper(nums, Integer.MIN_VALUE, 0);
}

private int helper(int[] nums, int prev, int i) {
    if (i == nums.length) return 0;

    int taken = nums[i] > prev ? 1 + helper(nums, nums[i], i + 1) : 0;
    return max(taken, helper(nums, prev, i + 1));
}
```

### #2 Top Down Memo

```java
// TC: O(n^2), SC: O(n^2 + n)
public int lengthOfLIS_TopDown(int[] nums) {
    int n = nums.length;
    return helper(nums, -1, 0, new Integer[n][n]);
}

private int helper(int[] nums, int prevI, int i, Integer[][] memo) {
    if (i >= nums.length) return 0;

    if (memo[i][prevI + 1] != null) return memo[i][prevI + 1];

    int choose = (prevI == -1 || nums[i] > nums[prevI]) ? 1 + helper(nums, i, i + 1, memo) : 0;
    int notChoose = helper(nums, prevI, i + 1, memo);

    return memo[i][prevI + 1] = max(choose, notChoose);
}
```

### #3 DP Bottom UP

```java
// this has less for loops than the below dp 1d method, but same TC
// TC: O(n^2), SC: O(n)
public int lengthOfLIS_DP_BottomUP_1D_Micro_Optimized(int[] A) {
    int longest = 1, n = A.length, dp[] = new int[n];

    dp[0] = 1;
    for (int i = 1; i < n; i++) {
        dp[i] = 1;
        for (int j = 0; j < i; j++) {
            if (A[i] > A[j] && dp[i] <= dp[j]) {
                dp[i] = 1 + dp[j];
                longest = Math.max(longest, dp[i]);
            }
        }
    }

    return longest;
}

// TC: O(n^2), SC: O(n)
public int lengthOfLIS_DP_BottomUP_1D(int[] nums) {
    int dp[] = new int[nums.length], max = 1;
    Arrays.fill(dp, 1);
    for (int i = 0; i < dp.length; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[i] > nums[j]) dp[i] = max(dp[i], 1 + dp[j]);
            if (dp[i] > max) max = dp[i];
        }
    }
    return max;
}

// TC: O(n^2), SC: O(n^2)
public int lengthOfLIS_DP_BottomUP_2D(int[] nums) {
    int n = nums.length;
    if (n == 1) return 1;

    int[][] dp = new int[n][n];
    Arrays.fill(dp[0], 1);
    int longest = Integer.MIN_VALUE;
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (j <= i - 1 || nums[j] <= nums[i - 1]) dp[i][j] = dp[i - 1][j];
            else dp[i][j] = max(dp[i - 1][j], 1 + dp[i - 1][i - 1]);
            longest = max(longest, dp[i][j]);
        }
    }
    return longest;
}
```

### #4 Build Intelligent Sequence

```java
// TC: O(n^2), SC: O(n)
// todo relearn this and understand it better
public int lengthOfLIS_Build_Intelligent_Sequence(int[] nums) {
    List<Integer> sub = new ArrayList<>();
    sub.add(nums[0]);

    for (int i = 1; i < nums.length; i++) {
        if (nums[i] > sub.get(sub.size() - 1)) sub.add(nums[i]);
        else {
            // find the first element that is >= nums[i]
            int j = 0;
            while (sub.get(j) < nums[i]) j++;

            sub.set(j, nums[i]);
        }
    }

    return sub.size();
}
```

### #5 Binary Search

```java
// TC: O(nlogn), SC: O(n)
// TODO relearn this and understand it better
public int lengthOfLISBinarySearch(int[] nums) {
    int dp[] = new int[nums.length], len = 0;
    for (int num : nums) {
        int i = Arrays.binarySearch(dp, 0, len, num);
        if (i < 0) i = -(i + 1);
        dp[i] = num;
        if (i == len) len++;
    }
    return len;
}

// TC: O(nlogn), SC: O(n)
// todo relearn this and understand it better
public int lengthOfLIS_Build_Intelligent_Sequence_BinarySearch(int[] nums) {
    List<Integer> sub = new ArrayList<>();
    sub.add(nums[0]);

    for (int i = 1; i < nums.length; i++) {
        if (nums[i] > sub.get(sub.size() - 1)) sub.add(nums[i]);
        else {
            // find the first element that is >= nums[i]
            int j = Collections.binarySearch(sub, nums[i]);
            if (j < 0) j = -(j + 1);

            sub.set(j, nums[i]);
        }
    }

    return sub.size();
```

## Common Pitfalls to Avoid

- pitfall1

---

# Maximum Product Subarray
## Intro
https://leetcode.com/problems/maximum-product-subarray/
![[Pasted image 20220321044400.png]]

## Thoughts
- list

## Solutions
### #1 BruteForce Cubic

```java
    // TC: O(n^3), SC: O(1)
    public int maxProductBruteForceCubic(int[] nums) {
        int max = Integer.MIN_VALUE;
        for (int i = 0; i < nums.length; i++) {
            for (int j = i; j < nums.length; j++) {
                int product = Arrays.stream(nums, i, j + 1).reduce(1, (a, b) -> a * b);
                max = max(max, product);
            }
        }
        return max;
    }
```

### #2 BruteForce Quadratic
```java
    // TC: O(n^2), SC: O(1)
    public int maxProductQuadratic(int[] nums) {
        int max = Integer.MIN_VALUE;
        for (int i = 0; i < nums.length; i++) {
            int accumulator = 1;
            for (int j = i; j < nums.length; j++) {
                accumulator *= nums[j];
                max = max(max, accumulator);
            }
        }
        return max;
    }

```

### #3 DP Bottom UP No Space
```java
    // TC: O(n), SC: O(1)
    public int maxProductBottomUpDP(int[] nums) {
        if (nums.length == 0) return 0;

        int maxSoFar = nums[0], minSoFar = nums[0], res = maxSoFar;
        for (int i = 1; i < nums.length; i++) {
            int cur = nums[i], maxProd = maxSoFar * cur, minProd = minSoFar * cur;

            int tempMax = max(List.of(cur, maxProd, minProd));
            minSoFar = min(List.of(cur, maxProd, minProd));

            maxSoFar = tempMax;
            res = max(res, maxSoFar);
        }
        return res;
    }
```

### #4 DP Bottom UP Simple to Understand
```java
    // Check Leetcode explanation, its not bad
    // TC: O(n), SC: O(1)
    public int maxProduct_SimpleToUnderstand(int[] nums) {
        if (nums.length == 0) return 0;

        int maxSoFar = nums[0], minSoFar = nums[0], res = nums[0];

        for (int i = 1; i < nums.length; i++) {
            int cur = nums[i];

            int max = Math.max(cur, Math.max(cur * maxSoFar, cur * minSoFar));
            int min = Math.min(cur, Math.min(cur * maxSoFar, cur * minSoFar));


            maxSoFar = max;
            minSoFar = min;

            res = Math.max(res, maxSoFar);
        }

        return res;
    }
```
## Common Pitfalls to Avoid
- N/A

---