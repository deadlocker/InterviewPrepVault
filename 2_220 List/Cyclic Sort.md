
# Cyclic Sort
## Intro
![[Pasted image 20220322132200.png]]

## Thoughts
- N/A

## Solutions
### #1 Grokking
```java
    // TC: O(n), SC: O(1)
    public static void sort_StupidWay(int[] nums) {
        for (int i = 0; i < nums.length; i++) nums[i] = i + 1;
    }

    // TC: O(n + n - 1), SC: O(1) or TC: O(2n), SC: O(1) or more simply TC: O(n), SC: O(1)
    public static void sort_Grokking(int[] nums) {
        int i = 0;
        while (i < nums.length) {
            int j = nums[i] - 1;
            if (nums[i] != nums[j]) swap(nums, i, j);
            else i++;
        }
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find the Corrupt Pair
## Intro
![[Pasted image 20220322132259.png]]

## Thoughts
- N/A

## Solutions
### #1 Cyclic Sort
```java
    // TC: O(n), SC: O(1)
    public static int[] findNumbers_CyclicSort(int[] A) {
        int n = A.length;
        for (int i = 0; i < n; i++) swap(A, i, A[i] - 1);

        for (int i = 0; i < n; i++) if (A[i] != i + 1) return new int[]{A[i], i + 1};
        return new int[0];
    }
```

### #2 Mutate Negative
```java
    // TC: O(n), SC: O(1)
    public static int[] findNumbers_MutateNegative(int[] A) {
        int n = A.length;
        for (int i = 0; i < n; i++) {
            int index = abs(A[i]) - 1;
            if (A[index] > 0) A[index] *= -1;
        }

        for (int i = 0; i < n; i++) if (A[i] > 0) return new int[]{i - 1, A[i] - 1};
        return new int[]{-1, -1};
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find All Numbers Disappeared in an array
## Intro
https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/
![[Pasted image 20220322132531.png]]

## Thoughts
- N/A

## Solutions
### #1 Set
```java
    // TC: O(n), SC: O(n)
    public List<Integer> findDisappearedNumbers_Set(int[] A) {
        Set<Integer> set = new HashSet<>();
        List<Integer> res = new ArrayList<>();
        for (int num : A) set.add(num);
        for (int i = 1; i <= A.length; i++) if (!set.contains(i)) res.add(i);
        return res;
    }
```

### #2 Math.abs() trick
```java
    // TC: O(n), SC: O(1)
    public List<Integer> findDisappearedNumbers(int[] A) {
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < A.length; i++) {
            int num = abs(A[i]) - 1;
            if (A[num] > 0) A[num] *= -1;
        }
        for (int i = 0; i < A.length; i++) if (A[i] > 0) res.add(i + 1);
        return res;
    }
```

### #3 Cyclic Sort
```java
    // TC: O(3n), SC: O(1)
    public List<Integer> findDisappearedNumbers_CyclicSort(int[] A) {
        List<Integer> res = new ArrayList<>();
        int n = A.length, i = 0;
        while (i < n) {
            int index = A[i] - 1;
            if (i + 1 != A[i] && A[index] != A[i]) swap(A, i, index);
            else i++;
        }

        for (i = 0; i < n; i++) if (i + 1 != A[i]) res.add(i + 1);
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find the duplicate number
## Intro
https://leetcode.com/problems/find-the-duplicate-number/
![[Pasted image 20220322132709.png]]
## Thoughts
- N/A

## Solutions
### #1 Sort
```java
    // TC: O(nlogn), SC: O(1)
    public int findDuplicate_SortApproach(int[] nums) {
        Arrays.sort(nums);
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1]) return nums[i];
        }
        return -1;
    }
```

### #2 Set
```java
    // TC: O(n), SC: O(n)
    public int findDuplicate_SetApproach(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (set.contains(num)) return num;
            set.add(num);
        }
        return 0;
    }
```

### #3 Modify Input
```java
    // TC: O(n), SC: O(1)
    public int findDuplicate_ModifyInput(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            int val = abs(nums[i]);
            if (nums[val] < 0) return val;
            nums[val] *= -1;
        }
        return -1;
    }
```

### #4 Floyd Cycle
```java
    // TC: O(n), SC: O(1)
    public int findDuplicate_FloydCycleDetection(int[] nums) {
        int slow = nums[0], fast = nums[0];
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);
        slow = nums[0];

        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }

    // TC: O(n), SC: O(1)
    public int findDuplicate_FloydCycleDetection_AnotherWay(int[] nums) {
        if (nums.length == 0) return -1;
        int slow = nums[0], fast = nums[nums[0]];
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }
        slow = 0;
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }
```

### #5 Cyclic Sort
```java
    // TC: O(n), SC: O(1)
    public int findDuplicate_CyclicSortWay(int[] nums) {
        int i = 0;
        while (i < nums.length) {
            if (nums[i] != nums[nums[i]]) swap(nums, i, nums[i]);
            else i++;
        }
        return nums[0];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find All duplicates in an array
## Intro
https://leetcode.com/problems/find-all-duplicates-in-an-array/
![[Pasted image 20220322132729.png]]

## Thoughts
- N/A

## Solutions
### #1 Sort
```java
    // TC: O(nlogn), SC: O(1)
    public List<Integer> findDuplicates_SortApproach(int[] nums) {
        List<Integer> res = new ArrayList<>();
        Arrays.sort(nums);
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1]) res.add(nums[i]);
        }
        return res;
    }
```

### #2 Set
```java
    // TC: O(n), SC: O(n)
    public List<Integer> findDuplicates_SetApproach(int[] nums) {
        List<Integer> res = new ArrayList<>();
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (set.contains(num)) res.add(num);
            else set.add(num);
        }
        return res;
    }
```

### #3 Modify Input
```java
    // TC: O(n), SC: O(1)
    public List<Integer> findDuplicates_ModifyInput(int[] nums) {
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            int val = abs(nums[i]);
            if (nums[val - 1] < 0) res.add(val);
            else nums[val - 1] *= -1;
        }
        return res;
    }
```

### #4 Cyclic Sort
```java
    // TC: O(n), SC: O(1)
    public List<Integer> findDuplicates_CyclicSort(int[] nums) {
        List<Integer> res = new ArrayList<>();
        int i = 0;
        while (i < nums.length) {
            if (nums[i] != nums[nums[i] - 1]) swap(nums, i, nums[i] - 1);
            else i++;
        }

        for (i = 0; i < nums.length; i++) if (nums[i] != i + 1) res.add(nums[i]);
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# First Missing Positive
## Intro
https://leetcode.com/problems/first-missing-positive/
![[Pasted image 20220322132837.png]]
## Thoughts
- N/A

## Solutions
### #1 Extra Array
```java
    // TC: O(n), SC: O(n)
    public int firstMissingPositive_ExtraArray(int[] A) {
        int n = A.length;
        boolean[] exists = new boolean[n];
        for (int num : A) {
            if (num > 0 && num <= n) exists[num - 1] = true;
        }
        for (int i = 0; i < exists.length; i++) {
            if (!exists[i]) return i + 1;
        }
        return n + 1;
    }
```

### #2 NoSpace Mine
```java
    // TC: O(n), SC: O(1)
    public int firstMissingPositive_NoSpace_MySolution(int[] A) {
        // Why is res initialized as n + 1? coz this is the max possible answer that we could have
        int n = A.length;
        // pre-process array so that non-positive ints are overwritten with n + 1;
        // why n + 1? coz this is the safest value to choose during
        // floyd cycle way which we are about to do in second for loop below
        for (int i = 0; i < n; i++) if (A[i] <= 0) A[i] = n + 1;

        // Take abs of A[i] and then make sure its within bounds and use that as index.
        // This is also called as Floyd Cycle algorithm or something. Another fancy name I think is tortoise-hare.
        for (int num : A) {
            int index = abs(num);
            if (index > 0 && index <= n) {
                // Now indicate that the +ve value index was found.
                // We indicate this by setting A[index - 1] as a negative value;
                // Why does this work? We are using the negative sign in the array to indicate that
                // a positive value has been found. On the other hand, value itself is encoded in Math.abs(A[i])
                A[index - 1] = -abs(A[index - 1]);
            }
        }

        // Now check for the first value where A[i] is not negative, and we have found our answer
        for (int i = 0; i < n; i++) if (A[i] > 0) return i + 1;
        return n + 1;
    }
```

### #3 Cyclic Sort
```java
    // TC: O(n), SC: O(1)
    public int firstMissingPositive_CyclicSort(int[] A) {
        int i = 0, n = A.length;
        while (i < n) {
            int j = A[i] - 1;
            if (j >= 0 && j < n && A[i] != A[j]) swap(A, i, j);
            else i++;
        }

        for (i = 0; i < n; i++) if (A[i] != i + 1) return i + 1;
        return n + 1;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Kth Missing Positive Number
## Intro
https://leetcode.com/problems/kth-missing-positive-number/
![[Pasted image 20220322132944.png]]

## Thoughts
- N/A

## Solutions
### #1 Set
```java
    // TC: O(max(k,n)), SC: O(n)
    public int findKthPositive_HashSet(int[] A, int k) {
        Set<Integer> set = new HashSet<>();
        for (int num : A) set.add(num);
        int i = 1, count = 0;
        while (count < k) {
            if (!set.contains(i)) count++;
            i++;
        }
        return i - 1;
    }
```

### #2 BruteForce Linear
```java
    // TC: O(n), SC: O(1)
    public int findKthPositive_BruteForceLinear(int[] A, int k) {
        // kth missing is less than A[0]
        if (k <= A[0] - 1) return k;

        k -= A[0] - 1;
        int n = A.length;
        for (int i = 0; i < n - 1; i++) {
            int curMissing = A[i + 1] - A[i] - 1;
            if (k <= curMissing) return A[i] + k;
            k -= curMissing;
        }
        return A[n - 1] + k;
    }
```

### #3 Binary Search
```java
    // // TC: O(logn), SC: O(1)
    public int findKthPositive_BinarySearch(int[] A, int k) {
        int l = 0, r = A.length - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (A[mid] - mid - 1 < k) l = mid + 1;
            else r = mid - 1;
        }

        // Now l = r + 1, kth missing is b/w A[r] and A[l].
        // no.of ints missing before A[r] is A[r] - r - 1
        // ans = A[r] + k - (A[r] - r - 1) = k + r + 1 = k + l;
        return k + l;
    }
```
## Common Pitfalls to Avoid
- N/A

---