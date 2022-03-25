
# Range Sum Query Immutable
## Intro
https://leetcode.com/problems/range-sum-query-immutable/
![[Pasted image 20220322134411.png]]
## Thoughts
- N/A

## Solutions
### #1 Cumulative Approach(Prefix Sum)
```java
    // cumulative approach
    private int[] nums;

    public P303_RangeSumQueryImmutable(int[] arr) {
        nums = new int[arr.length];
        int sum = 0;
        for(int i = 0; i < arr.length; i++) {
            sum += arr[i];
            nums[i] += sum;
        }
    }

    // TC: O(1), SC: O(n)
    public int sumRange_CumulativeApproach(int i, int j) {
        int prev = i < 1 ? 0 : nums[i - 1];
        return nums[j] - prev;
    }
```

### #2 BruteForce
```java
    private int[] nums;

    public P303_RangeSumQueryImmutable(int[] nums) {
        this.nums = nums;
    }

    // TC: O(n), SC: O(n)
    public int sumRange_BruteForce(int i, int j) {
        int sum = 0;
        for (int k = i; k <= j; k++) sum += nums[k];
        return sum;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Majority Element
## Intro
https://leetcode.com/problems/majority-element/
![[Pasted image 20220322134420.png]]
## Thoughts
- N/A

## Solutions
### #1 Randomized
```java
    // Worst case TC: O(inf), SC: O(1)
    // Avg case TC: O(2), SC: O(1), check LC solution for this stupid explanation
    public int majorityElement_Randomized(int[] nums) {
        Random rand = new Random();
        Map<Integer, Integer> counts = new HashMap<>();
        for (int num : nums) counts.put(num, counts.getOrDefault(num, 0) + 1);


        int majorityCount = nums.length / 2;

        while (true) {
            int i = rand.nextInt(nums.length);
            int candidate = nums[i];
            if (counts.get(candidate) > majorityCount) return candidate;
        }
    }
```

### #2 Map
```java
    // TC: O(n), SC: O(n)
    public int majorityElement_MapApproach(int[] nums) {
        Map<Integer, Integer> counts = new HashMap<>();
        for (int num : nums) counts.put(num, counts.getOrDefault(num, 0) + 1);

        int max = 0, ans = nums[0];
        for (Map.Entry<Integer, Integer> entry : counts.entrySet()) {
            int num = entry.getKey(), count = entry.getValue();
            if (count > max) {
                max = count;
                ans = num;
            }
        }
        return ans;
    }
```

### #3 Sort
```java
    // TC: O(nlogn), SC: O(1)
    public int majorityElement_SortApproach(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length / 2];
    }
```

### #4 Boyer-Moore
```java
    // TC: O(n), SC: O(1)
    // Todo: check how this works
    public int majorityElement_BoyerMoore(int[] nums) {
        int count = 0, candidate = 0;
        for (int num : nums) {
            if (count == 0) candidate = num;
            count += (num == candidate) ? 1 : -1;
        }
        return candidate;
    }
```

## Common Pitfalls to Avoid
- N/A

---