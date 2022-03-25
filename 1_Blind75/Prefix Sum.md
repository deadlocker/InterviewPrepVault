
# Product Of Array Except Self
## Intro
https://leetcode.com/problems/product-of-array-except-self/
![[Pasted image 20220321044435.png]]

## Thoughts
- list

## Solutions
### #1 BruteForce

```java
    // TC: O(n^2), SC: O(1)
    public int[] productExceptSelf_BruteForce(int[] nums) {
        int res[] = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            int prod = 1;
            for (int j = 0; j < nums.length; j++) {
                if (j != i) prod *= nums[j];
            }
            res[i] = prod;
        }
        return res;
    }
```

### #2 LeftRight Cumulative Full Space
```java
    // TC: O(n), SC: O(n)
    public int[] productExceptSelf_LeftRightCumulative_FullSpace(int[] nums) {
        int[] res = new int[nums.length], left = new int[nums.length], right = new int[nums.length];

        left[0] = 1;
        for (int i = 1; i < left.length; i++) left[i] = left[i - 1] * nums[i - 1];

        right[right.length - 1] = 1;
        for (int i = right.length - 2; i >= 0; i--) right[i] = right[i + 1] * nums[i + 1];

        for (int i = 0; i < res.length; i++) res[i] = left[i] * right[i];
        return res;
    }
```

### #3 LeftRight Cumulative Full Space Another Way
```java
    // TC: O(n), SC: O(n)
    public int[] productExceptSelf_LeftRightCumulative_FullSpace_2(int[] nums) {
        int[] res = new int[nums.length], left = new int[nums.length], right = new int[nums.length];

        left[0] = nums[0];
        for (int i = 1; i < left.length; i++) left[i] = left[i - 1] * nums[i];

        right[right.length - 1] = nums[nums.length - 1];
        for (int i = right.length - 2; i >= 0; i--) right[i] = right[i + 1] * nums[i];

        for (int i = 0; i < res.length; i++) {
            if (i == 0) res[i] = right[i + 1];
            else if (i == res.length - 1) res[i] = left[i - 1];
            else res[i] = left[i - 1] * right[i + 1];

        }
        return res;
    }
```

### #4 No Space
```java
    // TC: O(n), SC: O(1)
    public int[] productExceptSelf_NoSpace(int[] nums) {
        int[] res = new int[nums.length];

        res[0] = 1;
        for (int i = 1; i < res.length; i++) res[i] = res[i - 1] * nums[i - 1];

        int prod = 1;
        for (int i = res.length - 2; i >= 0; i--) {
            res[i] *= (prod * nums[i + 1]);
            prod *= nums[i + 1];
        }

        return res;
    }
```

### #5 Much Simpler
```java
    // Check the difference between this method and NoSpace method
    // TC: O(n), SC: O(1)
    public int[] productExceptSelf_MuchSimpler(int[] nums) {
        int[] res = new int[nums.length];

        res[0] = 1;
        for (int i = 1; i < res.length; i++) res[i] = res[i - 1] * nums[i - 1];

        int prod = 1;
        for (int i = res.length - 1; i >= 0; i--) {
            res[i] *= prod;
            prod *= nums[i];
        }

        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---