
# Longest Palindromic Subsequence
## Intro
https://leetcode.com/problems/longest-palindromic-subsequence/
![[Pasted image 20220321134224.png]]
## Thoughts
- N/A

## Solutions
### #1 Brute Force Recursive

```java
    // TC: O(2^n), SC: O(n)
    public int longestPalindromeSubseq_BruteForceRecursive(String s) {
        return helper_bruteForceRecursive(s, 0, s.length() - 1);
    }

    private int helper_bruteForceRecursive(String s, int start, int end) {
        if (start > end) return 0;
        if (start == end) return 1;

        if (s.charAt(start) == s.charAt(end)) return 2 + helper_bruteForceRecursive(s, start + 1, end - 1);

        int c1 = helper_bruteForceRecursive(s, start + 1, end);
        int c2 = helper_bruteForceRecursive(s, start, end - 1);
        return max(c1, c2);
    }
```

### #2 TopDown Memo
```java
    // TC: O(n^2), SC: O(n^2 + n)
    public int longestPalindromeSubseq_TopDownMemo(String s) {
        Integer[][] memo = new Integer[s.length()][s.length()];
        return helper(s, 0, s.length() - 1, memo);
    }

    private int helper(String s, int start, int end, Integer[][] memo) {
        if (start > end) return 0;
        if (start == end) return 1;
        if (memo[start][end] != null) return memo[start][end];

        if (s.charAt(start) == s.charAt(end)) return memo[start][end] = 2 + helper(s, start + 1, end - 1, memo);

        int c1 = helper(s, start + 1, end, memo);
        int c2 = helper(s, start, end - 1, memo);
        return memo[start][end] = max(c1, c2);
    }
```

### #3 Using LCS
```java
    // TC: O(n^2), SC: O(n^2)
    public int longestPalindromeSubseq_UsingLCS(String s) {
        return lcs(s, new StringBuilder(s).reverse().toString());
    }

    // TC: O(mn), SC: O(mn)
    private int lcs(String s1, String s2) {
        int dp[][] = new int[s1.length() + 1][s2.length() + 1];

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

### #4 DP 2D
```java
    // TC: O(n^2), SC: O(n^2)
    public int longestPalindromeSubseq_DP_BottomUP_2D(String s) {
        int n = s.length(), dp[][] = new int[n][n];

        // every sequence with one element is a palindrome of length 1
        for (int i = 0; i < dp.length; i++) dp[i][i] = 1;

        for (int i = n - 1; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                if (s.charAt(i) == s.charAt(j)) dp[i][j] = 2 + dp[i + 1][j - 1];
                else dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
            }
        }

        return dp[0][n - 1];
    }
```

### #5 DP 1D
```java
    // TC: O(n^2), SC: O(n)
    public int longestPalindromeSubseq_DP_BottomUP_1D(String s) {
        int n = s.length(), dp[] = new int[n];

        for (int i = n - 1; i >= 0; i--) {
            dp[i] = 1;
            int prev = 0;
            for (int j = i + 1; j < n; j++) {
                int temp = dp[j];
                if (s.charAt(i) == s.charAt(j)) dp[j] = 2 + prev;
                else dp[j] = max(dp[j], dp[j - 1]);
                prev = temp;
            }
        }

        return dp[n - 1];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Minimum Insertion Steps to Make a String Palindrome
## Intro
https://leetcode.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/
![[Pasted image 20220321134323.png]]

## Thoughts
- list

## Solutions
### #1 Using LPS

```java
    // TC: O(n^2), SC: O(n)
    public int minInsertions_UsingLPS(String s) {
        return s.length() - lps(s);
    }

    // this is longest palindromic subsequence
    // TC: O(n^2), SC: O(n)
    private int lps(String s) {
        int n = s.length(), dp[] = new int[n];

        for (int i = n - 1; i >= 0; i--) {
            dp[i] = 1;
            int prev = 0;
            for (int j = i + 1; j < n; j++) {
                int temp = dp[j];
                if (s.charAt(i) == s.charAt(j)) dp[j] = 2 + prev;
                else dp[j] = max(dp[j], dp[j - 1]);
                prev = temp;
            }
        }

        return dp[n - 1];
    }
```

### #2 Using LCS
```java
    // TC: O(n^2), SC: O(n)
    public int minInsertions_UsingLCS(String s) {
        int lcs = lcs(s, new StringBuilder(s).reverse().toString());
        return s.length() - lcs;
    }

    // this is longest common subsequence
    // TC: O(mn), SC: O(mn)
    private int lcs(String s1, String s2) {
        int dp[][] = new int[s1.length() + 1][s2.length() + 1];

        for (int i = 0; i < dp.length; i++) {
            for (int j = 0; j < dp[0].length; j++) {
                if (i == 0 || j == 0) dp[i][j] = 0;
                else if (s1.charAt(i - 1) == s2.charAt(j - 1)) dp[i][j] = 1 + dp[i - 1][j - 1];
                else dp[i][j] = max(dp[i][j - 1], dp[i - 1][j]);
            }
        }

        return dp[s1.length()][s2.length()];
    }

    // TC: O(n^2), SC: O(n^2)
    public int minInsertions_UsingLCS_Better(String s) {
        int n = s.length();
        int[][] dp = new int[n + 1][n + 1];
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                dp[i + 1][j + 1] = s.charAt(i) == s.charAt(n - 1 - j) ? dp[i][j] + 1 : max(dp[i][j + 1], dp[i + 1][j]);
            }
        }
        return n - dp[n][n];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Palindrome Partitioning
## Intro
https://leetcode.com/problems/palindrome-partitioning/
![[Pasted image 20220321134503.png]]

## Thoughts
- list

## Solutions
### #1 DP Backtracking

```java
    // TC: O(n * 2^n), SC: O(n*n)
    public List<List<String>> partition_DP_Backtracking(String s) {
        int len = s.length();
        boolean[][] dp = new boolean[len][len];
        List<List<String>> result = new ArrayList<>();
        dfs_DP_Backtracking(result, s, 0, new ArrayList<>(), dp);
        return result;
    }

    private void dfs_DP_Backtracking(List<List<String>> res, String s, int start, List<String> cur, boolean[][] dp) {
        if (start >= s.length()) {
            res.add(new ArrayList<>(cur));
            return;
        }
        for (int end = start; end < s.length(); end++) {
            if (s.charAt(start) == s.charAt(end) && (end - start <= 2 || dp[start + 1][end - 1])) {
                dp[start][end] = true;
                cur.add(s.substring(start, end + 1));
                dfs_DP_Backtracking(res, s, end + 1, cur, dp);
                cur.remove(cur.size() - 1);
            }
        }
    }
```

### #2 DP Backtracking Another Way
```java
    // TC: O(n * 2^n), SC: O(n)
    public List<List<String>> partition_backtrackingApproach(String s) {
        List<List<String>> res = new ArrayList<>();
        dfs_backtracking(res, new ArrayList<>(), s, 0);
        return res;
    }

    private void dfs_backtracking(List<List<String>> res, List<String> cur, String s, int start) {
        if (start >= s.length()) {
            res.add(new ArrayList<>(cur));
            return;
        }
        for (int end = start; end < s.length(); end++) {
            if (isPalindrome(s, start, end)) {
                cur.add(s.substring(start, end + 1));
                dfs_backtracking(res, cur, s, end + 1);
                cur.remove(cur.size() - 1);
            }
        }
    }

    private boolean isPalindrome(String s, int low, int high) {
        while (low < high) {
            if (s.charAt(low++) != s.charAt(high--)) return false;
        }
        return true;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Palidrome Partitioning II
## Intro
https://leetcode.com/problems/palindrome-partitioning-ii/
![[Pasted image 20220321134635.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(n * 2^n), SC: O(n)
    public int minCut_BruteForceRecursive(String s) {
        return helper_bruteForceRecursive(s, 0, s.length() - 1, s.length() - 1);
    }

    private int helper_bruteForceRecursive(String s, int start, int end, int minCuts) {
        if (start == end || isPalindrome_BruteForce(s, start, end)) return 0;

        for (int i = start; i <= end; i++) {
            if (isPalindrome_BruteForce(s, start, i))
                minCuts = min(minCuts, 1 + helper_bruteForceRecursive(s, i + 1, end, minCuts));
        }

        return minCuts;
    }

    private boolean isPalindrome_BruteForce(String s, int l, int r) {
        while (l <= r) {
            if (s.charAt(l++) != s.charAt(r--)) return false;
        }
        return true;
    }
```

### #2 TopDown Memo
```java
    private Integer[][] memoCuts;
    private Boolean[][] memoPalindrome;

    // TC: O(n^2 * n), SC: O(n^2)
    public int minCut_TopDownMemo(String s) {
        int n = s.length();
        memoCuts = new Integer[n][n];
        memoPalindrome = new Boolean[n][n];
        return helper_topDownMemo(s, 0, s.length() - 1, s.length() - 1);
    }

    private int helper_topDownMemo(String s, int start, int end, int minCuts) {
        if (start == end || isPalindrome_topDown(s, start, end)) return 0;

        if (memoCuts[start][end] != null) return memoCuts[start][end];

        for (int i = start; i <= end; i++) {
            if (isPalindrome_topDown(s, start, i))
                minCuts = min(minCuts, 1 + helper_topDownMemo(s, i + 1, end, minCuts));
        }

        return memoCuts[start][end] = minCuts;
    }

    private boolean isPalindrome_topDown(String s, int l, int r) {
        if (l >= r) return true;

        if (memoPalindrome[l][r] != null) return memoPalindrome[l][r];

        return memoPalindrome[l][r] = s.charAt(l) == s.charAt(r) && isPalindrome_topDown(s, l + 1, r - 1);
    }
```

### #3 TopDown Space Optimized
```java
    private Integer[] memoCutsSingle;
    private Boolean[][] memoPalindrome;

    // TC: O(n^2 * n), SC: O(n^2)
    public int minCut_TopDown_OptimizedSpace(String s) {
        int n = s.length();
        memoCutsSingle = new Integer[n];
        memoPalindrome = new Boolean[n][n];
        return helper(s, 0, s.length() - 1, s.length() - 1);
    }

    private int helper(String s, int start, int end, int minCuts) {
        if (start == end || isPalindrome_topDown(s, start, end)) return 0;

        if (memoCutsSingle[start] != null) return memoCutsSingle[start];

        for (int i = start; i <= end; i++) {
            if (isPalindrome_topDown(s, start, i)) minCuts = min(minCuts, 1 + helper(s, i + 1, end, minCuts));
        }

        return memoCutsSingle[start] = minCuts;
    }
```

### #4 DP Bottom UP
```java
    // TC: O(n^2), SC: O(n^2)
    public int minCut_DP_BottomUP(String s) {
        int n = s.length();
        Integer[] dpCuts = new Integer[n];
        boolean[][] dpPalindrome = new boolean[n][n];
        buildPalindromeDP(s, s.length(), dpPalindrome);

        for (int end = 0; end < n; end++) {
            int minCut = end;
            for (int start = 0; start <= end; start++) {
                if (dpPalindrome[start][end]) minCut = start == 0 ? 0 : min(minCut, dpCuts[start - 1] + 1);
            }
            dpCuts[end] = minCut;
        }
        return dpCuts[n - 1];
    }

    private void buildPalindromeDP(String s, int n, boolean[][] dp) {
        for (int end = 0; end < s.length(); end++) {
            for (int start = 0; start <= end; start++) {
                if (s.charAt(start) == s.charAt(end) && (end - start <= 2 || dp[start + 1][end - 1]))
                    dp[start][end] = true;
            }
        }
    }
```

### #5 DP Bottom UP Optimized
```java
    // this is better than before one coz buildPalindromeDp method is not needed and this building can be done parallelly in for loop itself.
    // TC: O(n^2), SC: O(n^2)
    public int minCut_DP_BottomUP_Optimized(String s) {
        int n = s.length();
        Integer[] dpCuts = new Integer[n];
        boolean[][] dpPalindrome = new boolean[n][n];

        for (int end = 0; end < n; end++) {
            int minCuts = end;
            for (int start = 0; start <= end; start++) {
                if (s.charAt(start) == s.charAt(end) && (end - start <= 2 || dpPalindrome[start + 1][end - 1])) {
                    dpPalindrome[start][end] = true;
                    minCuts = start == 0 ? 0 : min(minCuts, dpCuts[start - 1] + 1);
                }
            }
            dpCuts[end] = minCuts;
        }

        return dpCuts[n - 1];
    }
```

### #6 Expand Around Center
```java
    // TC: O(n^2), SC: O(n)
    public int minCut_ExpandAroundCenter(String s) {
        int n = s.length(), dpCuts[] = new int[n];
        for (int i = 1; i < n; i++) dpCuts[i] = i;

        for (int mid = 0; mid < n; mid++) {
            // check for odd length palindrome around mid index
            eac(s, mid, mid, dpCuts);

            // check for even length palindrome around mid index
            eac(s, mid - 1, mid, dpCuts);
        }
        return dpCuts[n - 1];
    }

    private void eac(String s, int i, int j, int[] dpCuts) {
        for (int start = i, end = j; start >= 0 && end < s.length() && s.charAt(start) == s.charAt(end); start--, end++) {
            int newCut = start == 0 ? 0 : dpCuts[start - 1] + 1;
            dpCuts[end] = min(dpCuts[end], newCut);
        }
    }
```

## Common Pitfalls to Avoid
- N/A

---