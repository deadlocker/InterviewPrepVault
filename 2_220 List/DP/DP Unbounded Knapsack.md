
# Unbounded Knapsack(Grokking)
## Intro
![[Pasted image 20220321125015.png]]

## Thoughts
- Pretty easy

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^(n+c)), SC: O(n+c), n is no.of items and c is capacity
    public int solveKnapsack_BruteForceRecursive(int[] profits, int[] weights, int W) {
        return helper_bruteForceRecursive(profits, weights, W, profits.length - 1);
    }

    private int helper_bruteForceRecursive(int[] profits, int[] weights, int W, int i) {
        if (i < 0 || W <= 0) return 0;

        int includedProfit = W >= weights[i]
                ? profits[i] + helper_bruteForceRecursive(profits, weights, W - weights[i], i)
                : 0;
        int notIncludedProfit = helper_bruteForceRecursive(profits, weights, W, i - 1);
        return max(includedProfit, notIncludedProfit);
    }
```

### #2 Top Down Memo
```java
    // TC: O(NC), SC: O(NC + N), C is capacity, N = no.of items
    // Extra + N for SC coz of recursion stack
    public int solveKnapsack_TopDownMemo(int[] profits, int[] weights, int W) {
        return helper_topDownMemo(profits, weights, W, profits.length - 1, new Integer[W + 1][profits.length]);
    }

    private int helper_topDownMemo(int[] profits, int[] weights, int W, int i, Integer[][] memo) {
        if (i < 0 || W <= 0) return 0;
        if (memo[W][i] != null) return memo[W][i];

        int includedProfit = W >= weights[i]
                ? profits[i] + helper_topDownMemo(profits, weights, W - weights[i], i, memo)
                : 0;
        int notIncludedProfit = helper_topDownMemo(profits, weights, W, i - 1, memo);
        return memo[W][i] = max(includedProfit, notIncludedProfit);
    }
```

### #3 DP Bottom UP 2D
```java
    // TC: O(NC), SC: O(NC), C is capacity, N = no.of items
    public int solveKnapsack_DP_BottomUP_2D(int[] profits, int[] weights, int W) {
        int n = profits.length, dp[][] = new int[n][W + 1];

        for (int i = 0; i < n; i++) {
            for (int w = 1; w <= W; w++) {
                int includedProfit = w >= weights[i] ? profits[i] + dp[i][w - weights[i]] : 0;
                int notIncludedProfit = i >= 1 ? dp[i - 1][w] : 0;
                dp[i][w] = max(includedProfit, notIncludedProfit);
            }
        }
        return dp[n - 1][W];
    }
```

### #4 DP Bottom UP 1D
```java
    // TC: O(NC), SC: O(C)
    public int solveKnapsack_DP_BottomUP_1D_SingleArray(int[] profits, int[] weights, int W) {
        int n = profits.length, dp[] = new int[W + 1];

        for (int i = 0; i < n; i++) {
            for (int w = W; w >= 0; w--) {
                int includedProfit = w >= weights[i] ? profits[i] + dp[w - weights[i]] : 0;
                int notIncludedProfit = i >= 1 ? dp[w] : 0;
                dp[w] = max(includedProfit, notIncludedProfit);
            }
        }
        return dp[W];
    }

    // TC: O(NC), SC: O(2C)
    public int solveKnapsack_DP_BottomUP_1D(int[] profits, int[] weights, int W) {
        int n = profits.length, dp[][] = new int[2][W + 1];

        for (int i = 0; i < n; i++) {
            for (int w = 1; w <= W; w++) {
                int includedProfit = w >= weights[i] ? profits[i] + dp[i % 2][w - weights[i]] : 0;
                int notIncludedProfit = i >= 1 ? dp[(i - 1) % 2][w] : 0;
                dp[i % 2][w] = max(includedProfit, notIncludedProfit);
            }
        }
        return dp[(n - 1) % 2][W];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Rod Cutting
## Intro
![[Pasted image 20220321125222.png]]

## Thoughts
- Just follow the question

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^(n+c)), SC: O(n+c), n is lengths.length, c = L
    public int solveRodCutting_BruteForceRecursive(int[] lengths, int[] prices, int L) {
        return helper_bruteForceRecursive(lengths, prices, L, prices.length - 1);
    }

    private int helper_bruteForceRecursive(int[] lengths, int[] prices, int L, int i) {
        if (i < 0 || L <= 0) return 0;

        int includedCut = L >= lengths[i] ? prices[i] + helper_bruteForceRecursive(lengths, prices, L - lengths[i], i) : 0;
        int notIncludedCut = helper_bruteForceRecursive(lengths, prices, L, i - 1);
        return max(includedCut, notIncludedCut);
    }
```

### #2 Top Down Memo
```java
    // TC: O(NC), SC: O(NC + N)
    public int solveRodCutting_TopDownMemo(int[] lengths, int[] prices, int L) {
        return helper_TopDownMemo(lengths, prices, L, prices.length - 1, new Integer[prices.length][L + 1]);
    }

    private int helper_TopDownMemo(int[] lengths, int[] prices, int L, int i, Integer[][] memo) {
        if (i < 0 || L <= 0) return 0;

        if (memo[i][L] != null) return memo[i][L];

        int includedCut = L >= lengths[i] ? prices[i] + helper_TopDownMemo(lengths, prices, L - lengths[i], i, memo) : 0;
        int notIncludedCut = helper_TopDownMemo(lengths, prices, L, i - 1, memo);
        return memo[i][L] = max(includedCut, notIncludedCut);
    }
```

### #3 DP BottomUP 2D
```java
    // TC: O(NC), SC: O(NC)
    public int solveRodCutting_DP_BottomUP_2D(int[] lengths, int[] prices, int L) {
        int n = prices.length, dp[][] = new int[n][L + 1];
        for (int i = 0; i < n; i++) {
            for (int l = 0; l <= L; l++) {
                int includedCut = l >= lengths[i] ? prices[i] + dp[i][l - lengths[i]] : 0;
                int notIncludedCut = i >= 1 ? dp[i - 1][l] : 0;
                dp[i][l] = max(includedCut, notIncludedCut);
            }
        }
        return dp[n - 1][L];
    }
```

### #4 DP BottomUP 1D
```java
    // TC: O(NC), SC: O(2C)
    public int solveRodCutting_DP_BottomUP_1D(int[] lengths, int[] prices, int L) {
        int n = prices.length, dp[][] = new int[2][L + 1];
        for (int i = 0; i < n; i++) {
            for (int l = 0; l <= L; l++) {
                int includedCut = l >= lengths[i] ? prices[i] + dp[i % 2][l - lengths[i]] : 0;
                int notIncludedCut = i >= 1 ? dp[(i - 1) % 2][l] : 0;
                dp[i % 2][l] = max(includedCut, notIncludedCut);
            }
        }
        return dp[(n - 1) % 2][L];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Coin Change 2
## Intro
https://leetcode.com/problems/coin-change-2
![[Pasted image 20220321125513.png]]

## Thoughts
- Pretty easy

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^(C + T)), SC: O(C + T), C = coins.length, T = amount
    public int change_BruteForceRecursive(int amount, int[] coins) {
        return helper_bruteForceRecursive(coins, amount, coins.length - 1);
    }

    private int helper_bruteForceRecursive(int[] coins, int amount, int i) {
        if (amount == 0) return 1;
        if (i < 0) return 0;

        int includedWays = amount >= coins[i] ? helper_bruteForceRecursive(coins, amount - coins[i], i) : 0;
        int notIncludedWays = helper_bruteForceRecursive(coins, amount, i - 1);
        return includedWays + notIncludedWays;
    }
```

### #2 Top Down Memo
```java
    // TC: O(CT), SC: O(CT)
    public int change_TopDownMemo(int amount, int[] coins) {
        int n = coins.length;
        return helper_topDownMemo(coins, amount, n - 1, new Integer[n][amount + 1]);
    }

    private int helper_topDownMemo(int[] coins, int amount, int i, Integer[][] memo) {
        if (amount == 0) return 1;
        if (i < 0) return 0;

        if (memo[i][amount] != null) return memo[i][amount];

        int includedWays = amount >= coins[i] ? helper_topDownMemo(coins, amount - coins[i], i, memo) : 0;
        int notIncludedWays = helper_topDownMemo(coins, amount, i - 1, memo);
        return memo[i][amount] = includedWays + notIncludedWays;
    }
```

### #3 DP 2D
```java
    // TC: O(CT), SC: O(CT)
    public int change_DP_BottomUP_2D(int amount, int[] coins) {
        int n = coins.length, dp[][] = new int[n][amount + 1];
        for (int i = 0; i < n; i++) dp[i][0] = 1;

        for (int i = 0; i < n; i++) {
            for (int t = 1; t <= amount; t++) {
                int includedWays = t >= coins[i] ? dp[i][t - coins[i]] : 0;
                int notIncludedWays = i >= 1 ? dp[i - 1][t] : 0;
                dp[i][t] = includedWays + notIncludedWays;
            }
        }
        return dp[n - 1][amount];
    }
```

### #4 DP 1D
```java
    // TC: O(CT), SC: O(T)
    public int countChange_DP_BottomUP_1D_SingleArray_Simpler(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;

        for (int coin : coins) {
            for (int t = coin; t <= amount; t++) dp[t] += dp[t - coin];
        }
        return dp[amount];
    }

    // TC: O(CT), SC: O(T)
    public int countChange_DP_BottomUP_1D_SingleArray(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;

        for (int coin : coins) {
            for (int t = coin; t <= amount; t++) {
                int includedWays = dp[t - coin];
                int notIncludedWays = dp[t];
                dp[t] = includedWays + notIncludedWays;
            }
        }
        return dp[amount];
    }

    // TC: O(CT), SC: O(2T)
    public int change_DP_BottomUP_1D(int amount, int[] coins) {
        int n = coins.length, dp[][] = new int[2][amount + 1];
        for (int i = 0; i < n; i++) dp[i % 2][0] = 1;

        for (int i = 0; i < n; i++) {
            for (int t = 1; t <= amount; t++) {
                int includedWays = t >= coins[i] ? dp[i % 2][t - coins[i]] : 0;
                int notIncludedWays = i >= 1 ? dp[(i - 1) % 2][t] : 0;
                dp[i % 2][t] = includedWays + notIncludedWays;
            }
        }
        return dp[(n - 1) % 2][amount];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Maximum Ribbon Cut(Grokking)
## Intro
![[Pasted image 20220321125719.png]]

## Thoughts
- Pretty Easy

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^(L+T)), SC: O(L+T), L = ribbonLengths.length, T = total
    public int countRibbonPieces_BruteForceRecursive(int[] ribbonLengths, int total) {
        int n = ribbonLengths.length;
        int res = helper_bruteForceRecursive(ribbonLengths, total, n - 1);
        return res == Integer.MIN_VALUE ? -1 : res;
    }

    private int helper_bruteForceRecursive(int[] ribbonLengths, int total, int i) {
        if (total == 0) return 0;
        if (i < 0) return Integer.MIN_VALUE;

        int included = Integer.MIN_VALUE;
        if (total >= ribbonLengths[i]) {
            int res = helper_bruteForceRecursive(ribbonLengths, total - ribbonLengths[i], i);
            if (res != Integer.MIN_VALUE) included = res + 1;
        }
        int notIncluded = helper_bruteForceRecursive(ribbonLengths, total, i - 1);
        return max(included, notIncluded);
    }
```

### #2 Top Down Memo
```java
    // TC: O(LT), SC: O(LT)
    public int countRibbonPieces_TopDownMemo(int[] ribbonLengths, int total) {
        int n = ribbonLengths.length;
        int res = helper_TopDownMemo(ribbonLengths, total, n - 1, new Integer[n][total + 1]);
        return res == Integer.MIN_VALUE ? -1 : res;
    }

    private int helper_TopDownMemo(int[] ribbonLengths, int total, int i, Integer[][] memo) {
        if (total == 0) return 0;
        if (i < 0) return Integer.MIN_VALUE;

        if (memo[i][total] != null) return memo[i][total];

        int included = Integer.MIN_VALUE;
        if (total >= ribbonLengths[i]) {
            int res = helper_TopDownMemo(ribbonLengths, total - ribbonLengths[i], i, memo);
            if (res != Integer.MIN_VALUE) included = res + 1;
        }
        int notIncluded = helper_TopDownMemo(ribbonLengths, total, i - 1, memo);
        return memo[i][total] = max(included, notIncluded);
    }
```

### #3 DP 2D
```java
    // TC: O(LT), SC: O(LT)
    public int countRibbonPieces_DP_BottomUP_2D(int[] ribbonLengths, int total) {
        int n = ribbonLengths.length, dp[][] = new int[n][total + 1];
        for (int t = 0; t <= total; t++)
            dp[0][t] = t % ribbonLengths[0] == 0 ? (t / ribbonLengths[0]) : Integer.MIN_VALUE;

        for (int i = 1; i < n; i++) {
            for (int t = 1; t <= total; t++) {
                int included = t >= ribbonLengths[i] && dp[i][t - ribbonLengths[i]] != Integer.MIN_VALUE
                        ? 1 + dp[i][t - ribbonLengths[i]]
                        : Integer.MIN_VALUE;
                int notIncluded = dp[i - 1][t];
                dp[i][t] = max(included, notIncluded);
            }
        }
        return dp[n - 1][total] == Integer.MIN_VALUE ? -1 : dp[n - 1][total];
    }
```

### #4 DP 1D
```java
    // TC: O(LT), SC: O(T)
    public int countRibbonPieces_DP_BottomUP_1D_SingleArray(int[] ribbonLengths, int total) {
        int n = ribbonLengths.length, dp[] = new int[total + 1];
        for (int t = 0; t <= total; t++) dp[t] = t % ribbonLengths[0] == 0 ? (t / ribbonLengths[0]) : Integer.MIN_VALUE;

        for (int i = 1; i < n; i++) {
            for (int t = total; t >= 0; t--) {
                int included = t >= ribbonLengths[i] && dp[t - ribbonLengths[i]] != Integer.MIN_VALUE
                        ? 1 + dp[t - ribbonLengths[i]]
                        : Integer.MIN_VALUE;
                int notIncluded = dp[t];
                dp[t] = max(included, notIncluded);
            }
        }
        return dp[total] == Integer.MIN_VALUE ? -1 : dp[total];
    }

    // TC: O(LT), SC: O(2T)
    public int countRibbonPieces_DP_BottomUP_1D(int[] ribbonLengths, int total) {
        int n = ribbonLengths.length, dp[][] = new int[2][total + 1];
        for (int t = 0; t <= total; t++)
            dp[0][t] = t % ribbonLengths[0] == 0 ? (t / ribbonLengths[0]) : Integer.MIN_VALUE;

        for (int i = 1; i < n; i++) {
            for (int t = 1; t <= total; t++) {
                int included = t >= ribbonLengths[i] && dp[i % 2][t - ribbonLengths[i]] != Integer.MIN_VALUE
                        ? 1 + dp[i % 2][t - ribbonLengths[i]]
                        : Integer.MIN_VALUE;
                int notIncluded = dp[(i - 1) % 2][t];
                dp[i % 2][t] = max(included, notIncluded);
            }
        }
        return dp[(n - 1) % 2][total] == Integer.MIN_VALUE ? -1 : dp[(n - 1) % 2][total];
    }
```

## Common Pitfalls to Avoid
- N/A

---