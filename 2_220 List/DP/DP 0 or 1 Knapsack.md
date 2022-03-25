
# 0/1 Knapsack(Grokking)
## Intro
![[Pasted image 20220321103943.png]]

## Thoughts
- This is a classic DP question. Just think about each ith element and what it means to choose it or not. From there just follow the problem description and you will arrive at the brute force answer.
- From here, just memoize it and then follow optimizations

## Solutions
### #1 Brute Force Recursive

```java
    // TC: O(2^n), SC: O(n)
    public int solveKnapsack_Grokking_BruteForceRecursive(int[] profits, int[] weights, int W) {
        return this.knapsackRecursive_Grokking(profits, weights, W, 0);
    }

    private int knapsackRecursive_Grokking(int[] profits, int[] weights, int W, int i) {
        // base checks
        if (W <= 0 || i >= profits.length) return 0;

        // recursive call after choosing the element at the i
        // if the weight of the element at i exceeds the W, we shouldn't process this
        int profit1 = 0;
        if (weights[i] <= W)
            profit1 = profits[i] + knapsackRecursive_Grokking(profits, weights, W - weights[i], i + 1);

        // recursive call after excluding the element at the i
        int profit2 = knapsackRecursive_Grokking(profits, weights, W, i + 1);

        return max(profit1, profit2);
    }
```

### #2 Top Down Memo
```java
    // TC: O(NC), SC: O(NC + N), C is capacity
    public int solveKnapsack_Grokking_TopDownMemo(int[] profits, int[] weights, int W) {
        Integer[][] dp = new Integer[profits.length][W + 1];
        return this.knapsackRecursive(dp, profits, weights, W, 0);
    }

    private int knapsackRecursive(Integer[][] dp, int[] profits, int[] weights, int W, int i) {

        // base checks
        if (W <= 0 || i >= profits.length) return 0;

        // if we have already solved a similar problem, return the result from memory
        if (dp[i][W] != null) return dp[i][W];

        // recursive call after choosing the element at the i
        // if the weight of the element at i exceeds the W, we shouldn't process this
        int profit1 = 0;
        if (weights[i] <= W)
            profit1 = profits[i] + knapsackRecursive(dp, profits, weights, W - weights[i], i + 1);

        // recursive call after excluding the element at the i
        int profit2 = knapsackRecursive(dp, profits, weights, W, i + 1);

        dp[i][W] = max(profit1, profit2);
        return dp[i][W];
    }
```

### #3 DP Bottom UP 2D
```java
    // TC: O(NC), SC: O(NC)
    public int solveKnapsack_Grokking_BottomUp_2D(int[] profits, int[] weights, int capacity) {
        // basic checks
        if (capacity <= 0 || profits.length == 0 || weights.length != profits.length) return 0;

        int n = profits.length;
        int[][] dp = new int[n][capacity + 1];

        // populate the capacity=0 columns, with '0' capacity we have '0' profit
        for (int i = 0; i < n; i++) dp[i][0] = 0;

        // if we have only one weight, we will take it if it is not more than the capacity
        for (int c = 0; c <= capacity; c++) if (weights[0] <= c) dp[0][c] = profits[0];

        // process all sub-arrays for all the capacities
        for (int i = 1; i < n; i++) {
            for (int c = 1; c <= capacity; c++) {
                int profit1 = 0, profit2 = 0;
                // include the item, if it is not more than the capacity
                if (weights[i] <= c) profit1 = profits[i] + dp[i - 1][c - weights[i]];
                // exclude the item
                profit2 = dp[i - 1][c];
                // take maximum
                dp[i][c] = max(profit1, profit2);
            }
        }

        // maximum profit will be at the bottom-right corner.
        return dp[n - 1][capacity];
    }

    // todo this is to print the elements
    private void printSelectedElements(int dp[][], int[] weights, int[] profits, int W) {
        System.out.print("Selected weights:");
        int n = weights.length - 1;
        int totalProfit = dp[n][W];
        for (int i = n; i > 0; i--) {
            if (totalProfit != dp[i - 1][W]) {
                System.out.print(" " + weights[i]);
                W -= weights[i];
                totalProfit -= profits[i];
            }
        }

        if (totalProfit != 0) System.out.print(" " + weights[0]);
        System.out.println("");
    }

```

### #4 DP Bottom UP 1D
```java
    // TC: O(NC), SC: O(2C)
    public static int solveKnapsack_Grokking_BottomUp_1D(int[] profits, int[] weights, int W) {
        // basic checks
        if (W <= 0 || profits.length == 0 || weights.length != profits.length) return 0;

        int n = profits.length;
        // we only need one previous row to find the optimal solution, overall we need '2' rows
        // the above solution is similar to the previous solution, the only difference is that
        // we use `i%2` instead if `i` and `(i-1)%2` instead if `i-1`
        int[][] dp = new int[2][W + 1];

        // if we have only one weight, we will take it if it is not more than the W
        for (int c = 0; c <= W; c++) if (weights[0] <= c) dp[0][c] = dp[1][c] = profits[0];

        // process all sub-arrays for all the capacities
        for (int i = 1; i < n; i++) {
            for (int c = 0; c <= W; c++) {
                int profit1 = 0, profit2 = 0;
                // include the item, if it is not more than the W
                if (weights[i] <= c) profit1 = profits[i] + dp[(i - 1) % 2][c - weights[i]];
                // exclude the item
                profit2 = dp[(i - 1) % 2][c];
                // take maximum
                dp[i % 2][c] = max(profit1, profit2);
            }
        }

        return dp[(n - 1) % 2][W];
    }
```

### #5 DP Bottom UP 1D better
```java
    /**
     * TC: O(NC), SC: O(C)
     * // todo this is better than below solution {@link #solveKnapsack_Grokking_BottomUp_1D(int[], int[], int)} coz it uses single array instead of 2 arrays
     *
     * @param profits
     * @param weights
     * @param W
     * @return
     */
    public static int solveKnapsack_Grokking_BottomUp_1D_Better(int[] profits, int[] weights, int W) {
        // basic checks
        if (W <= 0 || profits.length == 0 || weights.length != profits.length) return 0;

        int n = profits.length;
        int[] dp = new int[W + 1];

        // if we have only one weight, we will take it if it is not more than the
        // W
        for (int c = 0; c <= W; c++) if (weights[0] <= c) dp[c] = profits[0];

        // process all sub-arrays for all the capacities
        for (int i = 1; i < n; i++) {
            for (int c = W; c >= 0; c--) {
                int profit1 = 0, profit2 = 0;
                // include the item, if it is not more than the W
                if (weights[i] <= c) profit1 = profits[i] + dp[c - weights[i]];
                // exclude the item
                profit2 = dp[c];
                // take maximum
                dp[c] = max(profit1, profit2);
            }
        }

        return dp[W];
    }
```

### #6 DP Bottom UP 1D Mine
```java
    // Looks like this is much nicer and shorter, but not sure if it works for all testcases since grokking has no good test cases
    // TC: O(NC), SC: O(C)
    public int solveKnapsack_BottomUP_1D_Mine(int[] c, int[] wt, int W) {
        int n = c.length, dp[] = new int[W + 1];

        for (int i = 1; i < n + 1; i++) {
            for (int w = W; w >= 0; w--) {
                int notIncluded = dp[w], included = 0;
                if (w >= wt[i - 1]) included = c[i - 1] + dp[w - wt[i - 1]];
                dp[w] = max(included, notIncluded);
            }
        }

        return dp[W];
    }
```


## Common Pitfalls to Avoid
- For bottom up DP, be careful with initialization of dp array for some problems.

---


# Partition equal subset sum
## Intro
https://leetcode.com/problems/partition-equal-subset-sum/
![[Pasted image 20220321104946.png]]

## Thoughts
- We want to split the array into two parts and each part should have same sum. Let total sum of array be T. Each split part sum be S. This means that `2S = T`, which implies that we need `S = T / 2`. Meaning if we have an odd total sum of the array, then its not possible to split. Therefore this can be the immediate check.
- After this we are essentially changing this problem to something like target sum, where we are asking if there is a subset in the array whose sum is equal to `T/2`. 
- Following this, coding the recursive solution is fairly easy and from there just follow the optimizations

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^n), SC: O(n)
    public boolean canPartition_BruteForce(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if (sum % 2 == 1) return false;
        return dfs(nums, sum / 2, 0);
    }

    private boolean dfs(int[] nums, int target, int i) {
        // shorter way to write the boolean condition
        // return i != nums.length && target >= 0 && (target == 0 || dfs(nums, target - nums[i], i + 1) || dfs(nums, target, i + 1));

        if (i == nums.length || target < 0) return false;
        return target == 0 || dfs(nums, target - nums[i], i + 1) || dfs(nums, target, i + 1);
    }
```

### #2 Top Down Memo
```java
    // TC: O(m * n), SC: O(m * n), n = nums.length, m is the target sum
    public boolean canPartition_TopDownDP(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if (sum % 2 == 1) return false;

        int target = sum / 2;
        Boolean[][] memo = new Boolean[nums.length + 1][target + 1];
        return dfs(nums, sum / 2, nums.length - 1, memo);
    }

    private boolean dfs(int[] nums, int target, int i, Boolean[][] memo) {
        if (target == 0) return true;
        if (i == 0 || target < 0) return false;
        if (memo[i][target] != null) return memo[i][target];

        memo[i][target] = dfs(nums, target, i - 1, memo) || dfs(nums, target - nums[i - 1], i - 1, memo);
        return memo[i][target];
    }
```

### #3 DP Bottom UP 2D
```java
    // TC: O(mn), SC: O(mn)
    public boolean canPartition_BottomUP_DP_2D(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if (sum % 2 == 1) return false;

        int target = sum / 2;
        boolean dp[][] = new boolean[nums.length + 1][target + 1];
        dp[0][0] = true;
        for (int i = 1; i < dp.length; i++) {
            for (int j = 0; j < dp[0].length; j++) {
                if (j < nums[i - 1]) dp[i][j] = dp[i - 1][j];
                else dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i - 1]];
            }
        }
        return dp[nums.length][target];
    }

    // TC: O(mn), SC: O(mn)
    // the difference between this and the above is that here we are using
    // boolean[][] dp = new boolean[nums.length][(target) + 1]; instead of
    // boolean dp[][] = new boolean[nums.length + 1][target + 1];
    public boolean canPartition_BottomUP_DP_2D_MyWay(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if (sum % 2 == 1) return false;
        int target = sum / 2;
        boolean[][] dp = new boolean[nums.length][(target) + 1];
        for (int i = 0; i < dp.length; i++) dp[i][0] = true;
        for (int j = 0; j <= target; j++) if (nums[0] == j) dp[0][j] = true;

        for (int i = 1; i < dp.length; i++) {
            for (int j = 1; j <= target; j++) {
                if (j < nums[i]) dp[i][j] = dp[i - 1][j];
                else dp[i][j] = dp[i - 1][j - nums[i]] || dp[i - 1][j];
            }
        }
        return dp[nums.length - 1][target];
    }
```

### #4 DP Bottom UP 1D
```java
    // TC: O(mn), SC: O(m)
    public boolean canPartition_BottomUp_DP_1D_Mine(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if (sum % 2 == 1) return false;
        int target = sum / 2;

        boolean dp[][] = new boolean[2][target + 1];
        for (int t = 0; t <= target; t++) dp[0][t] = t == nums[0];

        for (int i = 1; i < nums.length; i++) {
            for (int t = 1; t <= target; t++) {
                boolean including = false, notIncluding = dp[(i - 1) % 2][t];
                if (t >= nums[i]) including = dp[(i - 1) % 2][t - nums[i]];
                dp[i % 2][t] = including || notIncluding;
            }
        }
        return dp[(nums.length - 1) % 2][target];
    }

    // TC: O(mn), SC: O(m)
    public boolean canPartition_BottomUP_DP_1D(int[] nums) {
        if (nums.length == 0) return false;
        int sum = Arrays.stream(nums).sum();
        if (sum % 2 == 1) return false;

        int target = sum / 2;
        boolean[] dp = new boolean[target + 1];
        dp[0] = true;
        for (int num : nums) {
            for (int j = dp.length - 1; j >= num; j--) {
                dp[j] |= dp[j - num];
            }
        }
        return dp[target];
    }
```

### #5 DP Using Set
```java
    // TC: O(mn), SC: O(m), m is sum of elements, n = nums.length
    public boolean canPartition_Set(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if (sum % 2 != 0) return false;
        int target = sum / 2;

        Set<Integer> runningSet = new HashSet<>();
        runningSet.add(0);
        runningSet.add(nums[0]);

        for (int i = 1; i < nums.length; i++) {
            Set<Integer> tempSet = new HashSet<>(runningSet);
            for (Integer num : tempSet) {
                int currSum = num + nums[i];
                if (currSum == target)
                    return true;
                if (currSum < target) runningSet.add(currSum);
            }
        }
        return runningSet.contains(target);
    }
```

## Common Pitfalls to Avoid
- Getting the bottom up is tricky, so be careful with initialization, direction of the loop and final answer location.

---

# Subset Sum(Grokking)
## Intro
![[Pasted image 20220321110210.png]]

## Thoughts
- just follow the problem description and it will lead you to the answer

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^n), SC: O(n)
    private static boolean canPartition_BruteForceRecursive(int[] nums, int sum) {
        return helper_bruteForceRecursive(nums, sum, nums.length - 1);
    }

    private static boolean helper_bruteForceRecursive(int[] nums, int sum, int i) {
        if (i < 0) return sum == 0;
        return sum >= 0 && (helper_bruteForceRecursive(nums, sum, i - 1) || helper_bruteForceRecursive(nums, sum - nums[i], i - 1));
    }
```

### #2 TopDown Memo
```java
    // TC: O(NS), SC: O(NS)
    private static boolean canPartition_TopDownMemo(int[] nums, int sum) {
        Boolean[][] memo = new Boolean[sum + 1][nums.length];
        return helper_topDownMemo(nums, sum, nums.length - 1, memo);
    }

    private static boolean helper_topDownMemo(int[] nums, int sum, int i, Boolean[][] memo) {
        if (i < 0) return sum == 0;
        if (sum < 0) return false;
        return memo[sum][i] != null
                ? memo[sum][i]
                : (memo[sum][i] = helper_topDownMemo(nums, sum, i - 1, memo) || helper_topDownMemo(nums, sum - nums[i], i - 1, memo));

    }
```

### #3 DP Bottom UP 2D
```java
    // TC: O(NS), SC: O(NS)
    private static boolean canPartition_BottomUp_2D(int[] nums, int sum) {
        boolean[][] dp = new boolean[sum + 1][nums.length + 1];
        dp[0][0] = true;
        for (int s = 1; s <= sum; s++) {
            for (int n = 1; n <= nums.length; n++) {
                boolean including = false, notIncluding = dp[s][n - 1];
                if (s >= nums[n - 1]) including = dp[s - nums[n - 1]][n - 1];
                dp[s][n] = including || notIncluding;
            }
        }
        return dp[sum][nums.length];
    }
```

### #4 DP Bottom UP 1D
```java
    // todo check how this works or if this works for few more test cases. on Grokking it seems to work though.
    // TC: O(NS), SC: O(S)
    private static boolean canPartition_BottomUp_1D_Better_Shorter(int[] nums, int sum) {
        boolean[] dp = new boolean[sum + 1];
        dp[0] = true;
        for (int n = 1; n <= nums.length; n++) {
            for (int s = sum; s >= 1; s--) {
                if (!dp[s] && s >= nums[n - 1]) dp[s] = dp[s - nums[n - 1]];
            }
        }
        return dp[sum];
    }

    // TC: O(NS), SC: O(S)
    private static boolean canPartition_BottomUp_1D_Better(int[] nums, int sum) {
        boolean[] dp = new boolean[sum + 1];
        dp[0] = true;
        for (int n = 1; n <= nums.length; n++) {
            // note how we are traversing from back and using only 1d array instead of a 2 row array like below method
            for (int s = sum; s >= 1; s--) {
                boolean including = false, notIncluding = dp[s];
                if (s >= nums[n - 1]) including = dp[s - nums[n - 1]];
                dp[s] = including || notIncluding;
            }
        }
        return dp[sum];
    }

    // TC: O(NS), SC: O(2S)
    private static boolean canPartition_BottomUp_1D(int[] nums, int sum) {
        boolean[][] dp = new boolean[2][sum + 1];
        dp[0][0] = true;
        for (int n = 1; n <= nums.length; n++) {
            for (int s = 1; s <= sum; s++) {
                boolean including = false, notIncluding = dp[(n - 1) % 2][s];
                if (s >= nums[n - 1]) including = dp[(n - 1) % 2][s - nums[n - 1]];
                dp[n % 2][s] = including || notIncluding;
            }
        }
        return dp[(nums.length - 1) % 2][sum];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Last Stone Weight II
## Intro
https://leetcode.com/problems/last-stone-weight-ii/
![[Pasted image 20220321110605.png]]

## Thoughts
- This is a very tricky problem. First we need to somehow connect that this is asking us to find min subset difference. How to derive this though?
- Once the above is derived, then lets say total sum of array is T and difference of two subsets is diff
- i.e., `S1 + S2 = T` and `S1 - S2 = diff`, from this we get `diff = S - 2 * S2`. 
- Now we need to find max of S2 in range `[0, S/2]`

## Solutions
### #1 BruteForce Recursive using left and right sums

```java
    // TC: O(2^n), SC: O(n)
    public int lastStoneWeightII_RecursiveBruteForce(int[] A) {
        return helper(A, 0, 0, A.length - 1);
    }

    private int helper(int[] A, int leftSum, int rightSum, int i) {
        return i < 0
                ? Math.abs(rightSum - leftSum)
                : Math.min(
                helper(A, leftSum, rightSum + A[i], i - 1),
                helper(A, leftSum + A[i], rightSum, i - 1)
        );
    }
```

### #2 Top Down Memo 3D
```java
    // TC: O(NSS), SC: O(NSS), N = nums.length S = totalSum
    // Memory Limit Exceeded on LC
    public int lastStoneWeightII_TopDownMemo3D(int[] A) {
        int sum = Arrays.stream(A).sum(), n = A.length;
        Integer[][][] memo = new Integer[n][sum + 1][sum + 1];
        return helper(A, 0, 0, A.length - 1, memo);
    }

    private int helper(int[] A, int leftSum, int rightSum, int i, Integer[][][] memo) {
        if (i < 0) return Math.abs(rightSum - leftSum);
        if (memo[i][leftSum][rightSum] != null) return memo[i][leftSum][rightSum];

        return memo[i][leftSum][rightSum] = Math.min(
                helper(A, leftSum, rightSum + A[i], i - 1, memo),
                helper(A, leftSum + A[i], rightSum, i - 1, memo)
        );
    }
```

### #3 Top Down Memo 2D
```java
    // TC: O(NS), SC: O(NS), N = nums.length S = totalSum
    // note we can identify a subproblem using leftSum itself because rightSum would be totalSum - leftSum anyway
    public int lastStoneWeightII_TopDownMemo2D(int[] A) {
        int sum = Arrays.stream(A).sum(), n = A.length;
        Integer[][] memo = new Integer[n][sum + 1];
        return helper(A, 0, 0, A.length - 1, memo);
    }

    private int helper(int[] A, int leftSum, int rightSum, int i, Integer[][] memo) {
        if (i < 0) return Math.abs(rightSum - leftSum);
        if (memo[i][leftSum] != null) return memo[i][leftSum];

        return memo[i][leftSum] = Math.min(
                helper(A, leftSum, rightSum + A[i], i - 1, memo),
                helper(A, leftSum + A[i], rightSum, i - 1, memo)
        );
    }
```

### #4 DP Bottom UP 2D
```java
    // TC: O(NS), SC: O(NS)
    public int lastStoneWeightII_BottomUp_2D(int[] A) {
        int totalSum = Arrays.stream(A).sum();

        int n = A.length;
        boolean[][] dp = new boolean[n][totalSum / 2 + 1];

        // populate the totalSum=0 columns, as we can always form '0' totalSum with an empty set
        for (int i = 0; i < n; i++) dp[i][0] = true;

        // with only one number, we can form a subset only when the required totalSum is equal to that number
        for (int s = 1; s <= totalSum / 2; s++) dp[0][s] = A[0] == s;

        // process all subsets for all sums
        for (int i = 1; i < A.length; i++) {
            for (int s = 1; s <= totalSum / 2; s++) {
                // if we can get the totalSum 's' without the number at index 'i'
                if (dp[i - 1][s]) dp[i][s] = dp[i - 1][s];
                else if (s >= A[i]) dp[i][s] = dp[i - 1][s - A[i]];
                // else include the number and see if we can find a subset to get the remaining totalSum
            }
        }

        int sum1 = 0;
        // Find the largest index in the last row which is true
        for (int i = totalSum / 2; i >= 0; i--) {
            if (dp[n - 1][i]) {
                sum1 = i;
                break;
            }
        }

        int sum2 = totalSum - sum1;
        return Math.abs(sum2 - sum1);
    }

    // TC: O(NS), SC: O(NS)
    public int lastStoneWeightII_BottomUP2D_Better(int[] A) {
        int totalSum = Arrays.stream(A).sum(), leftSum = 0, n = A.length;
        boolean[][] dp = new boolean[totalSum + 1][n + 1];
        for (int i = 0; i <= n; i++) dp[0][i] = true;

        for (int i = 1; i <= n; i++) {
            for (int s = 1; s <= totalSum / 2; s++) {
                if (dp[s][i - 1] || (s >= A[i - 1] && dp[s - A[i - 1]][i - 1])) {
                    dp[s][i] = true;
                    leftSum = Math.max(leftSum, s);
                }
            }
        }
        return totalSum - 2 * leftSum;
    }
```

### #5 DP Bottom UP 1D
```java
    // TC: O(NS), SC: O(S)
    public int lastStoneWeightII_BottomUP1D(int[] A) {
        int totalSum = Arrays.stream(A).sum();
        boolean[] dp = new boolean[totalSum / 2 + 1];

        dp[0] = true;
        for (int n = 1; n <= A.length; n++) {
            for (int s = dp.length - 1; s >= 1; s--) {
                if (!dp[s] && s >= A[n - 1]) dp[s] = dp[s - A[n - 1]];
            }
        }

        int leftSum = 0;
        for (int s = dp.length - 1; s >= 1; s--) {
            if (dp[s]) {
                leftSum = s;
                break;
            }
        }
        int rightSum = totalSum - leftSum;
        return Math.abs(leftSum - rightSum);
    }
```
## Common Pitfalls to Avoid
- N/A

---

# Count of Subset Sum(Grokking)
## Intro
![[Pasted image 20220321113950.png]]

## Thoughts
- Pretty straight forward, just follow the problem description

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^n), SC: O(n)
    public static int countSubsets_BruteForceRecursive(int[] nums, int sum) {
        return helper_bruteForceRecursive(nums, sum, nums.length - 1);
    }

    private static int helper_bruteForceRecursive(int[] nums, int sum, int i) {
        if (sum == 0) return 1;
        if (i < 0 || sum < 0) return 0;

        return helper_bruteForceRecursive(nums, sum, i - 1) + helper_bruteForceRecursive(nums, sum - nums[i], i - 1);
    }
```

### #2 Top Down Memo
```java
    // TC: O(NS), SC: O(NS)
    public static int countSubsets_TopDownMemo(int[] nums, int sum) {
        return helper_topDownMemo(nums, sum, nums.length - 1, new Integer[sum + 1][nums.length]);
    }

    private static int helper_topDownMemo(int[] nums, int sum, int i, Integer[][] memo) {
        if (sum == 0) return 1;
        if (i < 0 || sum < 0) return 0;

        if (memo[sum][i] != null) return memo[sum][i];

        return memo[sum][i] = helper_topDownMemo(nums, sum, i - 1, memo) + helper_topDownMemo(nums, sum - nums[i], i - 1, memo);
    }
```

### #3 DP Bottom UP 2D
```java
    // TC: O(NS), SC: O(NS)
    public static int countSubsets_BottomUp_2D(int[] nums, int sum) {
        int n = nums.length, dp[][] = new int[n][sum + 1];
        for (int i = 0; i < n; i++) dp[i][0] = 1;
        for (int s = 1; s <= sum; s++) dp[0][s] = nums[0] == s ? 1 : 0;

        for (int i = 1; i < n; i++) {
            for (int s = 1; s <= sum; s++) {
                if (s >= nums[i]) dp[i][s] += dp[i - 1][s - nums[i]];
                dp[i][s] += dp[i - 1][s];
            }
        }

        return dp[n - 1][sum];
    }
```

### #4 DP Bottom UP 1D
```java
    // TC: O(NS), SC: O(S)
    // not sure if this works for all test cases coz grokking has only two test cases
    public static int countSubsets_BottomUP_1D_Even_Better(int[] A, int T) {
        int n = A.length, dp[] = new int[T + 1];

        dp[0] = 1;
        for (int i = 1; i < n + 1; i++) {
            for (int t = T; t >= 0; t--) {
                int included = t >= A[i - 1] ? dp[t - A[i - 1]] : 0;
                dp[t] = dp[t] + included;
            }
        }
        return dp[T];
    }

    // TC: O(NS), SC: O(S)
    public static int countSubsets_BottomUp_1D_Better(int[] nums, int sum) {
        int n = nums.length, dp[] = new int[sum + 1];
        dp[0] = 1;
        for (int s = 1; s <= sum; s++) dp[s] = nums[0] == s ? 1 : 0;
        for (int i = 1; i < n; i++) {
            // note we are traveling from back, very useful trick
            for (int s = sum; s >= 1; s--) dp[s] += s >= nums[i] ? dp[s - nums[i]] : 0;
        }

        return dp[sum];
    }

    // TC: O(NS), SC: O(2S)
    public static int countSubsets_BottomUp_1D(int[] nums, int sum) {
        int n = nums.length, dp[][] = new int[2][sum + 1];
        dp[0][0] = 1;
        for (int s = 1; s <= sum; s++) dp[0][s] = nums[0] == s ? 1 : 0;
        for (int i = 1; i < n; i++) {
            for (int s = 1; s <= sum; s++) {
                // note make sure to reset previous state. If you use if condition like below and did dp[][] += xxx, then you would get wrong answer
                // since += would keep adding to previous state instead of resetting it.
                dp[i % 2][s] = s >= nums[i] ? dp[(i - 1) % 2][s - nums[i]] : 0;
                dp[i % 2][s] += dp[(i - 1) % 2][s];
            }
        }

        return dp[(n - 1) % 2][sum];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Target Sum
## Intro
https://leetcode.com/problems/target-sum/
![[Pasted image 20220321114216.png]]

## Thoughts
- list

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^n), SC: O(n)
    public int findTargetSumWays_Recursive_BruteForce(int[] nums, int target) {
        return dfs(nums, target, 0, 0);
    }

    private int dfs(int[] nums, int target, int sum, int i) {
        if (i == nums.length) return sum == target ? 1 : 0;

        return dfs(nums, target, sum + nums[i], i + 1) + dfs(nums, target, sum - nums[i], i + 1);
    }
```

### #2 Top Down Memo using Hashmap
```java
    // TC: O(l*n), SC: O(l*n), where n = nums.length, l represents range of sum(not target btw). l is different compared to target
    public int findTargetSumWays_Memo_Using_HashMap_Of_String(int[] nums, int target) {
        return dfs(nums, target, 0, 0, new HashMap<>());
    }

    private int dfs(int[] nums, int target, int sum, int i, Map<String, Integer> map) {
        String key = sum + "-" + i;
        if (map.containsKey(key)) return map.get(key);
        if (i == nums.length) {
            map.put(key, sum == target ? 1 : 0);
            return map.get(key);
        }

        map.put(key, dfs(nums, target, sum + nums[i], i + 1, map) + dfs(nums, target, sum - nums[i], i + 1, map));
        return map.get(key);
    }
```

### #3 Top Down Memo Using Pair class
```java
    private Map<Pair<Integer, Integer>, Integer> seenSums = new HashMap<>();

    // TC: O(l*n), SC: O(L*n)??
    public int findTargetSumWays_UsingPairClass(int[] nums, int target) {
        return findTargetSumWaysHelper_UsingPairClass(nums, target, 0, 0);
    }

    private int findTargetSumWaysHelper_UsingPairClass(int[] nums, int target, int startIdx, int sumSoFar) {
        Pair<Integer, Integer> pair = new Pair<>(sumSoFar, startIdx);
        if (seenSums.containsKey(pair)) {
            return seenSums.get(pair);
        }
        if (startIdx == nums.length) {
            return sumSoFar == target ? 1 : 0;
        }
        int plusWays = findTargetSumWaysHelper_UsingPairClass(nums, target, startIdx + 1, sumSoFar + nums[startIdx]);
        int minusWays = findTargetSumWaysHelper_UsingPairClass(nums, target, startIdx + 1, sumSoFar - nums[startIdx]);
        int totalWays = plusWays + minusWays;
        seenSums.put(pair, totalWays);
        return plusWays + minusWays;
    }
```

### #4 Top Down Memo Using Array
```java
    private int offset;
    private Integer[][] memo;

    // TC: O(l*n), SC: O(l*n)
    public int findTargetSumWays_TopDownMemoUsingArray(int[] nums, int target) {
        int totalSum = Arrays.stream(nums).sum();

        // find out what's the min and max value sum can take.
        int modifier = abs(target);
        int leftBound = -totalSum - modifier, rightBound = totalSum + modifier;
        int arraySize = rightBound - leftBound + 1;

        // set instance variables to use for future recursion calls
        this.offset = leftBound;
        this.memo = new Integer[nums.length][arraySize];
        return helper(nums, target, nums.length - 1);
    }

    private int helper(int[] nums, int sum, int i) {
        if (i < 0) return sum == 0 ? 1 : 0;

        int key = sum - offset;
        if (memo[i][key] != null) return memo[i][key];

        return memo[i][key]
                = helper(nums, sum - nums[i], i - 1)
                + helper(nums, sum + nums[i], i - 1);
    }
```

### #5 DP Bottom UP 2D
```java
    // TC: O(nl), SC: O(nl), n = nums.length, l = range of sum possible
    public int findTargetSumWays_BottomUp_Better(int[] nums, int S) {
        int sum = 0;
        for (int i : nums) sum += i;
        if (S > sum || S < -sum) return 0;
        int[][] dp = new int[nums.length][sum * 2 + 1];
        dp[0][nums[0] + sum] = 1;
        dp[0][-nums[0] + sum] += 1;
        for (int i = 1; i < nums.length; i++) {
            for (int j = -sum; j <= sum; j++) {
                if (dp[i - 1][j + sum] != 0) {
                    int n = nums[i];
                    dp[i][j + sum + n] += dp[i - 1][j + sum];
                    dp[i][j + sum - n] += dp[i - 1][j + sum];
                }
            }
        }
        return dp[nums.length - 1][S + sum];
    }
```

## Common Pitfalls to Avoid
- N/A

---