# Two Pointers

# [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)

## Intro

![Untitled](1_Blind75/Two%20Pointe%20151da/Untitled.png)

## Thoughts

- Just code according to question description. A palindrome is something that reads same from front and also back. So just code based on palindrome’s definition. Pretty straightforward.

## Solutions

### #1 Two Pointers

```java
// TC: O(n), SC: O(1)
public boolean isPalindrome_TwoPointers(String s) {
  for (int i = 0, j = s.length() - 1; i < j; i++, j--) {
      while (i < j && !Character.isLetterOrDigit(s.charAt(i))) i++;
      while (i < j && !Character.isLetterOrDigit(s.charAt(j))) j--;

      if (Character.toLowerCase(s.charAt(i)) != Character.toLowerCase(s.charAt(j))) return false;
  }
  return true;
}

// TC: O(n), SC: O(1)
public boolean isPalindrome_AnotherWay(String s) {
  int l = 0, r = s.length() - 1;
  while (l < r) {
      char a = Character.toLowerCase(s.charAt(l));
      char b = Character.toLowerCase(s.charAt(r));
      if (!Character.isLetterOrDigit(a)) {
          l++;
          continue;
      }
      if (!Character.isLetterOrDigit(b)) {
          r--;
          continue;
      }

      if (a != b) return false;
      l++;
      r--;
  }
  return true;
}
```

## Common Pitfalls to Avoid

- Understand the question requirements carefully. In this question, the input string can contain digits, punctuation(like comma etc), spaces and also upper and lower case letters. Consider all of these things. Even thought question is very easy, ask clarifying questions and gather all requirements early on. Some companies have failed people because they are “reactive”(towards bugs like these) instead of being “proactive”.

# [3Sum](https://leetcode.com/problems/3sum/)

## Intro

![Untitled](1_Blind75/Two%20Pointe%20151da/Untitled%201.png)

## Thoughts

- This is an extension of two sum. Use sorting.

## Solutions

### #1 Using Two Sum

```java
// TC: O(n^2), SC: O(n)
public List<List<Integer>> threeSum_Sort_TwoPointers(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> res = new ArrayList<>();
    for (int i = 0; i < nums.length && nums[i] <= 0; i++) {
        if (i == 0 || nums[i - 1] != nums[i]) twoSum(nums, i, res);
    }
    return res;
}

void twoSum(int[] nums, int i, List<List<Integer>> res) {
    Set<Integer> seen = new HashSet<>();
    for (int j = i + 1; j < nums.length; j++) {
        int complement = -(nums[i] + nums[j]);
        if (seen.contains(complement)) {
            res.add(Arrays.asList(nums[i], nums[j], complement));
            while (j < nums.length - 1 && nums[j] == nums[j + 1]) j++;
        }
        seen.add(nums[j]);
    }
}
```

### #2 Using No Sorting and HashSet

```java
// TC: O(n^2), SC: O(n)
// btw this is very slow on LC
public List<List<Integer>> threeSum_NoSort(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    Set<Pair<Integer, Integer>> found = new HashSet<>();
    for (int i = 0; i < nums.length; i++) {
        Set<Integer> seen = new HashSet<>();
        for (int j = i + 1; j < nums.length; j++) {
            int complement = -(nums[i] + nums[j]);
            if (seen.contains(complement)) {
                int min = min(nums[i], min(nums[j], complement));
                int max = max(nums[i], max(nums[j], complement));
                Pair<Integer, Integer> pair = new Pair<>(min, max);
                if (!found.contains(pair)) {
                    res.add(new ArrayList<>(Arrays.asList(nums[i], nums[j], complement)));
                    found.add(pair);
                }
            }
            seen.add(nums[j]);
        }
    }
    return res;
}
```

### #3 Sorting

```java
// This is quite fast on leetcode
// TC: O(n^2), SC: O(1)
public List<List<Integer>> threeSum_BestSortApproach(int[] A) {
    List<List<Integer>> res = new ArrayList<>();
    Arrays.sort(A);
    for (int i = 0; i < A.length - 2; i++) {
        if (i > 0 && A[i] == A[i - 1]) continue;
        int l = i + 1, r = A.length - 1;
        while (l < r) {
            int sum = A[i] + A[l] + A[r];
            if (sum < 0) l++;
            else if (sum > 0) r--;
            else {
                res.add(Arrays.asList(A[i], A[l], A[r]));
                while (l < r && A[l] == A[l + 1]) l++;
                while (l < r && A[r] == A[r - 1]) r--;
                l++;
                r--;
            }
        }
    }
    return res;
}

// TC: O(n^2), SC: O(1)
public List<List<Integer>> threeSum_Sort_MyWay(int[] A) {
    Arrays.sort(A);
    List<List<Integer>> res = new ArrayList<>();
    for (int i = 0; i < A.length; i++) {
        if (i > 0 && A[i] == A[i - 1]) continue;

        int l = i + 1, r = A.length - 1;
        while (l < r) {
            if (l > i + 1 && A[l] == A[l - 1]) {
                l++;
                continue;
            }
            if (r < A.length - 1 && A[r] == A[r + 1]) {
                r--;
                continue;
            }

            int sum = A[i] + A[l] + A[r];
            if (sum == 0) res.add(List.of(A[i], A[l++], A[r--]));
            else if (sum < 0) l++;
            else r--;
        }
    }
    return res;
}
```

## Common Pitfalls to Avoid

- Be careful with duplicates and re-read on why you need multiple inner loops on conditions such as A[i] == A[i-1] or A[i] == A[i + 1] etc.

# [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)

## Intro

![Untitled](1_Blind75/Two%20Pointe%20151da/Untitled%202.png)

![Untitled](1_Blind75/Two%20Pointe%20151da/Untitled%203.png)

## Thoughts

- BruteForce for this is pretty straightfoward.
- The immediate next thought could be two pointers. l = 0, r = n - 1. The width in this case would be the maximum. So the minimum height bar would only contribute now, there is no way for the min height bar to contribute for smaller(inner) widths. Therefore its better to move the pointer for min height bar.
- As you keep going inwards, the width will only keep decreasing anyway. So the min height bar even if it gets considered for inner widths, the area will only be smaller, so no point in considering min height bar again. Essentially we are pruning all those comparisons.

## Solutions

### #1 BruteForce

```java
// TC: O(n^2), SC: O(1)
public int maxArea_BruteForce(int[] height) {
    int max = 0;
    for (int i = 0; i < height.length; i++) {
        for (int j = i; j < height.length; j++) {
            int min = Math.min(height[i], height[j]);
            max = Math.max(max, (j - i) * min);
        }
    }
    return max;
}
```

### #2 Two Pointers

```java
// TC: O(n), SC: O(1)
public int maxArea_TwoPointer(int[] height) {
    int max = 0, lo = 0, hi = height.length - 1;
    while (lo < hi) {
        if (height[lo] < height[hi]) {
            max = Math.max(max, (hi - lo) * height[lo]);
            lo++;
        } else {
            max = Math.max(max, (hi - lo) * height[hi]);
            hi--;
        }
    }
    return max;
}
```

## Common Pitfalls to Avoid

- N/A