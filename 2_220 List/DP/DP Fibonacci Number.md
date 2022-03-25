
# Fibonacci Number
## Intro
https://leetcode.com/problems/fibonacci-number/
![[Pasted image 20220321133231.png]]

## Thoughts
- Pretty easy

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^n), SC: O(n)
    public int fib_BruteForceRecursive(int n) {
        if (n <= 1) return n;
        return fib_BruteForceRecursive(n - 1) + fib_BruteForceRecursive(n - 2);
    }
```

### #2 Top Down Memo
```java
    // TC: O(n), SC: O(n)
    public int fib_TopDownMemo(int n) {
        return helper_topDownMemo(n, new int[n + 1]);
    }

    public int helper_topDownMemo(int n, int[] dp) {
        if (n <= 1) return n;
        if (dp[n] != 0) return dp[n];
        return dp[n] = helper_topDownMemo(n - 1, dp) + helper_topDownMemo(n - 2, dp);
    }
```

### #3 DP 1D
```java
    // TC: O(n), SC: O(n)
    public int fib_DP_BottomUP_1D(int n) {
        if (n <= 1) return n;

        int[] dp = new int[n + 1];
        dp[0] = 0;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) dp[i] = dp[i - 2] + dp[i - 1];
        return dp[n];
    }
```

### #4 DP No Space
```java
    // TC: O(n), SC: O(1)
    public int fib_DP_BottomUP_NoSpace(int n) {
        if (n < 2) return n;

        int a = 0, b = 1;
        for (int i = 2; i <= n; i++) {
            int c = a + b;
            a = b;
            b = c;
        }
        return b;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Min Cost Climbing Stairs
## Intro
https://leetcode.com/problems/min-cost-climbing-stairs/
![[Pasted image 20220321133528.png]]

## Thoughts
- Pretty easy

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^n), SC: O(n)
    public int minCostClimbingStairsBruteForceRecursive(int[] cost) {
        return minCostClimbingStairsBruteForceRecursive(cost, cost.length);
    }

    private int minCostClimbingStairsBruteForceRecursive(int[] cost, int n) {
        if (n <= 1) return 0;
        return min(
                cost[n - 1] + minCostClimbingStairsBruteForceRecursive(cost, n - 1),
                cost[n - 2] + minCostClimbingStairsBruteForceRecursive(cost, n - 2)
        );
    }
```

### #2 Top Down Memo
```java
    // TC: O(n), SC: O(n)
    public int minCostClimbingStairsMemo(int[] cost) {
        return minCostClimbingStairsMemo(cost, cost.length, new int[cost.length + 1]);
    }

    private int minCostClimbingStairsMemo(int[] cost, int n, int memo[]) {
        if (n <= 1) return 0;

        if (memo[n] == 0) {
            memo[n] = min(cost[n - 1] + minCostClimbingStairsMemo(cost, n - 1, memo), cost[n - 2] + minCostClimbingStairsMemo(cost, n - 2, memo));
        }

        return memo[n];
    }
```

### #3 DP 1D
```java
    // TC: O(n), SC: O(n)
    public int minCostClimbingStairsDPApproach(int[] cost) {
        int[] dp = new int[cost.length + 1];
        for (int i = 2; i < dp.length; i++) {
            dp[i] = min(cost[i - 1] + dp[i - 1], cost[i - 2] + dp[i - 2]);
        }
        return dp[cost.length];
    }
```

### #4 DP No Space
```java
    // TC: O(n), SC: O(1)
    public int minCostClimbingStairsDPLessSpace(int[] cost) {
        int arr[] = {0, 0, 0};
        for (int i = 2; i <= cost.length; i++) {
            arr[2] = min(cost[i - 1] + arr[1], cost[i - 2] + arr[0]);
            arr[0] = arr[1];
            arr[1] = arr[2];
        }
        return arr[2];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Number Factors
## Intro
![[Pasted image 20220321133557.png]]

## Thoughts
- Pretty easy

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(3^n), SC: O(n)
    public int CountWays_BruteForceRecursive(int n) {
        if (n == 0) return 1;
        if (n == 1) return 1;
        if (n == 2) return 1;
        if (n == 3) return 2;
        return CountWays_BruteForceRecursive(n - 1) + CountWays_BruteForceRecursive(n - 3) + CountWays_BruteForceRecursive(n - 4);
    }
```

### #2 Top Down Memo
```java
    // TC: O(n), SC: O(n)
    public int CountWays_TopDownMemo(int n) {
        return helper_topDownMemo(n, new int[n + 1]);
    }

    private int helper_topDownMemo(int n, int[] memo) {
        if (n == 0) return 1;
        if (n == 1) return 1;
        if (n == 2) return 1;
        if (n == 3) return 2;

        if (memo[n] != 0) return memo[n];
        return memo[n] = helper_topDownMemo(n - 1, memo) + helper_topDownMemo(n - 3, memo) + helper_topDownMemo(n - 4, memo);
    }
```

### #3 DP Bottom UP 1D
```java
    // TC: O(n), SC: O(n)
    public int CountWays_DP_BottomUP_1D(int n) {
        int[] dp = new int[n + 1];
        dp[0] = dp[1] = dp[2] = 1;
        dp[3] = 2;
        for (int i = 4; i <= n; i++) dp[i] = dp[i - 1] + dp[i - 3] + dp[i - 4];
        return dp[n];
    }
```

### #4 DP No Space
```java
    // TC: O(n), SC: O(1)
    public int CountWays_DP_BottomUP_NoSpace(int n) {
        int[] dp = {1, 1, 1, 2};
        for (int i = 4; i <= n; i++) {
            int temp = dp[0] + dp[1] + dp[3];
            dp[0] = dp[1];
            dp[1] = dp[2];
            dp[2] = dp[3];
            dp[3] = temp;
        }
        return dp[3];
    }
```

## Common Pitfalls to Avoid
- N/A

---


# Jump Game II
## Intro
https://leetcode.com/problems/jump-game-ii/
![[Pasted image 20220321133825.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(n^n), SC: O(n)??
    // ?? is this tc and sc right
    public int jump_BruteForceRecursive(int[] nums) {
        return helper_bruteForceRecursive(nums, 0);
    }

    private int helper_bruteForceRecursive(int[] jumps, int i) {
        if (i >= jumps.length - 1) return 0;

        int min = Integer.MAX_VALUE;
        for (int j = i + 1; j <= i + jumps[i] && j < jumps.length; j++) {
            min = min(min, helper_bruteForceRecursive(jumps, j));
        }
        return min == Integer.MAX_VALUE ? Integer.MAX_VALUE : 1 + min;
    }
```

### #2 Top Down Memo
```java
    // TC: O(n^2), SC: O(n)
    // ?? is this tc and sc right
    public int jump_TopDownMemo(int[] nums) {
        return helper_TopDownMemo(nums, 0, new Integer[nums.length + 1]);
    }

    private int helper_TopDownMemo(int[] jumps, int i, Integer[] memo) {
        if (i >= jumps.length - 1) return 0;
        if (memo[i] != null) return memo[i];

        int min = Integer.MAX_VALUE;
        for (int j = i + 1; j <= i + jumps[i] && j < jumps.length; j++) {
            min = min(min, helper_TopDownMemo(jumps, j, memo));
        }
        return memo[i] = min == Integer.MAX_VALUE ? Integer.MAX_VALUE : 1 + min;
    }
```

### #3 DP 1D
```java
    // TC: O(n^2), SC: O(n)
    public int jump_BottomUP_DP_1D(int[] nums) {
        int n = nums.length, dp[] = new int[nums.length + 1];
        dp[n - 1] = 0;
        for (int i = n - 2; i >= 0; i--) {
            int min = Integer.MAX_VALUE;
            for (int j = i + 1; j <= i + nums[i] && j < n; j++) min = min(min, dp[j]);
            dp[i] = min == Integer.MAX_VALUE ? Integer.MAX_VALUE : 1 + min;
        }
        return dp[0];
    }
```

### #4 BFS Approach
```java
    // TC: O(n), SC: O(1)
    // todo understand clearly how this works.
    // Check neetcode video for more clarification
    public int jump_BFSWay(int[] nums) {
        int res = 0, l = 0, r = 0;
        while (r < nums.length - 1) {
            int farthest = 0;
            for (int i = l; i <= r; i++) farthest = max(farthest, i + nums[i]);
            l = r + 1;
            r = farthest;
            res++;
        }
        return res;
    }
```

### #5 Greedy Approach
```java
    // todo understand how this works also
    // TC: O(n), SC: O(1)
    public int jump_Greedy_SingleLoop(int[] nums) {
        int res = 0, farthest = 0, currentJumpEnd = 0;
        for (int i = 0; i < nums.length - 1; i++) {
            farthest = max(farthest, i + nums[i]);
            if (i == currentJumpEnd) {
                res++;
                currentJumpEnd = farthest;
            }
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---