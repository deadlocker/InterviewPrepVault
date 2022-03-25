
# Binary Search
## Intro
https://leetcode.com/problems/binary-search/
![[Pasted image 20220322101202.png]]

## Thoughts
- N/A

## Solutions
### #1 Iterative

```java
    // TC: O(logn), SC: O(1)
    public int search_Iterative(int[] nums, int target) {
        int lo = 0, hi = nums.length - 1;
        while (lo <= hi) {
//            int mid = (lo + hi) / 2;
            int mid = lo + (hi - lo) / 2; // <-- this is better and it avoids int overflow
            if (nums[mid] == target) return mid;
            if (nums[mid] > target) hi = mid - 1;
            else lo = mid + 1;
        }
        return -1;
    }
```

### #2 Recursive
```java
    // TC: O(logn), SC: O(1)
    public int search_Recursive(int[] nums, int target) {
        return helper(nums, target, 0, nums.length - 1);
    }

    private int helper(int[] nums, int target, int lo, int hi) {
        if (lo <= hi) {
//            int mid = (lo + hi) / 2;
            int mid = lo + (hi - lo) / 2; // <-- this is better and it avoids int overflow
            if (nums[mid] == target) return mid;
            if (nums[mid] > target) return helper(nums, target, lo, mid - 1);
            return helper(nums, target, mid + 1, hi);
        }
        return -1;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Celing of a Number(Grokking)
## Intro
![[Pasted image 20220322101302.png]]

## Thoughts
- N/A

## Solutions
### #1 Iterative

```java
    // TC: O(logn), SC: O(1)
    public static int searchCeilingOfANumber_MyWay(int[] arr, int key) {
        int l = 0, r = arr.length - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (arr[mid] < key) l = mid + 1;
            else r = mid;
        }
        return arr[l] >= key ? l : -1;
    }

    // TC: O(logn), SC: O(1)
    public static int searchCeilingOfANumber_Grokking(int[] arr, int key) {
        // if the 'key' is bigger than the biggest element
        if (key > arr[arr.length - 1]) return -1;

        int l = 0, r = arr.length - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (key < arr[mid]) r = mid - 1;
            else if (key > arr[mid]) l = mid + 1;
            else { // found the key
                return mid;
            }
        }
        // since the loop is running until 'l <= r', so at the r of the while loop, 'l == r+1'
        // we are not able to find the element in the given array, so the next big number will be arr[l]
        return l;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Floor of a number
## Intro
![[Pasted image 20220322101358.png]]

## Thoughts
- N/A

## Solutions
### #1 Iterative

```java
    // TC: O(logn), SC: O(1)
    public static int searchFloorOfANumber_Grokking(int[] arr, int key) {
        // if the 'key' is smaller than the smallest element
        if (key < arr[0]) return -1;

        int l = 0, r = arr.length - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (key < arr[mid]) r = mid - 1;
            else if (key > arr[mid]) l = mid + 1;
            else return mid;
        }
        // since the loop is running until 'l <= r', so at the r of the while loop, 'l == r+1'
        // we are not able to find the element in the given array, so the next smaller number will be arr[r]
        return r;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find Smallest Letter Greater Than Target
## Intro
https://leetcode.com/problems/find-smallest-letter-greater-than-target/
![[Pasted image 20220322101529.png]]

## Thoughts
- N/A

## Solutions
### #1 Linear Search

```java
    // TC: O(n), SC: O(1)
    public char nextGreatestLetter_BruteForce(char[] A, char target) {
        for (char c : A) if (c > target) return c;
        return A[0];
    }
```

### #2 Iterative Binary Search 4 Ways
```java
    // TC: O(logn), SC: O(1)
    public char nextGreatestLetter_MyWay2(char[] A, char key) {
        int l = 0, r = A.length - 1;
        if (A[r] <= key) return A[0];
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (A[mid] <= key) l = mid + 1;
            else r = mid;
        }
        return A[l];
    }

    // TC: O(logn), SC: O(1)
    public char nextGreatestLetter_AnotherWay(char[] A, char key) {
        int l = 0, r = A.length - 1, n = A.length;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (A[mid] <= key) l = mid + 1;
            else r = mid;
        }
        return A[l] > key ? A[l] : A[(l + 1) % n];
    }

    // TC: O(logn), SC: O(1)
    public char nextGreatestLetter_BinarySearch_Better(char[] A, char target) {
        int l = 0, r = A.length;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (A[mid] <= target) l = mid + 1;
            else r = mid;
        }
        return A[l % A.length];
    }

    // TC: O(logn), SC: O(1)
    public char nextGreatestLetter_BinarySearch_MYWAY(char[] A, char target) {
        int l = 0, r = A.length - 1, ans = 0;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (A[mid] <= target) {
                ans = mid + 1;
                l = mid + 1;
            } else {
                ans = mid;
                r = mid - 1;
            }
        }
        if (ans >= A.length) return A[0];
        return A[ans];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find First and Last Position of Element in Sorted Array
## Intro
https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/
![[Pasted image 20220322101642.png]]

## Thoughts
- N/A

## Solutions
### #1 LC Way Binary Search

```java
    // TC: O(logn), SC: O(1)
    public int[] searchRange_LeetcodeWay(int[] nums, int target) {
        int leftId = searchRange(nums, target, true);

        if (leftId == nums.length || nums[leftId] != target) return new int[]{-1, -1};

        return new int[]{leftId, searchRange(nums, target, false) - 1};
    }

    private int searchRange(int[] nums, int target, boolean left) {
        int lo = 0, hi = nums.length;

        while (lo < hi) {
            int mid = (lo + hi) / 2;
            if (nums[mid] > target || (left && target == nums[mid])) hi = mid;
            else lo = mid + 1;
        }
        return lo;
    }
```

### #2 My Way Binary Search
```java
    // TC: O(logn), SC: O(1)
    public int[] searchRange_MyWay2(int[] A, int key) {
        return new int[]{findFirst(A, key, true), findFirst(A, key, false)};
    }

    private int findFirst(int[] A, int key, boolean findFirst) {
        int l = 0, r = A.length - 1, ans = -1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (A[mid] == key) {
                ans = mid;
                if (findFirst) r = mid - 1;
                else l = mid + 1;
            } else if (A[mid] > key) r = mid - 1;
            else l = mid + 1;
        }
        return ans;
    }

    // TC: O(logn), SC: O(1)
    public int[] searchRange_MyWay(int[] nums, int target) {
        int firstPos = helper(nums, target, true);
        int lastPos = helper(nums, target, false);
        return new int[]{firstPos, lastPos};
    }

    private int helper(int[] nums, int target, boolean moveLeft) {
        int position = -1, left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] == target) {
                position = mid;
                if (moveLeft) right = mid - 1;
                else left = mid + 1;
            } else if (nums[mid] < target) left = mid + 1;
            else right = mid - 1;
        }
        return position;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Search In A Sorted Array of Unknown Size
## Intro
https://leetcode.com/problems/search-in-a-sorted-array-of-unknown-size/
![[Pasted image 20220322101803.png]]
![[Pasted image 20220322101808.png]]

## Thoughts
- N/A

## Solutions
### #1 Binary Search Iterative

```java
    // TC: O(log T + log T), SC: O(1), where T is index of target value
    public int search(ArrayReader reader, int key) {
        int l = 0, r = 1;
        while (reader.get(r) < key) {
            int newL = r + 1;
            r += (r - l + 1) * 2;
            l = newL;
        }
        return binarySearch(reader, key, l, r);
    }

    private int binarySearch(ArrayReader reader, int key, int l, int r) {
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (reader.get(mid) == key) return mid;
            if (reader.get(mid) < key) l = mid + 1;
            else r = mid - 1;
        }
        return -1;
    }

    interface ArrayReader {
        int get(int index);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find Peak Element
## Intro
https://leetcode.com/problems/find-peak-element/
![[Pasted image 20220322101908.png]]

## Thoughts
- N/A

## Solutions
### #1 Linear Scan

```java
    // TC: O(n), SC: O(1)
    public int findPeakElement_LinearScan(int[] nums) {
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] > nums[i + 1]) return i;
        }
        return nums.length - 1;
    }

    // TC: O(n), SC: O(1)
    public int findPeakElement_LinearScanAnotherWay(int[] A) {
        if (A.length == 1) return 0;
        for (int i = 0; i < A.length; i++) {
            if (isOutOfOrder(A, i)) return i;
        }
        return -1;
    }

    private boolean isOutOfOrder(int[] A, int i) {
        if (i == 0) return A[i] > A[i + 1];
        if (i == A.length - 1) return A[i] > A[i - 1];
        return A[i] > A[i - 1] && A[i] > A[i + 1];
    }
```

### #2 Binary Search
```java
    // TC: O(logn), SC: O(1)
    public int findPeakElement_BinarySearch(int[] A) {
        int l = 0, r = A.length - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (A[mid] > A[mid + 1]) r = mid;
            else l = mid + 1;
        }
        return l;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Minimum Difference Element
## Intro
![[Pasted image 20220322102008.png]]

## Thoughts
- N/A

## Solutions
### #1 BS My way

```java
    // todo check if this works on all cases
    // TC: O(logn), SC: O(1)
    public static int searchMinDiffElement_My_Way(int[] A, int key) {
        int ans = 0, diff = Integer.MAX_VALUE, l = 0, r = A.length - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (A[mid] == key) return key;
            else if (A[mid] < key) {
                int[] item = process(A[mid], key, diff, ans);
                ans = item[0];
                diff = item[1];

                l = mid + 1;
            } else {
                int[] item = process(A[mid], key, diff, ans);
                ans = item[0];
                diff = item[1];

                r = mid - 1;
            }
        }
        return ans;
    }

    private static int[] process(int element, int key, int prevDiff, int prevAns) {
        int newDiff = abs(element - key);
        if (newDiff < prevDiff) return new int[]{element, newDiff};
        return new int[]{prevAns, prevDiff};
    }
```

### #2 Grokking BS Way
```java
    // TC: O(logn), SC: O(1)
    public static int searchMinDiffElement_Grokking(int[] A, int key) {
        if (key < A[0]) return A[0];
        if (key > A[A.length - 1]) return A[A.length - 1];

        int l = 0, r = A.length - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (key < A[mid]) r = mid - 1;
            else if (key > A[mid]) l = mid + 1;
            else return A[mid];
        }

        // at the r of the while loop, 'l == r+1'
        // we are not able to find the element in the given array
        // return the element which is closest to the 'key'
        return (A[l] - key) < (key - A[r]) ? A[l] : A[r];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Search Bitonic Array
## Intro
![[Pasted image 20220322102115.png]]

## Thoughts
- N/A

## Solutions
### #1 Grokking BS

```java
    // TC: O(3logn), SC: O(1)
    public static int search(int[] arr, int key) {
        int maxIndex = findMax(arr);
        int keyIndex = binarySearch(arr, key, 0, maxIndex);
        if (keyIndex != -1) return keyIndex;
        return binarySearch(arr, key, maxIndex + 1, arr.length - 1);
    }

    // find index of the maximum value in a bitonic array
    private static int findMax(int[] arr) {
        int l = 0, r = arr.length - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (arr[mid] > arr[mid + 1]) r = mid;
            else l = mid + 1;
        }

        // at the r of the while loop, 'l == r'
        return l;
    }

    // order-agnostic binary search
    private static int binarySearch(int[] arr, int key, int l, int r) {
        while (l <= r) {
            int mid = l + (r - l) / 2;

            if (key == arr[mid]) return mid;
            if (arr[l] < arr[r]) {
                if (key < arr[mid]) r = mid - 1;
                else l = mid + 1;
            } else {
                if (key > arr[mid]) r = mid - 1;
                else l = mid + 1;
            }
        }
        return -1;
    }
```

### #2 BS My Way
```java
    // TC: O(logn), SC: O(1)
    // not sure if this works for all testcases since grokking has only 3 test cases
    public static int search_MyWay(int[] A, int key) {
        int l = 0, r = A.length - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;

            if (mid + 1 >= A.length) {
                if (A[mid] == key) return mid;
                else return -1;
            }

            if (A[mid] < A[mid + 1]) {
                if (A[mid] == key) return mid;
                if (A[mid] < key) l = mid + 1;
                else r = mid - 1;
            } else {
                if (A[mid] == key) return mid;
                if (A[mid] < key) r = mid - 1;
                else l = mid + 1;
            }
        }
        return -1;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Rotation Count
## Intro
![[Pasted image 20220322102227.png]]
![[Pasted image 20220322102240.png]]
## Thoughts
- N/A

## Solutions
### #1 Linear
```java
    // TC: O(n), SC: O(1)
    public static int countRotations_Linear(int[] A) {
        int minIndex = 0;
        for (int i = 1; i < A.length; i++) {
            if (A[i] < A[minIndex]) minIndex = i;
        }
        return minIndex;
    }
```

### #2 BS My Way
```java
    // TC: O(logn), SC: O(1)
    public static int countRotations_MyWay(int[] A) {
        int l = 0, r = A.length - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (A[mid] < A[r]) r = mid;
            else l = mid + 1;
        }
        return l;
    }
```

### #3 Grokking BS
```java
    // TC: O(logn), SC: O(1)
    // Intuition behind this is that min element is the only element where its always smaller than its previous element.
    public static int countRotations_GrokkingWay(int[] arr) {
        int l = 0, r = arr.length - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;

            // if mid is greater than the next element
            if (mid < r && arr[mid] > arr[mid + 1]) return mid + 1;
            // if mid is smaller than the previous element
            if (mid > l && arr[mid - 1] > arr[mid]) return mid;

            // left side is sorted, so the pivot is on right side
            if (arr[l] < arr[mid]) l = mid + 1;
            else r = mid - 1; // right side is sorted, so the pivot is on the left side
        }

        return 0; // the array has not been rotated
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Rotation Count II
## Intro
![[Pasted image 20220322102356.png]]

## Thoughts
- N/A

## Solutions
### #1 Grokking Way
```java
    // TC: O(logn), SC: O(1)
    public static int countRotations_Grokking(int[] arr) {
        if (arr == null || arr.length == 0)
            throw new IllegalArgumentException();

        int start = 0, end = arr.length - 1;
        while (start < end) {
            int mid = start + (end - start) / 2;
            if (mid < end && arr[mid] > arr[mid + 1]) // if element at mid is greater than the next element
                return mid + 1;
            if (mid > start && arr[mid - 1] > arr[mid]) // if element at mid is smaller than the previous element
                return mid;

            // this is the only difference from the previous solution
            // if numbers at indices start, mid, and end are same, we can't choose a side
            // the best we can do is to skip one number from both ends if they are not the smallest number
            if (arr[start] == arr[mid] && arr[end] == arr[mid]) {
                if (arr[start] > arr[start + 1]) // if element at start+1 is not the smallest
                    return start + 1;
                ++start;
                if (arr[end - 1] > arr[end]) // if the element at end is not the smallest
                    return end;
                --end;
                // left side is sorted, so the pivot is on right side
            } else if (arr[start] < arr[mid] || (arr[start] == arr[mid] && arr[mid] > arr[end])) {
                start = mid + 1;
            } else { // right side is sorted, so the pivot is on the left side
                end = mid - 1;
            }
        }

        return 0; // the array has not been rotated
    }
```

### #2 LC Way
```java
    // TC: O(logn), SC: O(1)
    public static int countRotations_LCWay(int[] A) {
        int l = 0, r = A.length - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (A[mid] < A[r]) r = mid;
            else if (A[mid] > A[r]) l = mid + 1;
            else r--;
        }
        return A[l];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Search In Rotated Sorted Array II
## Intro
https://leetcode.com/problems/search-in-rotated-sorted-array-ii/
![[Pasted image 20220322102531.png]]

## Thoughts
- N/A

## Solutions
### #1 LC BS
```java
    // TC: O(n), SC: O(1) worst case
    // TC: O(logn), SC: O(1) avg case
    public boolean search_LCSolution(int[] nums, int target) {
        if (nums.length == 0) return false;
        int lo = 0, hi = nums.length - 1;

        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;

            if (nums[mid] == target) return true;

            if (!isBinarySearchHelpful(nums, lo, nums[mid])) {
                lo++;
                continue;
            }

            // which array does pivot belong to
            boolean pivotArray = existsInFirst(nums, lo, nums[mid]);

            // which array does target belong to
            boolean targetArray = existsInFirst(nums, lo, target);

            // xor is true when both operands are different
            if (pivotArray ^ targetArray) {
                // pivot in first, target in second
                if (pivotArray) lo = mid + 1;
                    // target in first, pivot in second
                else hi = mid - 1;
            } else {
                // if pivot and target exist in same sorted array
                if (nums[mid] < target) lo = mid + 1;
                else hi = mid - 1;
            }
        }
        return false;
    }

    private boolean existsInFirst(int[] nums, int startIndex, int element) {
        return nums[startIndex] <= element;
    }

    private boolean isBinarySearchHelpful(int[] nums, int startIndex, int element) {
        return nums[startIndex] != element;
    }
```

### #2 BS Shorter
```java
    // TC: O(n), SC: O(1) worst case
    // TC: O(logn), SC: O(1) avg case
    public boolean search_33_Improvement(int[] nums, int target) {
        if (nums.length == 0) return false;

        int lo = 0, hi = nums.length - 1;

        while (lo <= hi) {
            // to avoid dups
            while (lo < hi && nums[lo] == nums[lo + 1]) lo++;
            while (lo < hi && nums[hi] == nums[hi - 1]) hi--;

            // the code below is exactly same as 33: Search in rotated sorted array
            int mid = lo + (hi - lo) / 2;
            if (nums[mid] == target) return true;

            if (nums[mid] >= nums[lo]) {
                if (target >= nums[lo] && target < nums[mid]) hi = mid - 1;
                else lo = mid + 1;
            } else {
                if (target <= nums[hi] && target > nums[mid]) lo = mid + 1;
                else hi = mid - 1;
            }
        }
        return false;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Search A 2D Matrix
## Intro
https://leetcode.com/problems/search-a-2d-matrix/
![[Pasted image 20220322102623.png]]
![[Pasted image 20220322102627.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(mn), SC: O(1)
    public boolean searchMatrix_BruteForce(int[][] matrix, int target) {
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                if (matrix[i][j] == target) return true;
            }
        }
        return false;
    }

    // TC: O(mn), SC: O(1)
    public boolean searchMatrix_BruteForce_EnhancedForLoop(int[][] matrix, int target) {
        for (int[] rows : matrix) {
            for (int num : rows) {
                if (num == target) return true;
            }
        }
        return false;
    }
```

### #2 Construct Array
```java
    // TC: O(mn + log(mn)), SC: O(mn)
    public boolean searchMatrix_ConstructAnArray(int[][] matrix, int target) {
        List<Integer> res = new ArrayList<>();
        int rows = matrix.length, cols = matrix[0].length;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) res.add(matrix[i][j]);
        }
        return Collections.binarySearch(res, target) >= 0;
    }
```

### #3 O(m+n)
```java
    // TC: O(m+n), SC: O(1)
    public boolean searchMatrix_BottomLeft(int[][] matrix, int target) {
        int rows = matrix.length, cols = matrix[0].length, i = rows - 1, j = 0;
        while (i >= 0 && i < rows && j >= 0 && j < cols) {
            int num = matrix[i][j];
            if (num == target) return true;
            if (num < target) j++;
            else i--;
        }
        return false;
    }

    // TC: O(m+n), SC: O(1)
    public boolean searchMatrix_TopRight(int[][] matrix, int target) {
        int rows = matrix.length, cols = matrix[0].length, i = 0, j = cols - 1;
        while (i >= 0 && i < rows && j >= 0 && j < cols) {
            int num = matrix[i][j];
            if (num == target) return true;
            if (num < target) i++;
            else j--;
        }
        return false;
    }
```

### #4 Binary Search
```java
    // TC: O(log(mn)), SC: O(1)
    public boolean searchMatrix_BinarySearch(int[][] matrix, int target) {
        if (matrix.length == 0) return false;
        int rows = matrix.length, cols = matrix[0].length;

        int lo = 0, hi = rows * cols - 1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            int num = matrix[mid / cols][mid % cols];
            if (num == target) return true;
            else if (num < target) lo = mid + 1;
            else hi = mid - 1;
        }
        return false;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Search a 2D Matrix II
## Intro
https://leetcode.com/problems/search-a-2d-matrix-ii/
![[Pasted image 20220322102805.png]]
![[Pasted image 20220322102811.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(mn), SC: O(1)
    public boolean searchMatrix_BruteForce(int[][] matrix, int target) {
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                if (matrix[i][j] == target) {
                    return true;
                }
            }
        }

        return false;
    }
```

### #2 O(m+n)
```java
    // TC: O(m+n), SC: O(1)
    public boolean searchMatrix_BottomLeft(int[][] matrix, int target) {
        int rows = matrix.length, cols = matrix[0].length, i = rows - 1, j = 0;
        while (i >= 0 && i < rows && j >= 0 && j < cols) {
            int num = matrix[i][j];
            if (num == target) return true;
            if (num < target) j++;
            else i--;
        }
        return false;
    }

    // TC: O(m+n), SC: O(1)
    public boolean searchMatrix_TopRight(int[][] matrix, int target) {
        int rows = matrix.length, cols = matrix[0].length, i = 0, j = cols - 1;
        while (i >= 0 && i < rows && j >= 0 && j < cols) {
            int num = matrix[i][j];
            if (num == target) return true;
            if (num < target) i++;
            else j--;
        }
        return false;
    }
```

### #3 Binary Search
```java
    // TC: O(log(n!)), SC: O(1)
    // todo learn how this works
    public boolean searchMatrix_binarySearchOnDiagonals(int[][] matrix, int target) {
        // an empty matrix obviously does not contain `target`
        if (matrix == null || matrix.length == 0) {
            return false;
        }

        // iterate over matrix diagonals
        int shorterDim = Math.min(matrix.length, matrix[0].length);
        for (int i = 0; i < shorterDim; i++) {
            boolean verticalFound = binarySearch(matrix, target, i, true);
            boolean horizontalFound = binarySearch(matrix, target, i, false);
            if (verticalFound || horizontalFound) {
                return true;
            }
        }

        return false;
    }

    private boolean binarySearch(int[][] matrix, int target, int start, boolean vertical) {
        int lo = start;
        int hi = vertical ? matrix[0].length - 1 : matrix.length - 1;

        while (hi >= lo) {
            int mid = (lo + hi) / 2;
            if (vertical) { // searching a column
                if (matrix[start][mid] < target) {
                    lo = mid + 1;
                } else if (matrix[start][mid] > target) {
                    hi = mid - 1;
                } else {
                    return true;
                }
            } else { // searching a row
                if (matrix[mid][start] < target) {
                    lo = mid + 1;
                } else if (matrix[mid][start] > target) {
                    hi = mid - 1;
                } else {
                    return true;
                }
            }
        }

        return false;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Median of Two Sorted Arrays
## Intro
https://leetcode.com/problems/median-of-two-sorted-arrays/
![[Pasted image 20220322103036.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O((m + n) log (m + n)), SC: O(m + n)
    public double findMedianSortedArrays_BruteForce(int[] nums1, int[] nums2) {
        int k = 0, arr[] = new int[nums1.length + nums2.length], n = arr.length;
        for (int num : nums1) arr[k++] = num;
        for (int num : nums2) arr[k++] = num;

        Arrays.sort(arr);
        return n % 2 == 1 ? arr[n / 2] : (arr[n / 2] + arr[(n / 2) - 1]) / 2.0;
    }
```

### #2 Merge Approach
```java
    // TC: O(m + n), SC: O(m + n)
    public double findMedianSortedArrays_Merge_Or(int[] nums1, int[] nums2) {
        int k = 0, arr[] = new int[nums1.length + nums2.length], n = arr.length, i = 0, j = 0;

        while (i < nums1.length || j < nums2.length) {
            if (i < nums1.length && j < nums2.length) {
                if (nums1[i] < nums2[j]) arr[k++] = nums1[i++];
                else arr[k++] = nums2[j++];
            } else if (i < nums1.length) arr[k++] = nums1[i++];
            else arr[k++] = nums2[j++];
        }

        return n % 2 == 1 ? arr[n / 2] : (arr[n / 2] + arr[(n / 2) - 1]) / 2.0;
    }

    // TC: O(m + n), SC: O(m + n)
    public double findMedianSortedArrays_Merge_And(int[] nums1, int[] nums2) {
        int k = 0, arr[] = new int[nums1.length + nums2.length], n = arr.length, i = 0, j = 0;

        while (i < nums1.length && j < nums2.length) {
            if (nums1[i] < nums2[j]) arr[k++] = nums1[i++];
            else arr[k++] = nums2[j++];
        }
        while (i < nums1.length) arr[k++] = nums1[i++];
        while (j < nums2.length) arr[k++] = nums2[j++];

        return n % 2 == 1 ? arr[n / 2] : (arr[n / 2] + arr[(n / 2) - 1]) / 2.0;
    }
```

### #3 Early Break
```java
    // TC: O(m + n), SC: O(m + n)
    public double findMedianSortedArrays_EarlyBreak(int[] nums1, int[] nums2) {
        int k = 0, arr[] = new int[nums1.length + nums2.length], n = arr.length, i = 0, j = 0;

        while (i < nums1.length || j < nums2.length) {

            if (i < nums1.length && j < nums2.length) {
                if (nums1[i] < nums2[j]) arr[k++] = nums1[i++];
                else arr[k++] = nums2[j++];
            } else if (i < nums1.length) arr[k++] = nums1[i++];
            else arr[k++] = nums2[j++];

            if (n % 2 == 1 && k - 1 == n / 2) return arr[n / 2];
            if (n % 2 == 0 && k - 1 == n / 2) return (arr[n / 2] + arr[(n / 2) - 1]) / 2.0;
        }
        return 0.0;
    }
```

### #4 Recursive Binary Search
```java
    // TC: O(log(m+n)), SC: O(m+n), SC coz of recursion. If iterative then no space
    // todo convert to iterative
    public double findMedianSortedArrays_BinarySearch(int[] A, int[] B) {
        int m = A.length, n = B.length, l = (m + n + 1) / 2, r = (m + n + 2) / 2;
        return (getkth(A, 0, B, 0, l) + getkth(A, 0, B, 0, r)) / 2.0;
    }

    public double getkth(int[] A, int aStart, int[] B, int bStart, int k) {
        if (aStart > A.length - 1) return B[bStart + k - 1];
        if (bStart > B.length - 1) return A[aStart + k - 1];
        if (k == 1) return min(A[aStart], B[bStart]);

        int aMid = Integer.MAX_VALUE, bMid = Integer.MAX_VALUE;
        if (aStart + k / 2 - 1 < A.length) aMid = A[aStart + k / 2 - 1];
        if (bStart + k / 2 - 1 < B.length) bMid = B[bStart + k / 2 - 1];

        if (aMid < bMid)
            return getkth(A, aStart + k / 2, B, bStart, k - k / 2);// Check: aRight + bLeft
        else
            return getkth(A, aStart, B, bStart + k / 2, k - k / 2);// Check: bRight + aLeft
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Count of Range Sum(todo)
## Intro
https://leetcode.com/problems/count-of-range-sum/
![[Pasted image 20220322103307.png]]

## Thoughts
- N/A

## Solutions
### #1
```java

```

### #2
```java

```

## Common Pitfalls to Avoid
- N/A

---

# Peak Index In a Mountain Array
## Intro
https://leetcode.com/problems/peak-index-in-a-mountain-array/
![[Pasted image 20220322103419.png]]
## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n), SC: O(1)
    public int peakIndexInMountainArray_BruteForce(int[] arr) {
        int prevIndex = 0;
        for (int i = 1; i < arr.length; i++) {
            if (arr[i] < arr[prevIndex]) break;
            prevIndex = i;
        }
        return prevIndex;
    }
```

### #2 Binary Search
```java
    // TC: O(logn), SC: O(1)
    public int peakIndexInMountainArray_BinarySearch_Better(int[] A) {
        int l = 0, r = A.length - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (A[mid] < A[mid + 1]) l = mid + 1;
            else r = mid;
        }
        return l;
    }

    // TC: O(logn), SC: O(1)
    public int peakIndexInMountainArray_BinarySearch_MYWAY(int[] A) {
        int l = 0, r = A.length - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (mid == 0) l = mid + 1;
            else if (mid == A.length - 1) r = mid - 1;
            else {
                if (A[mid] > A[mid + 1] && A[mid] > A[mid - 1]) return mid;
                if (A[mid] > A[mid + 1]) r = mid - 1;
                else if (A[mid] > A[mid - 1]) l = mid + 1;
            }
        }
        return -1;
    }
```

## Common Pitfalls to Avoid
- N/A

---