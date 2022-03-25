
# Longest Common Substring(Grokking)
## Intro
![[Pasted image 20220321135052.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^(m+n)), SC: O(m+n)
    public int findLCSLength_BruteForce(String s1, String s2) {
        return helper_bruteForce(s1, s2, 0, 0);
    }

    private int helper_bruteForce(String s1, String s2, int i, int j) {
        if (i == s1.length() || j == s2.length()) return 0;

        if (s1.charAt(i) == s2.charAt(j)) return 1 + helper_bruteForce(s1, s2, i + 1, j + 1);
        return max(helper_bruteForce(s1, s2, i, j + 1), helper_bruteForce(s1, s2, i + 1, j));
    }
```

### #2 TopDown Memo
```java
    // TC: O(mn), SC: O(mn)
    public int findLCSLength_TopDownMemo(String s1, String s2) {
        return helper_topDown(s1, s2, 0, 0, new Integer[s1.length()][s2.length()]);
    }

    private int helper_topDown(String s1, String s2, int i, int j, Integer[][] memo) {
        if (i == s1.length() || j == s2.length()) return 0;

        if (memo[i][j] != null) return memo[i][j];

        if (s1.charAt(i) == s2.charAt(j)) return memo[i][j] = 1 + helper_topDown(s1, s2, i + 1, j + 1, memo);
        return memo[i][j] = max(helper_topDown(s1, s2, i, j + 1, memo), helper_topDown(s1, s2, i + 1, j, memo));
    }
```

### #3 DP 2D
```java
    // TC: O(mn), SC: O(mn)
    public int findLCSLength_DP_BottomUP_2D(String s1, String s2) {
        int[][] dp = new int[s1.length() + 1][s2.length() + 1];

        for (int i = 1; i < dp.length; i++) {
            for (int j = 1; j < dp[0].length; j++) {
                if (s1.charAt(i - 1) == s2.charAt(j - 1)) dp[i][j] = 1 + dp[i - 1][j - 1];
                else dp[i][j] = max(dp[i][j - 1], dp[i - 1][j]);
            }
        }

        return dp[s1.length()][s2.length()];
    }
```

### #4 DP 1D
```java
    // TC: O(mn), SC: O(n)
    public static int findLCSLength_Grokking_DP_BottomUP_1D(String s1, String s2) {
        int[][] dp = new int[2][s2.length() + 1];
        int maxLength = 0;
        for (int i = 1; i <= s1.length(); i++) {
            for (int j = 1; j <= s2.length(); j++) {
                dp[i % 2][j] = s1.charAt(i - 1) == s2.charAt(j - 1)
                        ? 1 + dp[(i - 1) % 2][j - 1] :
                        max(dp[(i - 1) % 2][j], dp[i % 2][j - 1]);
                maxLength = max(maxLength, dp[i % 2][j]);
            }
        }
        return maxLength;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Minimum Deletions and Insertions to Transform a String into another(Grokking)
## Intro
![[Pasted image 20220321135452.png]]

## Thoughts
- N/A

## Solutions
### #1 Using LCS

```java
    /**
     * This problem can easily be converted to the Longest Common Subsequence (LCS). If we can find the LCS of the two input strings, we can easily find how many characters we
     * need to insert and delete from s1. Here is how we can do this:
     * <p>
     * Let's assume len1 is the length of s1 and len2 is the length of s2.
     * Now let's assume c1 is the length of LCS of the two strings s1 and s2.
     * To transform s1 into s2, we need to delete everything from s1 which is not part of LCS, so minimum deletions we need to perform from s1 => len1 - c1
     * Similarly, we need to insert everything in s1 which is present in s2 but not part of LCS, so minimum insertions we need to perform in s1 => len2 - c1
     *
     * @param s1
     * @param s2
     */
    // TC: O(mn), SC: O(mn)
    public void findMDI(String s1, String s2) {
        int c1 = findLCS(s1, s2);
        System.out.println("Minimum deletions needed: " + (s1.length() - c1));
        System.out.println("Minimum insertions needed: " + (s2.length() - c1));
    }

    // TC: O(mn), SC: O(mn)
    private int findLCS(String s1, String s2) {
        int[][] dp = new int[s1.length() + 1][s2.length() + 1];
        for (int i = 1; i < dp.length; i++) {
            for (int j = 1; j < dp[0].length; j++) {
                if (s1.charAt(i - 1) == s2.charAt(j - 1)) dp[i][j] = 1 + dp[i - 1][j - 1];
                else dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
            }
        }
        return dp[s1.length()][s2.length()];
    }
```


## Common Pitfalls to Avoid
- N/A

---

# Maximum Sum Increasing Subsequence(Grokking)
## Intro
![[Pasted image 20220321135711.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^n), SC: O(n)
    public int findMSIS_BruteForce(int[] nums) {
        return helper(nums, -1, 0);
    }

    private int helper(int[] nums, int prevI, int i) {
        if (i >= nums.length) return 0;

        int choose = (prevI == -1 || nums[i] > nums[prevI])
                ? nums[i] + helper(nums, i, i + 1)
                : 0;
        int notChoose = helper(nums, prevI, i + 1);
        return max(choose, notChoose);
    }
```

### #2 TopDown Memo
```java
    // TC: O(n^2), SC: O(n^2)
    public int findMSIS_TopDownMemo(int[] nums) {
        int n = nums.length;
        return helper(nums, -1, 0, new Integer[n + 1][n]);
    }

    private int helper(int[] nums, int prevI, int i, Integer[][] memo) {
        if (i >= nums.length) return 0;
        if (memo[prevI + 1][i] != null) return memo[prevI + 1][i];

        int choose = (prevI == -1 || nums[i] > nums[prevI])
                ? nums[i] + helper(nums, i, i + 1, memo)
                : 0;
        int notChoose = helper(nums, prevI, i + 1, memo);
        return memo[prevI + 1][i] = max(choose, notChoose);
    }
```

### #3 DP Bottom UP
```java
    // TC: O(n^2), SC: O(n)
    public int findMSIS_Grokking_DP_BottomUP(int[] nums) {
        int[] dp = new int[nums.length];
        dp[0] = nums[0];

        int max = nums[0];
        for (int i = 1; i < nums.length; i++) {
            dp[i] = nums[i];
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) dp[i] = max(dp[i], dp[j] + nums[i]);
            }
            max = max(max, dp[i]);
        }

        return max;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Shortest Common Supersequence
## Intro
https://leetcode.com/problems/shortest-common-supersequence/
![[Pasted image 20220321135919.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java
    // TC: O(?), SC: O(?)
    public String shortestCommonSupersequence_Extreme_BruteForce(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        if (m == 0) return s2;
        if (n == 0) return s1;

        if (s1.charAt(0) == s2.charAt(0))
            return s1.charAt(0) + shortestCommonSupersequence_Extreme_BruteForce(s1.substring(1, m), s2.substring(1, n));

        String newS1 = shortestCommonSupersequence_Extreme_BruteForce(s1.substring(1, m), s2.substring(0, n));
        String newS2 = shortestCommonSupersequence_Extreme_BruteForce(s1.substring(0, m), s2.substring(1, n));

        return newS1.length() >= newS2.length()
                ? s2.charAt(0) + newS2
                : s1.charAt(0) + newS1;
    }
```

### #2 TopDown Memo
```java
    // TC: O(mn), SC: O(mn)
    public int findSCSLength_Grokking_TopDownMemo(String s1, String s2) {
        Integer[][] dp = new Integer[s1.length()][s2.length()];
        return findSCSLengthRecursive(dp, s1, s2, 0, 0);
    }

    private int findSCSLengthRecursive(Integer[][] dp, String s1, String s2, int i, int j) {
        // if we have reached the end of a string, return the remaining length of the other string,
        // as in this case we have to take all of the remaining other string
        if (i == s1.length()) return s2.length() - j;
        if (j == s2.length()) return s1.length() - i;

        if (dp[i][j] == null) {
            if (s1.charAt(i) == s2.charAt(j)) dp[i][j] = 1 + findSCSLengthRecursive(dp, s1, s2, i + 1, j + 1);
            else {
                int length1 = 1 + findSCSLengthRecursive(dp, s1, s2, i, j + 1);
                int length2 = 1 + findSCSLengthRecursive(dp, s1, s2, i + 1, j);
                dp[i][j] = min(length1, length2);
            }
        }

        return dp[i][j];
    }
```

### #3 DP Bottom UP 2D
```java
    // TC: O(mn), SC: O(mn)
    public int findSCSLength_Grokking_DP_BottomUP_2D(String s1, String s2) {
        int m = s1.length(), n = s2.length(), dp[][] = new int[m + 1][n + 1];

        // if one of the strings is of zero length, SCS would be equal to the length of the other string
        for (int i = 0; i <= m; i++) dp[i][0] = i;
        for (int j = 0; j <= n; j++) dp[0][j] = j;

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (s1.charAt(i - 1) == s2.charAt(j - 1)) dp[i][j] = 1 + dp[i - 1][j - 1];
                else dp[i][j] = 1 + min(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return dp[m][n];
    }
```

### #4 Using LCS
```java
    // TC: O(mn), SC: O(mn) * O(string)
    public String shortestCommonSupersequence(String A, String B) {
        int i = 0, j = 0;
        StringBuilder sb = new StringBuilder();
        for (char c : lcs(A, B).toCharArray()) {
            while (i < A.length() && A.charAt(i) != c) sb.append(A.charAt(i++));
            while (j < B.length() && B.charAt(j) != c) sb.append(B.charAt(j++));
            sb.append(c);
            i++;
            j++;
        }
        sb.append(A.substring(i)).append(B.substring(j));
        return sb.toString();
    }

    private String lcs(String A, String B) {
        int n = A.length(), m = B.length();
        String[][] dp = new String[n + 1][m + 1];
        for (String[] strings : dp) Arrays.fill(strings, "");

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (A.charAt(i - 1) == B.charAt(j - 1)) dp[i][j] = dp[i - 1][j - 1] + A.charAt(i - 1);
                else dp[i][j] = dp[i - 1][j].length() > dp[i][j - 1].length() ? dp[i - 1][j] : dp[i][j - 1];
            }
        }
        return dp[n][m];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Minimum Deletions To Make a Sequence Sorted(Grokking)
## Intro
![[Pasted image 20220321140129.png]]

## Thoughts
- N/A

## Solutions
### #1 DP

```java
    // TC: O(n^2), SC: O(n)
    public int findMinimumDeletions_Grokking_LIS(int[] nums) {
        // subtracting the length of LIS from the length of the input array to get minimum number of deletions
        return nums.length - findLISLength_DP(nums);
    }

    // TC: O(n^2), SC: O(n)
    private int findLISLength_DP(int[] nums) {
        int[] dp = new int[nums.length];
        dp[0] = 1;

        int maxLength = 1;
        for (int i = 1; i < nums.length; i++) {
            dp[i] = 1;
            for (int j = 0; j < i; j++)
                if (nums[i] > nums[j] && dp[i] <= dp[j]) {
                    dp[i] = dp[j] + 1;
                    maxLength = max(maxLength, dp[i]);
                }
        }
        return maxLength;
    }
```

### #2 Binary Search
```java
    // TC: O(nlogn), SC: O(n)
    public int findMinimumDeletions_MyWay_Using_LIS_BinarySearch(int[] nums) {
        return nums.length - LIS_BinarySearch(nums);
    }

    // TC: O(nlogn), SC: O(n)
    private int LIS_BinarySearch(int[] nums) {
        List<Integer> sub = new ArrayList<>();
        sub.add(nums[0]);
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > sub.get(sub.size() - 1)) sub.add(nums[i]);
            else {
                int id = Collections.binarySearch(sub, nums[i]);
                if (id < 0) id = -(id + 1);
                sub.set(id, nums[i]);
            }
        }
        return sub.size();
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Longest Repeating Sequence(Grokking)
## Intro
![[Pasted image 20220321140257.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^n), SC: O(n)
    public int findLRSLength_Grokking_BruteForce(String s) {
        return findLRSLengthRecursive(s, 0, 0);
    }

    private int findLRSLengthRecursive(String s, int i, int j) {
        if (i == s.length() || j == s.length()) return 0;

        if (i != j && s.charAt(i) == s.charAt(j)) return 1 + findLRSLengthRecursive(s, i + 1, j + 1);

        int c1 = findLRSLengthRecursive(s, i, j + 1);
        int c2 = findLRSLengthRecursive(s, i + 1, j);

        return max(c1, c2);
    }
```

### #2 TopDown Memo
```java
    // TC: O(n^2), SC: O(n^2)
    public int findLRSLength_Grokking_TopDown(String s) {
        Integer[][] memo = new Integer[s.length()][s.length()];
        return findLRSLengthRecursive(memo, s, 0, 0);
    }

    private int findLRSLengthRecursive(Integer[][] memo, String s, int i, int j) {
        if (i == s.length() || j == s.length()) return 0;

        if (memo[i][j] == null) {
            if (i != j && s.charAt(i) == s.charAt(j)) memo[i][j] = 1 + findLRSLengthRecursive(memo, s, i + 1, j + 1);
            else {
                int c1 = findLRSLengthRecursive(memo, s, i, j + 1);
                int c2 = findLRSLengthRecursive(memo, s, i + 1, j);
                memo[i][j] = max(c1, c2);
            }
        }

        return memo[i][j];
    }
```

### #3 DP Bottom UP
```java
    // TC: O(n^2), SC: O(n^2)
    public int findLRSLength_Grokking_DP_BottomUP(String s) {
        int dp[][] = new int[s.length() + 1][s.length() + 1], max = 0;
        // dp[i1][i2] will be storing the LRS up to s[0..i1-1][0..i2-1]
        // this also means that subsequences of length zero (first row and column of dp[][]),
        // will always have LRS of size zero.
        for (int i = 1; i <= s.length(); i++) {
            for (int j = 1; j <= s.length(); j++) {
                if (i != j && s.charAt(i - 1) == s.charAt(j - 1)) dp[i][j] = 1 + dp[i - 1][j - 1];
                else dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                max = max(max, dp[i][j]);
            }
        }
        return max;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Subsequence Pattern Matching(Grokking)
## Intro
![[Pasted image 20220321140422.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(2^m), SC: O(m), where m = s.length(), n = pat.length()
    public int findSPMCount_BruteForce(String s, String pat) {
        return helper(s, pat, 0, 0);
    }

    private int helper(String s, String pat, int i, int j) {
        if (j >= pat.length()) return 1;
        if (i >= s.length()) return 0;

        int match = s.charAt(i) == pat.charAt(j) ? helper(s, pat, i + 1, j + 1) : 0;
        int notMatch = helper(s, pat, i + 1, j);
        return match + notMatch;
    }
```

### #2 TopDown Memo
```java
    // TC: O(mn), SC: O(mn)
    public int findSPMCount_Grokking_TopDown(String s, String pat) {
        Integer[][] memo = new Integer[s.length()][pat.length()];
        return findSPMCountRecursive(memo, s, pat, 0, 0);
    }

    private int findSPMCountRecursive(Integer[][] memo, String s, String pat, int i, int j) {

        // if we have reached the end of the pattern
        if (j == pat.length()) return 1;

        // if we have reached the end of the string but pattern has still some characters left
        if (i == s.length()) return 0;

        if (memo[i][j] == null) {
            int c1 = s.charAt(i) == pat.charAt(j) ? findSPMCountRecursive(memo, s, pat, i + 1, j + 1) : 0;
            int c2 = findSPMCountRecursive(memo, s, pat, i + 1, j);
            memo[i][j] = c1 + c2;
        }

        return memo[i][j];
    }
```

### #3 DP Bottom UP
```java
    // TC: O(mn), SC: O(mn)
    public int findSPMCount_Grokking_DP_BottomUP(String s, String pat) {
        int n = pat.length(), m = s.length();
       
        // every empty pattern has one match
        if (n == 0) return 1;

        if (m == 0 || n > m) return 0;

        // dp[strIndex][patIndex] will be storing the count of SPM up to s[0..strIndex-1][0..patIndex-1]
        int[][] dp = new int[m + 1][n + 1];

        // for the empty pattern, we have one matching
        for (int i = 0; i <= m; i++) dp[i][0] = 1;

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (s.charAt(i - 1) == pat.charAt(j - 1)) dp[i][j] = dp[i - 1][j - 1];
                dp[i][j] += dp[i - 1][j];
            }
        }

        return dp[m][n];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Longest Bitonic Subsequence(Grokking)
## Intro
![[Pasted image 20220321140602.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java
    // TC: O(2^n), SC: O(n)
    public int findLBSLength_Grokking_BruteForce(int[] nums) {
        int max = 0;
        for (int i = 0; i < nums.length; i++) {
            int ldsToRight = findLDSLength(nums, i, -1);
            int ldsToLeft = findLDSLengthRev(nums, i, -1);
            // note subtract 1, so that you dont count the element at i twice
            max = max(max, ldsToRight + ldsToLeft - 1);
        }
        return max;
    }

    // find the longest decreasing subsequence from currentIndex till the end of the array
    private int findLDSLength(int[] nums, int currentIndex, int previousIndex) {
        if (currentIndex == nums.length) return 0;

        // include nums[currentIndex] if it is smaller than the previous number
        int c1 = previousIndex == -1 || nums[currentIndex] < nums[previousIndex]
                ? 1 + findLDSLength(nums, currentIndex + 1, currentIndex)
                : 0;

        // excluding the number at currentIndex
        int c2 = findLDSLength(nums, currentIndex + 1, previousIndex);

        return max(c1, c2);
    }

    // find the longest decreasing subsequence from currentIndex till the beginning of the array
    private int findLDSLengthRev(int[] nums, int currentIndex, int previousIndex) {
        if (currentIndex < 0) return 0;

        // include nums[currentIndex] if it is smaller than the previous number
        int c1 = previousIndex == -1 || nums[currentIndex] < nums[previousIndex]
                ? 1 + findLDSLengthRev(nums, currentIndex - 1, currentIndex)
                : 0;

        // excluding the number at currentIndex
        int c2 = findLDSLengthRev(nums, currentIndex - 1, previousIndex);

        return max(c1, c2);
    }
```

### #2 TopDown Memo
```java
    // TC: O(n^2), SC: O(n)
    public int findLBSLength_Grokking_TopDownMemo(int[] nums) {
        Integer[][] lds = new Integer[nums.length][nums.length + 1];
        Integer[][] ldsRev = new Integer[nums.length][nums.length + 1];

        int maxLength = 0;
        for (int i = 0; i < nums.length; i++) {
            int ldsToRight = findLDSLength(lds, nums, i, -1);
            int ldsToLeft = findLDSLengthReverse(ldsRev, nums, i, -1);
            maxLength = max(maxLength, ldsToRight + ldsToLeft - 1);
        }

        return maxLength;
    }

    // find the longest decreasing subsequence from currentIndex till the end of the array
    private int findLDSLength(Integer[][] memo, int[] nums, int currentIndex, int previousIndex) {
        if (currentIndex == nums.length) return 0;

        if (memo[currentIndex][previousIndex + 1] == null) {
            // include nums[currentIndex] if it is smaller than the previous number
            int c1 = previousIndex == -1 || nums[currentIndex] < nums[previousIndex]
                    ? 1 + findLDSLength(memo, nums, currentIndex + 1, currentIndex)
                    : 0;

            // excluding the number at currentIndex
            int c2 = findLDSLength(memo, nums, currentIndex + 1, previousIndex);

            memo[currentIndex][previousIndex + 1] = max(c1, c2);
        }

        return memo[currentIndex][previousIndex + 1];
    }

    // find the longest decreasing subsequence from currentIndex till the beginning of the array
    private int findLDSLengthReverse(Integer[][] memo, int[] nums, int currentIndex, int previousIndex) {
        if (currentIndex < 0) return 0;

        if (memo[currentIndex][previousIndex + 1] == null) {
            // include nums[currentIndex] if it is smaller than the previous number
            int c1 = previousIndex == -1 || nums[currentIndex] < nums[previousIndex]
                    ? 1 + findLDSLengthReverse(memo, nums, currentIndex - 1, currentIndex)
                    : 0;

            // excluding the number at currentIndex
            int c2 = findLDSLengthReverse(memo, nums, currentIndex - 1, previousIndex);

            memo[currentIndex][previousIndex + 1] = max(c1, c2);
        }
        return memo[currentIndex][previousIndex + 1];
    }
```

### #3 DP Bottom UP
```java
    // TC: O(n^2), SC: O(n)
    public int findLBSLength_Grokking_DP_BottomUP(int[] nums) {
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
        for (int i = 0; i < nums.length; i++) maxLength = max(maxLength, lds[i] + ldsReverse[i] - 1);

        return maxLength;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Wiggle Subsequence
## Intro
https://leetcode.com/problems/wiggle-subsequence/
![[Pasted image 20220321140907.png]]

## Thoughts
- N/A

## Solutions
### #1 Extreme BruteForce

```java
    // TC: O(n!), SC: O(n)
    // todo how is the above tc calculated?
    public int wiggleMaxLength_BruteForce(int[] nums) {
        return 1 + max(helper(nums, 0, true), helper(nums, 0, false));
    }

    private int helper(int[] nums, int index, boolean isUp) {
        int max = 0;
        for (int i = index + 1; i < nums.length; i++) {
            if ((isUp && nums[i] > nums[index]) || (!isUp && nums[i] < nums[index]))
                max = max(max, 1 + helper(nums, i, !isUp));
        }
        return max;
    }
```

### #2 Better BruteForce
```java
    // TC: O(2^n), SC: O(n)
    public int wiggleMaxLength_BruteForceRecursive(int[] nums) {
        return max(helper(nums, -1, 0, true), helper(nums, -1, 0, false));
    }

    private int helper(int[] nums, int prevI, int i, boolean isAsc) {
        if (i == nums.length) return 0;

        int c1 = 0;
        if (isAsc) {
            if (prevI == -1 || nums[i] > nums[prevI]) c1 = 1 + helper(nums, i, i + 1, false);
        } else if (prevI == -1 || nums[i] < nums[prevI]) c1 = 1 + helper(nums, i, i + 1, true);

        int c2 = helper(nums, prevI, i + 1, isAsc);
        return max(c1, c2);
	}
```

### #3 DP Bottom UP O(n^2) time
```java
    // TC: O(n^2), SC: O(n)
    public int wiggleMaxLength_DP_BottomUP(int[] nums) {
        int n = nums.length, up[] = new int[n], down[] = new int[n];

        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) up[i] = max(up[i], 1 + down[j]);
                else if (nums[i] < nums[j]) down[i] = max(down[i], 1 + up[j]);
            }
        }

        return 1 + max(up[n - 1], down[n - 1]);
    }
```

### #4 DP linear O(n) time
```java
    // TC: O(n), SC: O(n)
    public int wiggleMaxLength_DP_Linear(int[] nums) {
        int n = nums.length, up[] = new int[n], down[] = new int[n];

        for (int i = 1; i < n; i++) {
            if (nums[i] > nums[i - 1]) {
                up[i] = 1 + down[i - 1];
                down[i] = down[i - 1];
            } else if (nums[i] < nums[i - 1]) {
                down[i] = 1 + up[i - 1];
                up[i] = up[i - 1];
            } else {
                up[i] = up[i - 1];
                down[i] = down[i - 1];
            }
        }

        return 1 + max(up[n - 1], down[n - 1]);
    }
```

### #5 DP Space Optimized
```java
    // TC: O(n), SC: O(1)
    public int wiggleMaxLength_DP_Space_Optimized(int[] nums) {
        int n = nums.length, up = 0, down = 0;

        for (int i = 1; i < n; i++) {
            if (nums[i] > nums[i - 1]) up = 1 + down;
            else if (nums[i] < nums[i - 1]) down = 1 + up;
        }

        return 1 + max(up, down);
    }
```

### #6 Greedy Approach
```java
    // TC: O(n), SC: O(1)
    // todo how to prove this? also how does this even work.
    public int wiggleMaxLength_GreedyApproach(int[] nums) {
        if (nums.length < 2) return nums.length;

        int prevDiff = nums[1] - nums[0], count = prevDiff == 0 ? 1 : 2;
        for (int i = 2; i < nums.length; i++) {
            int diff = nums[i] - nums[i - 1];
            if ((diff > 0 && prevDiff <= 0) || (diff < 0 && prevDiff >= 0)) {
                count++;
                prevDiff = diff;
            }
        }
        return count;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Edit Distance
## Intro
https://leetcode.com/problems/edit-distance/
![[Pasted image 20220321141202.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce Recursive

```java
    // TC: O(3^(m+n)), SC: O(m+n)
    public int minDistance_BruteForceRecursive(String s1, String s2) {
        return helper(s1, s2, 0, 0);
    }

    private int helper(String s1, String s2, int i, int j) {
        if (i == s1.length()) return s2.length() - j;
        if (j == s2.length()) return s1.length() - i;

        if (s1.charAt(i) == s2.charAt(j)) return helper(s1, s2, i + 1, j + 1);

        int c1 = 1 + helper(s1, s2, i, j + 1);
        int c2 = 1 + helper(s1, s2, i + 1, j);
        int c3 = 1 + helper(s1, s2, i + 1, j + 1);

        return Math.min(c1, Math.min(c2, c3));
    }
```

### #2 TopDown Memo
```java
    // TC: O(mn), SC: O(mn)
    public int minDistance_TopDownMemo(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        return helper(s1, s2, 0, 0, new Integer[m][n]);
    }

    private int helper(String s1, String s2, int i, int j, Integer[][] memo) {
        if (i == s1.length()) return s2.length() - j;
        if (j == s2.length()) return s1.length() - i;

        if (memo[i][j] != null) return memo[i][j];

        if (s1.charAt(i) == s2.charAt(j)) return memo[i][j] = helper(s1, s2, i + 1, j + 1, memo);

        int c1 = 1 + helper(s1, s2, i, j + 1, memo);
        int c2 = 1 + helper(s1, s2, i + 1, j, memo);
        int c3 = 1 + helper(s1, s2, i + 1, j + 1, memo);

        return memo[i][j] = Math.min(c1, Math.min(c2, c3));
    }
```

### #3 DP 2D
```java
    // TC: O(mn), SC: O(mn)
    public int minDistance_DP_BottomUP_2D(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 0; i <= m; i++) dp[i][0] = i;
        for (int j = 0; j <= n; j++) dp[0][j] = j;

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                char c1 = s1.charAt(i - 1), c2 = s2.charAt(j - 1);
                if (c1 == c2) dp[i][j] = dp[i - 1][j - 1];
                else dp[i][j] = 1 + Math.min(dp[i][j - 1], Math.min(dp[i - 1][j], dp[i - 1][j - 1]));
            }
        }

        return dp[m][n];
    }
```

### #4 DP 1D
```java
    // TC: O(mn), SC: O(max(m,n))
    // we can further optimize SC to min(m, n) by swapping s1 and s2 depending on which is larger or smaller
    public int minDistance_DP_BottomUP_1D(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        int[][] dp = new int[2][n + 1];

        for (int j = 0; j <= n; j++) dp[0][j] = j;

        for (int i = 1; i <= m; i++) {
            // note the below line is very important. Understand why you need to add it next time
            dp[i % 2][0] = i;
            for (int j = 1; j <= n; j++) {
                char c1 = s1.charAt(i - 1), c2 = s2.charAt(j - 1);
                if (c1 == c2) dp[i % 2][j] = dp[(i - 1) % 2][j - 1];
                else
                    dp[i % 2][j] = 1 + Math.min(dp[i % 2][j - 1], Math.min(dp[(i - 1) % 2][j], dp[(i - 1) % 2][j - 1]));
            }
        }

        return dp[(m) % 2][n];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Interleaving String
## Intro
https://leetcode.com/problems/interleaving-string/
![[Pasted image 20220321141345.png]]
![[Pasted image 20220321141349.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java
    // TC: O(2^(n+m)), SC: O(n+m)
    public boolean isInterleave_BruteForceRecursive(String s1, String s2, String s3) {
        return (s3.length() == s1.length() + s2.length())
                && canBeInterwoven(s1, s2, s3, 0, 0);

    }

    private boolean canBeInterwoven(String s1, String s2, String s3, int i, int j) {
        int k = i + j;

        return (k == s3.length())
                || (i < s1.length() && s1.charAt(i) == s3.charAt(k) && canBeInterwoven(s1, s2, s3, i + 1, j))
                || (j < s2.length() && s2.charAt(j) == s3.charAt(k) && canBeInterwoven(s1, s2, s3, i, j + 1));
    }
```

### #2 TopDown Memo
```java
    // TC: O(mn), SC: O(mn)
    public boolean isInterleave_TopDown(String s1, String s2, String s3) {
        return (s3.length() == s1.length() + s2.length())
                && canBeInterwoven(s1, s2, s3, 0, 0, new Boolean[s1.length() + 1][s2.length() + 1]);

    }

    private boolean canBeInterwoven(String s1, String s2, String s3, int i, int j, Boolean[][] memo) {
        if (memo[i][j] != null) return memo[i][j];

        int k = i + j;

        return memo[i][j] = (k == s3.length())
                || (i < s1.length() && s1.charAt(i) == s3.charAt(k) && canBeInterwoven(s1, s2, s3, i + 1, j, memo))
                || (j < s2.length() && s2.charAt(j) == s3.charAt(k) && canBeInterwoven(s1, s2, s3, i, j + 1, memo));
    }
```

### #3 DP 2D
```java
    // TC: O(mn), SC: O(mn)
    public boolean isInterleave_DP_BottomUP_2D(String s1, String s2, String s3) {
        int m = s1.length(), n = s2.length();
        if (s3.length() != m + n) return false;

        boolean[][] dp = new boolean[m + 1][n + 1];
        for (int i = 0; i <= m; i++) {
            for (int j = 0; j <= n; j++) {
                if (i == 0 && j == 0) dp[i][j] = true;
                else if (i == 0) dp[i][j] = dp[i][j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1);
                else if (j == 0) dp[i][j] = dp[i - 1][j] && s1.charAt(i - 1) == s3.charAt(i + j - 1);
                else
                    dp[i][j] = (dp[i - 1][j] && s1.charAt(i - 1) == s3.charAt(i + j - 1)) || (dp[i][j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1));
            }
        }
        return dp[m][n];
	}
```

### #4 DP 1D
```java
    // TC: O(mn), SC: O(n)
    public boolean isInterleave_DP_BottomUP_1D(String s1, String s2, String s3) {
        int m = s1.length(), n = s2.length();
        if (s3.length() != m + n) return false;

        boolean[] dp = new boolean[n + 1];
        for (int i = 0; i <= m; i++) {
            for (int j = 0; j <= n; j++) {
                if (i == 0 && j == 0) dp[j] = true;
                else if (i == 0) dp[j] = dp[j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1);
                else if (j == 0) dp[j] = dp[j] && s1.charAt(i - 1) == s3.charAt(i + j - 1);
                else
                    dp[j] = (dp[j] && s1.charAt(i - 1) == s3.charAt(i + j - 1)) || (dp[j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1));
            }
        }
        return dp[n];
    }
```

## Common Pitfalls to Avoid
- N/A

---