
# Remove Duplicates From Sorted Array
## Intro
https://leetcode.com/problems/remove-duplicates-from-sorted-array/
![[Pasted image 20220322121524.png]]
![[Pasted image 20220322121533.png]]


## Thoughts
- N/A

## Solutions
### #1 For Loop
```java
    // TC: O(n), SC: O(1)
    public int removeDuplicates_MineAnotherWay(int[] A) {
        int k = 0;
        for (int i = 0; i < A.length; i++) {
            if (i == 0 || A[i] != A[i - 1]) A[k++] = A[i];
        }
        return k;
    }

    // TC: O(n), SC: O(1)
    public static int remove_Mine(int[] A) {
        if (A.length <= 1) return A.length;
        int k = 0;
        for (int i = 1; i < A.length; i++) {
            if (A[k] != A[i]) A[++k] = A[i];
        }
        return k + 1;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Squares of A Sorted Array
## Intro
https://leetcode.com/problems/squares-of-a-sorted-array/
![[Pasted image 20220322121621.png]]
## Thoughts
- N/A

## Solutions
### #1 Sort
```java
    // TC: O(nlogn), SC: O(1)
    public int[] sortedSquares_SortApproach(int[] nums) {
        for (int i = 0; i < nums.length; i++) nums[i] *= nums[i];
        Arrays.sort(nums);
        return nums;
    }
```

### #2 Two Pointer
```java
    // TC: O(n + n), SC: O(n), SC coz of extra res array.
    public int[] sortedSquares_TwoPointer(int[] nums) {
        int i = -1;
        for (int k = 0; k < nums.length; k++) {
            if (nums[k] < 0) i = k;
            else if (nums[k] >= 0) break;
        }

        int j = i + 1, k = 0, res[] = new int[nums.length];
        while (i >= 0 && j < nums.length) {
            int left = nums[i] * nums[i], right = nums[j] * nums[j];
            if (left <= right) {
                res[k++] = left;
                i--;
            } else {
                res[k++] = right;
                j++;
            }
        }
        while (i >= 0) res[k++] = nums[i] * nums[i--];
        while (j < nums.length) res[k++] = nums[j] * nums[j++];
        return res;
    }
```

### #3 Two Pointer Better
```java
    // TC: O(n), SC: O(n)
    public int[] sortedSquares_TwoPointer_Better(int[] nums) {
        int i = 0, j = nums.length - 1, k = nums.length - 1, res[] = new int[nums.length];
        while (i <= j) {
            int left = nums[i] * nums[i], right = nums[j] * nums[j];
            if (left >= right) {
                res[k--] = left;
                i++;
            } else {
                res[k--] = right;
                j--;
            }
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# 3Sum Closest
## Intro
https://leetcode.com/problems/3sum-closest/
![[Pasted image 20220322121728.png]]

## Thoughts
- N/A

## Solutions
### #1 Two Variables
```java
    // TC: O(n^2), SC: O(logn or n), SC depends on sorting algorithm
    public int threeSumClosest_TwoVariables(int[] A, int target) {
        Arrays.sort(A);
        int diff = Integer.MAX_VALUE, closestSum = 0;
        for (int i = 0; i < A.length - 2; i++) {
            int l = i + 1, r = A.length - 1;
            while (l < r) {
                int sum = A[i] + A[l] + A[r], newDiff = Math.abs(target - sum);
                if (newDiff == 0) return sum;

                if (newDiff <= diff) {
                    diff = newDiff;
                    closestSum = sum;
                }
                if (sum < target) l++;
                else r--;
            }
        }
        return closestSum;
    }
```

### #2 Better
```java
    // TC: O(n^2), SC: O(logn or n), SC depends on sorting algorithm
    public int threeSumClosest_Better(int[] A, int target) {
        Arrays.sort(A);
        int diff = Integer.MAX_VALUE;
        for (int i = 0; i < A.length; i++) {
            int l = i + 1, r = A.length - 1;
            if (diff == 0) break;
            while (l < r) {
                int sum = A[i] + A[l] + A[r], newDiff = Math.abs(target - sum);
                if (newDiff < Math.abs(diff)) diff = target - sum;

                if (sum < target) l++;
                else r--;
            }
        }
        return target - diff;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Subarray Product Less than K
## Intro
https://leetcode.com/problems/subarray-product-less-than-k/
![[Pasted image 20220322122002.png]]
## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n^3), SC: O(1)
    public int numSubarrayProductLessThanK_BruteForce(int[] nums, int k) {
        int count = 0;
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j <= nums.length; j++) {
                if (satisfy(nums, k, i, j)) count++;
            }
        }
        return count;
    }

    private boolean satisfy(int[] nums, int k, int start, int end) {
        int prod = 1;
        // early break of prod < k to avoid overflows
        for (int i = start; i < end && prod < k; i++) prod *= nums[i];
        return prod < k;
    }
	```

### #2 Binary Search On Logarithms
```java
    // TC: O(nlogn), SC: O(n)
    public int numSubarrayProductLessThanK_BinarySearchOnLogarithms(int[] nums, int k) {
        if (k == 0) return 0;

        double prefix[] = new double[nums.length + 1], logk = Math.log(k);
        for (int i = 0; i < nums.length; i++) prefix[i + 1] = prefix[i] + Math.log(nums[i]);

        int ans = 0;
        for (int i = 0; i < prefix.length; i++) {
            int lo = i + 1, hi = prefix.length;
            while (lo < hi) {
                int mid = lo + (hi - lo) / 2;
                if (prefix[mid] < (prefix[i] + logk) - Double.MIN_VALUE) lo = mid + 1;
                else hi = mid;
            }
            ans += lo - (i + 1);
        }
        return ans;
    }
```

### #3 Sliding Window
```java
    // TC: O(n), SC: O(1)
    // TC: O(n), SC: O(1)
    public int numSubarrayProductLessThanK_SlidingWindow(int[] nums, int k) {
        // note the difference if condition. Fails on A=[1,2,3], k = 0, hence this is needed.
        // alternatively you could also have in i <= j condition in while as shown in above method.
        if (k <= 1) return 0;
        int prod = 1, ans = 0, i = 0;
        for (int j = 0; j < nums.length; j++) {
            prod *= nums[j];
            while (prod >= k) prod /= nums[i++];
            ans += (j - i + 1);
        }
        return ans;
    }
```

### #4 Sliding Window Another Way
```java
    // TC: O(n), SC: O(1)
    public int numSubarrayProductLessThanK_SlidingWindow_AnotherWay(int[] A, int k) {
        int count = 0, i = 0, prod = 1;
        for (int j = 0; j < A.length; j++) {
            prod *= A[j];
            // note the difference i <= j. Fails on A=[1,2,3], k = 0, hence this is needed.
            // alternatively you could also have if(k<=r) return 0 as shown in below method.
            while (i <= j && prod >= k) prod /= A[i++];
            count += j - i + 1;
        }
        return count;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Sort Colors
## Intro
https://leetcode.com/problems/sort-colors/
![[Pasted image 20220322122126.png]]

## Thoughts
- N/A

## Solutions
### #1 One Pass
```java
    // TC: O(n), SC: O(1)
    public void sortColors_OnePass(int[] nums) {
        int l = 0, r = nums.length - 1, i = 0;
        while (i <= r) {
            if (nums[i] == 0) swap(nums, i++, l++);
            else if (nums[i] == 2) swap(nums, i, r--);
            else i++;
        }
    }
```

## Common Pitfalls to Avoid
- N/A

---

# 4Sum
## Intro
https://leetcode.com/problems/4sum/
![[Pasted image 20220322122240.png]]

## Thoughts
- N/A

## Solutions
### #1 Sort
```java
    // TC: O(nlogn + n^3), SC: O(n)
    public List<List<Integer>> fourSum(int[] arr, int target) {
        List<List<Integer>> quadruplets = new ArrayList<>();
        Arrays.sort(arr);
        for (int i = 0; i < arr.length - 3; i++) {
            if (i > 0 && arr[i] == arr[i - 1]) continue;
            for (int j = i + 1; j < arr.length - 2; j++) {
                if (j > i + 1 && arr[j] == arr[j - 1]) continue;
                search(arr, target, i, j, quadruplets);
            }
        }
        return quadruplets;
    }

    private void search(int[] arr, int target, int first, int second, List<List<Integer>> quadruplets) {
        int left = second + 1, right = arr.length - 1;
        while (left < right) {
            int sum = arr[first] + arr[second] + arr[left] + arr[right];
            if (sum == target) {
                quadruplets.add(Arrays.asList(arr[first], arr[second], arr[left], arr[right]));
                left++;
                right--;
                while (left < right && arr[left] == arr[left - 1]) left++;
                while (left < right && arr[right] == arr[right + 1]) right--;
            } else if (sum < target) left++;
            else right--;
        }
    }
```

### #2 KSum Generalized
```java
    // TC: O(nlogn + n^3), SC: O(n)
    public List<List<Integer>> fourSum_KsumWay(int[] A, int target) {
        return kSum(A, 4, target);
    }

    private List<List<Integer>> kSum(int[] A, int k, int target) {
        Arrays.sort(A);
        if (k <= 1) {
            List<List<Integer>> res = new ArrayList<>();
            if (Arrays.binarySearch(A, target) >= 0) res.add(Collections.singletonList(target));
            return res;
        }
        return kSum(A, k, target, 0);
    }

    private List<List<Integer>> kSum(int[] A, int k, int target, int beginIdx) {
        int n = A.length;
        if (k == 2) return twoSum(A, target, beginIdx, n);

        List<List<Integer>> res = new ArrayList<>();
        for (int i = beginIdx; i <= n - k; i++) {
            if (i > beginIdx && A[i] == A[i - 1]) continue;

            List<List<Integer>> sub = kSum(A, k - 1, target - A[i], i + 1);
            for (List<Integer> list : sub) list.add(0, A[i]);
            res.addAll(sub);
        }
        return res;
    }

    private List<List<Integer>> twoSum(int[] A, int target, int beginIdx, int n) {
        List<List<Integer>> res = new ArrayList<>();
        int l = beginIdx, r = n - 1;
        while (l < r) {
            int sum = A[l] + A[r];
            if (sum == target) {
                res.add(new ArrayList<>(List.of(A[l++], A[r--])));
                while (l < r && A[l] == A[l - 1]) l++;
                while (l < r && A[r] == A[r + 1]) r--;
            } else if (sum < target) l++;
            else r--;
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Backspace String Compare
## Intro
https://leetcode.com/problems/backspace-string-compare/
![[Pasted image 20220322122347.png]]

## Thoughts
- N/A

## Solutions
### #1 Stack
```java
    // TC: O(s + t), SC: O(s + t)
    public boolean backspaceCompare_Stack_AnotherWay(String s, String t) {
        return getStackString(s).equals(getStackString(t));
    }

    private String getStackString(String s) {
        Stack<Character> stack = new Stack<>();
        for (char c : s.toCharArray()) {
            if (c != '#') stack.push(c);
            else if (!stack.isEmpty()) stack.pop();
        }
        return stack.toString();
    }

    // TC: O(s + t), SC: O(s + t)
    public boolean backspaceCompare_StackApproach(String s, String t) {
        Stack<Character> stackS = getStack(s), stackT = getStack(t);
        if (stackS.size() != stackT.size()) return false;

        while (!stackS.isEmpty()) if (stackS.pop() != stackT.pop()) return false;
        return true;
    }

    private Stack<Character> getStack(String s) {
        Stack<Character> stack = new Stack<>();
        for (char c : s.toCharArray()) {
            if (c != '#') stack.push(c);
            else if (!stack.isEmpty()) stack.pop();
        }
        return stack;
    }
```

### #2 LC O(1) Space
```java
    // TC: O(s + t), SC: O(1)
    public boolean backspaceCompare_LCWay(String s, String t) {
        int i = s.length() - 1, j = t.length() - 1;
        int skipS = 0, skipT = 0;

        while (i >= 0 || j >= 0) {
            while (i >= 0) {
                if (s.charAt(i) == '#') {
                    skipS++;
                    i--;
                } else if (skipS > 0) {
                    skipS--;
                    i--;
                } else break;
            }

            while (j >= 0) {
                if (t.charAt(j) == '#') {
                    skipT++;
                    j--;
                } else if (skipT > 0) {
                    skipT--;
                    j--;
                } else break;
            }

            if (i >= 0 && j >= 0 && s.charAt(i) != t.charAt(j)) return false;

            // if (((i >= 0) ^ (j >= 0))) return false;
            // != is same as xor operator as shown above
            if ((i >= 0) != (j >= 0)) return false;

            i--;
            j--;
        }
        return true;
    }
```

### #3 Mine O(1) Space Better
```java
    // TC: O(s + t), SC: O(1)
    public boolean backspaceCompare_Better(String s, String t) {
        int i = s.length() - 1, j = t.length() - 1;

        while (i >= 0 || j >= 0) {
            i = nextNonSkipChar(s, i);
            j = nextNonSkipChar(t, j);

            // if((i >= 0 && j < 0) || (j >= 0 && i < 0)) return false;
            // ^^another way of writing the below condition. If one of the pointers is valid, but the other is no longer valid(i.e, out of bounds) then return false
            if (((i >= 0) ^ (j >= 0))) return false;

            if (i >= 0 && j >= 0 && s.charAt(i--) != t.charAt(j--)) return false;
        }
        return true;
    }

    private int nextNonSkipChar(String s, int i) {
        int skip = 0;
        while (i >= 0) {
            if (s.charAt(i) == '#') {
                skip++;
                i--;
            } else if (skip > 0) {
                skip--;
                i--;
            } else break;
        }
        return i;
    }
```

### #4 StringBuilder Approach
```java
    // TC: O(?), SC: O(?), not sure about StringBuilder methods time complexity and equals method time complexity
    public boolean backspaceCompare_StringBuilderApproach(String s, String t) {
        return getString(s).equals(getString(t));
    }

    private String getString(String s) {
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (c == '#') {
                if (sb.length() != 0) sb.deleteCharAt(sb.length() - 1);
            } else sb.append(c);
        }
        return sb.toString();
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Shortest Unsorted Continuous Subarray
## Intro
https://leetcode.com/problems/shortest-unsorted-continuous-subarray/

## Thoughts
- N/A

## Solutions
### #1 isOutOfOrder
```java
    // TC: O(n), SC: O(1)
    public int findUnsortedSubarray_Mine(int[] nums) {
        if (nums.length <= 1) return 0;
        int min = Integer.MAX_VALUE, max = Integer.MIN_VALUE;
        for (int i = 0; i < nums.length; i++) {
            if (isOutOfOrder(nums, i)) {
                min = min(min, nums[i]);
                max = max(max, nums[i]);
            }
        }

        if (min == Integer.MAX_VALUE) return 0;

        int i = 0, j = nums.length - 1;
        while (min >= nums[i]) i++;
        while (max <= nums[j]) j--;
        return j - i + 1;
    }

    private boolean isOutOfOrder(int[] nums, int i) {
        if (i == 0) return nums[i] > nums[i + 1];
        if (i == nums.length - 1) return nums[i] < nums[i - 1];
        return nums[i] > nums[i + 1] || nums[i] < nums[i - 1];
    }
```

## Common Pitfalls to Avoid
- N/A

---