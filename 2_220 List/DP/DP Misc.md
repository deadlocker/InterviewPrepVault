
# Minimum Number of Removals to Make Mountain Array
## Intro
https://leetcode.com/problems/minimum-number-of-removals-to-make-mountain-array/
![[Pasted image 20220322133256.png]]

## Thoughts
- N/A

## Solutions
### #1 Using lbs
```java
    // TC: O(n^2), SC: O(n)
    public int minimumMountainRemovals(int[] nums) {
        return nums.length - lbs(nums);
    }

    private int lbs(int[] nums) {
        int[] lds = new int[nums.length];
        int[] ldsReverse = new int[nums.length];

        // find LDS for every index up to the beginning of the array
        for (int i = 0; i < nums.length; i++) {
            lds[i] = 1; // every element is an LDS of length 1
            for (int j = i - 1; j >= 0; j--)
                if (nums[j] < nums[i]) lds[i] = max(lds[i], lds[j] + 1);
        }

        // find LDS for every index up to the end of the array
        for (int i = nums.length - 1; i >= 0; i--) {
            ldsReverse[i] = 1; // every element is an LDS of length 1
            for (int j = i + 1; j < nums.length; j++)
                if (nums[j] < nums[i]) ldsReverse[i] = max(ldsReverse[i], ldsReverse[j] + 1);
        }

        int maxLength = 0;
        for (int i = 1; i < nums.length - 1; i++) {
            if (lds[i] > 1 && ldsReverse[i] > 1) maxLength = max(maxLength, lds[i] + ldsReverse[i] - 1);
        }
        return maxLength;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Trapping Rain Water
## Intro
https://leetcode.com/problems/trapping-rain-water/
![[Pasted image 20220322133341.png]]
## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n^2), SC: O(n)
    public int trap_BruteForce(int[] heights) {
        int left[] = getLeft(heights);
        int right[] = getRight(heights);

        int ans = 0;
        for (int i = 0; i < heights.length; i++) ans += min(left[i], right[i]) - heights[i];
        return ans;
    }

    private int[] getLeft(int[] heights) {
        int[] left = new int[heights.length];
        for (int i = 0; i < heights.length; i++) {
            int max = heights[i];
            for (int j = 0; j <= i; j++) max = max(max, heights[j]);
            left[i] = max;
        }
        return left;
    }

    private int[] getRight(int[] heights) {
        int[] right = new int[heights.length];
        for (int i = 0; i < heights.length; i++) {
            int max = heights[i];
            for (int j = i; j < heights.length; j++) max = max(max, heights[j]);
            right[i] = max;
        }
        return right;
    }
```

### #2 DP
```java
    // TC: O(n), SC: O(n)
    public int trap_DP(int[] heights) {
        int n = heights.length;
        if (n == 0) return 0;
        int left[] = new int[n], right[] = new int[n];

        left[0] = heights[0];
        for (int i = 1; i < n; i++) left[i] = max(left[i - 1], heights[i]);
        right[n - 1] = heights[n - 1];
        for (int i = n - 2; i >= 0; i--) right[i] = max(right[i + 1], heights[i]);

        int ans = 0;
        for (int i = 0; i < n; i++) ans += min(left[i], right[i]) - heights[i];
        return ans;
    }
```

### #3 Stack
```java
    // TC: O(n), SC: O(n)
    // Todo understand how this works exactly
    public int trap_StackApproach(int[] height) {
        int ans = 0, i = 0;
        Stack<Integer> stack = new Stack<>();
        while (i < height.length) {
            while (!stack.isEmpty() && height[i] > height[stack.peek()]) {
                int top = stack.pop();
                if (stack.isEmpty()) break;
                int curWidth = i - stack.peek() - 1;
                int boundedHeight = Math.min(height[i], height[stack.peek()]) - height[top];
                ans += curWidth * boundedHeight;
            }
            stack.push(i++);
        }
        return ans;
    }
```

### #4 Two Pointer
```java
    // TC: O(n), SC: O(1)
    // Todo understand how this works exactly
    public int trap_TwoPointerApproach(int[] height) {
        int i = 0, j = height.length - 1, ans = 0, leftMax = 0, rightMax = 0;
        while (i < j) {
            if (height[i] < height[j]) {
                if (height[i] >= leftMax) leftMax = height[i];
                else ans += leftMax - height[i];
                i++;
            } else {
                if (height[j] >= rightMax) rightMax = height[j];
                else ans += rightMax - height[j];
                j--;
            }
        }
        return ans;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Combination Sum IV
## Intro
https://leetcode.com/problems/combination-sum-iv/
![[Pasted image 20220322133457.png]]

## Thoughts
- N/A

## Solutions
### #1 Recursive BruteForce
```java
    // TC: O(n^target), SC: O(target)
    public int combinationSum4_RecursiveBruteForce(int[] nums, int target) {
        return helper(nums, target);
    }

    private int helper(int[] nums, int target) {
        if (target == 0) return 1;

        int ways = 0;
        for (int num : nums) {
            int diff = target - num;
            if (diff >= 0) ways += helper(nums, diff);
        }
        return ways;
    }
```

### #2 TopDown DP
```java
    // Note for some reason, if you use int[] instead of Map, this is giving TLE on LC
    // TC: O(target*n), SC: O(target)
    public int combinationSum4_TopDownDP(int[] nums, int target) {
        return helper(nums, target, new HashMap<>());
    }

    private int helper(int[] nums, int target, Map<Integer, Integer> memo) {
        if (target == 0) return 1;

        if (memo.containsKey(target)) return memo.get(target);

        int ways = 0;
        for (int num : nums) {
            int diff = target - num;
            if (diff >= 0) ways += helper(nums, diff, memo);
        }
        memo.put(target, ways);
        return ways;
    }
```

### #3 DP BottomUP
```java
    // TC: O(target * n), SC: O(target)
    public int combinationSum4_DP_BottomUP(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i < dp.length; i++) {
            for (int num : nums) {
                int diff = i - num;
                if (diff >= 0) dp[i] += dp[diff];
            }
        }
        return dp[target];
    }
```

### #4 DP Minor Optimization
```java
    // TC: O(target*n + nlogn), SC: O(target)
    public int combinationSum4_Minor_Optimization(int[] nums, int target) {
        // If we sort, we can break early in below for loops
        Arrays.sort(nums);
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i < dp.length; i++) {
            for (int num : nums) {
                int diff = i - num;
                if (diff >= 0) dp[i] += dp[diff];
                    // We can break early because any future num value will be greater than current num value,
                    // therefore diff will always be negative for future num values if diff is negative for current num value itself.
                else break;

            }
        }
        return dp[target];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Number of Longest Increasing Subsequence
## Intro
https://leetcode.com/problems/number-of-longest-increasing-subsequence/
![[Pasted image 20220322133738.png]]

## Thoughts
- N/A

## Solutions
### #1 LIS
```java
    // TC: O(n^2), SC: O(n)
    public int findNumberOfLIS(int[] nums) {
        int n = nums.length, longest = 0;

        int[] lengths = new int[n], counts = new int[n];
        Arrays.fill(counts, 1);

        for (int j = 0; j < n; j++) {
            for (int i = 0; i < j; i++) {
                if (nums[i] < nums[j]) {
                    if (lengths[i] >= lengths[j]) {
                        lengths[j] = lengths[i] + 1;
                        counts[j] = counts[i];
                    } else if (lengths[i] + 1 == lengths[j]) counts[j] += counts[i];
                }
            }
            longest = Math.max(longest, lengths[j]);
        }

        int count = 0;
        for (int i = 0; i < n; i++) {
            if (lengths[i] == longest) count += counts[i];
        }
        return count;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Best Time to Buy and Sell Stock With Cooldown
## Intro
https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/
![[Pasted image 20220322133822.png]]

## Thoughts
- N/A

## Solutions
### #1 DP State Machine
```java
    // TC: O(n), SC: O(1)
    public int maxProfit_StateMachine_DP(int[] prices) {
        int sold = Integer.MIN_VALUE, held = Integer.MIN_VALUE, reset = 0;
        for (int price : prices) {
            int preSold = sold;

            sold = held + price;
            held = max(held, reset - price);
            reset = max(reset, preSold);
        }
        return max(sold, reset);
    }
```

### #2 DP Math
```java
    // TC: O(n^2), SC: O(n)
    public int maxProfit_MathDP(int[] prices) {
        int[] dp = new int[prices.length + 2];
        for (int i = prices.length - 1; i >= 0; i--) {
            int c1 = 0;
            // case1: buy and sell stock
            for (int sell = i + 1; sell < prices.length; sell++) {
                int profit = prices[sell] - prices[i] + dp[sell + 2];
                c1 = Math.max(c1, profit);
            }

            // case2: do no transaction with stock p[i]
            int c2 = dp[i + 1];

            // wrap up two cases
            dp[i] = Math.max(c1, c2);
        }
        return dp[0];
    }
```

## Common Pitfalls to Avoid
- N/A

---