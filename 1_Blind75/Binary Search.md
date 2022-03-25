# Binary Search Tree

# Search In Rotated Sorted Array

## Intro
https://leetcode.com/problems/search-in-rotated-sorted-array/
![[Pasted image 20220320221918.png]]


## Thoughts
- The moment you see sorted, think about binary search.
- Then figure out which side is normal(increasing or decreasing) using `if(A[mid] < A[r])` 

## Solutions

### #1 Binary Search Iterative

```java
    // TC: O(logn), SC: O(1)
    public int search_Iterative(int[] A, int target) {
        int l = 0, r = A.length - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (target == A[mid]) return mid;
            if (A[mid] < A[r]) {
                // rightside is normal
                if (target > A[mid] && target <= A[r]) l = mid + 1;
                else r = mid - 1;
            } else {
                // leftside is normal
                if (target >= A[l] && target < A[mid]) r = mid - 1;
                else l = mid + 1;
            }
        }
        return -1;
```


## Common Pitfalls to Avoid

- For binary search problems be careful with mid. Use l + (r - l) / 2 instead of (l + r) / 2 to avoid overflow issues

# Find Minimum in Rotated Sorted Array

## Intro
https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/

![[Pasted image 20220320221321.png]]

## Thoughts
- The intuition here is that the minimum element is the only element which will be smaller than the next or previous element. Especially next, you can use this to your advantage.

## Solutions
### #1 Recursive

```java
    // TC: O(logn), SC: O(logn)
    public int findMin_Recursive(int[] nums) {
        return helper(nums, 0, nums.length - 1);
    }

    private int helper(int[] nums, int left, int right) {
        if (left >= right) return nums[left];

        int mid = (left + right) / 2;
        if (nums[mid] < nums[right]) return helper(nums, left, mid);
        else return helper(nums, mid + 1, right);
    }

```

### #2 Iterative
```java
    // TC: O(logn), SC: O(1)
    public int findMin_Iterative(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = (left + right) / 2;
            if (nums[mid] < nums[right]) right = mid;
            else left = mid + 1;
        }
        return nums[left];
    }

    // TC: O(logn), SC: O(1)
    public int findMin_AnotherWay(int[] nums) {
        int left = 0, right = nums.length - 1;
        if (nums[left] < nums[right]) return nums[left];
        while (right - left > 1) {
            int mid = (left + right) / 2;
            if (nums[left] > nums[mid]) right = mid;
            else left = mid;
        }
        return nums[right];
    }
```

### #3 Grokking Way
```java
    // TC: O(logn), SC: O(1)
    // Intuition behind this is that min element is the only element where its always smaller than its previous element.
    public int findMin_GrokkingWay(int[] arr) {
        int l = 0, r = arr.length - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;

            if (mid < r && arr[mid] > arr[mid + 1]) return arr[mid + 1];
            if (mid > l && arr[mid - 1] > arr[mid]) return arr[mid];

            if (arr[l] < arr[mid]) l = mid + 1;
            else r = mid - 1;
        }

        return arr[0];
    }
```
## Common Pitfalls to Avoid
- N/A