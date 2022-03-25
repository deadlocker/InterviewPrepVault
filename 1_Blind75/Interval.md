# Interval
# Merge Intervals
## Intro
https://leetcode.com/problems/merge-intervals/
![[Pasted image 20220320222750.png]]

## Thoughts
- Pretty straightforward, just keep merging if they overlap. Make sure to sort the array based on start times.

## Solutions
### #1 Sorting

```java
    // TC: O(nlogn), SC: O(n)
    public int[][] merge_MyWay(int[][] A) {
        if (A.length == 0) return A;
        Arrays.sort(A, Comparator.comparingInt(a -> a[0]));

        List<int[]> res = new ArrayList<>();
        res.add(A[0]);
        for (int i = 1; i < A.length; i++) {
            int[] first = res.get(res.size() - 1);
            int[] second = A[i];

            if (second[0] <= first[1]) first[1] = Math.max(first[1], second[1]);
            else res.add(second);
        }
        return res.toArray(new int[0][]);
    }

    // TC: O(nlogn), SC: O(logn or n), SC depends on sort whether its in place or not.
    public int[][] merge_AnotherWay(int[][] intervals) {
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));
        List<int[]> res = new ArrayList<>();
        for (int[] cur : intervals) {
            if (res.isEmpty() || !overlap_long(res.get(res.size() - 1), cur)) res.add(cur);
            else res.get(res.size() - 1)[1] = max(res.get(res.size() - 1)[1], cur[1]);
        }

        return res.toArray(new int[0][]);
    }

    private boolean overlap_long(int[] a, int[] b) {
        return (b[0] >= a[0] && b[0] <= a[1]) || (b[0] <= a[0] && b[1] >= a[0]);
    }

    // TC: O(nlogn), SC: O(logn or n), SC depends on sort whether its in place or not.
    public int[][] merge_SortApproach(int[][] intervals) {
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));
        List<int[]> res = new ArrayList<>();
        for (int[] interval : intervals) {
            // if list is empty or if current interval doesnt overlap with previous
            if (res.isEmpty() || res.get(res.size() - 1)[1] < interval[0]) res.add(interval);
            else res.get(res.size() - 1)[1] = max(res.get(res.size() - 1)[1], interval[1]);
        }
        return res.toArray(new int[0][]);
    }

```

## Common Pitfalls to Avoid
- Be careful with overlap condition. Remember there are about 6 overlap conditions. But since this array is already sorted, its just 1 condition`(if cur.start > prev.end then no overlap)`

---
# Insert Interval
## Intro
https://leetcode.com/problems/insert-interval/
![[Pasted image 20220320222801.png]]

## Thoughts
- Make sure to insert and then also merge intervals if this insert caused an overlap.

## Solutions
### #1 Muti pass

```java
    // TC: O(n), SC: O(n)
    public int[][] insert(int[][] intervals, int[] newInterval) {
        int newStart = newInterval[0], newEnd = newInterval[1];
        int i = 0, n = intervals.length;
        LinkedList<int[]> res = new LinkedList<>();

        // add all intervals starting before newInterval
        while (i < n && newStart > intervals[i][0]) res.add(intervals[i++]);

        // add newInterval
        int[] interval;
        // if there is an overlap, merge with last interval
        if (res.isEmpty() || res.getLast()[1] < newStart) res.add(newInterval);
        else {
            interval = res.getLast();
            interval[1] = max(interval[1], newEnd);
        }

        // add next intervals, merge with newInterval if needed
        while (i < n) {
            interval = intervals[i++];
            int start = interval[0], end = interval[1];
            // if there is no overlap, just add an interval
            if (res.getLast()[1] < start) res.add(interval);
            else {// if there is an overlap, merge with last interval
                interval = res.getLast();
                interval[1] = max(interval[1], end);
            }
        }

        return res.toArray(new int[0][]);
    }
```

### #2 Using MergeIntervals method
```java
    // this uses mergeIntervals code
    // TC: O(n), SC: O(n)
    public int[][] insert_AnotherWay(int[][] intervals, int[] newInterval) {
        List<int[]> list = new ArrayList<>();
        boolean newIntervalAdded = false;
        for (int[] interval : intervals) {
            // if newInterval not added, try to add it.
            if (!newIntervalAdded && newInterval[0] < interval[0]) {
                list.add(newInterval);
                newIntervalAdded = true;
            }
            list.add(interval);
        }
        if (!newIntervalAdded) list.add(newInterval);
        return mergeIntervals(list.toArray(new int[0][0]));
    }

    private int[][] mergeIntervals(int[][] intervals) {
        List<int[]> res = new ArrayList<>();
        for (int[] cur : intervals) {
            if (res.isEmpty() || !overlap(res.get(res.size() - 1), cur)) res.add(cur);
            else res.get(res.size() - 1)[1] = Math.max(res.get(res.size() - 1)[1], cur[1]);
        }

        return res.toArray(new int[0][]);
    }

    private boolean overlap(int[] a, int[] b) {
        return (b[0] >= a[0] && b[0] <= a[1]) || (b[0] <= a[0] && b[1] >= a[0]);
```

### #3 Short Code
```java
    // TC: O(n), SC: O(n)
    public int[][] insert_Better(int[][] intervals, int[] newInterval) {
        int i = 0;
        List<int[]> merged = new ArrayList<>();
        while (i < intervals.length && intervals[i][1] < newInterval[0])
            merged.add(intervals[i++]);

        while (i < intervals.length && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
            newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
            i++;
        }

        merged.add(newInterval);

        while (i < intervals.length) merged.add(intervals[i++]);

        return merged.toArray(new int[0][0]);
    }
```

### #4 Binary Search
```java
    // TODO How the hell does this work?
    // TC: O(n), SC: O(n)
    public int[][] insert_UsingBinarySearch(int[][] intervals, int[] newInterval) {
        List<int[]> res = new ArrayList<>();
        int[] toSearch = new int[]{newInterval[1], newInterval[0]};
        int l = Arrays.binarySearch(intervals, toSearch, (a, b) -> a[1] - b[1]);
        if (l < 0) l = -l - 1;
        int r = Arrays.binarySearch(intervals, toSearch, (a, b) -> a[0] - b[0]);
        if (r < 0) r = -r - 2;
        int i = 0;
        while (i < l) res.add(intervals[i++]);
        if (l > r) res.add(newInterval);
        else {
            int start = Math.min(newInterval[0], intervals[l][0]);
            int end = max(newInterval[1], intervals[r][1]);
            res.add(new int[]{start, end});
        }
        i = r + 1;
        while (i < intervals.length) res.add(intervals[i++]);
        return res.toArray(new int[0][]);
    }
```

## Common Pitfalls to Avoid
- N/A

---
# Non-Overlapping Intervals
## Intro
https://leetcode.com/problems/non-overlapping-intervals/
![[Pasted image 20220320222813.png]]

## Thoughts
- Relearn this

## Solutions
### #1 BruteForce

```java
    // TC: O(2^n), SC: O(n)
    public int eraseOverlapIntervals_BruteForce(int[][] intervals) {
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));
        return helper(intervals, -1, 0);
    }

    private int helper(int[][] intervals, int prev, int cur) {
        if (cur == intervals.length) return 0;

        int taken = Integer.MAX_VALUE;
        // cur and prev are not overlapping
        if (prev == -1 || intervals[prev][1] <= intervals[cur][0])
            taken = helper(intervals, cur, cur + 1);

        int nottaken = helper(intervals, prev, cur + 1) + 1;
        return Math.min(taken, nottaken);
    }
```

### #2 DP Based on Starting Point
```java
    // TC: O(n^2), SC: O(n)
    public int eraseOverlapIntervals_DP_BasedOn_StartingPoint(int[][] intervals) {
        if (intervals.length == 0) return 0;

        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));

        int dp[] = new int[intervals.length], ans = 1;
        dp[0] = 1;
        for (int i = 1; i < dp.length; i++) {
            int max = 0;
            for (int j = i - 1; j >= 0; j--) {
                if (!isOverlapping(intervals[j], intervals[i])) max = Math.max(max, dp[j]);
            }
            dp[i] = max + 1;
            ans = Math.max(ans, dp[i]);
        }
        return intervals.length - ans;
    }

    private boolean isOverlapping(int[] A, int[] B) {
        return A[1] > B[0];
    }
```

### #3 DP Based on Ending Point
```java
    // TC: O(n^2), SC: O(n)
    public int eraseOverlapIntervals_DP_BasedOn_EndPoint(int[][] intervals) {
        if (intervals.length == 0) return 0;

        Arrays.sort(intervals, Comparator.comparingInt(a -> a[1]));
        int dp[] = new int[intervals.length], ans = 1;
        dp[0] = 1;
        for (int i = 1; i < dp.length; i++) {
            int max = 0;
            for (int j = i - 1; j >= 0; j--) {
                if (!isOverlapping(intervals[j], intervals[i])) {
                    max = Math.max(dp[j], max);
                    break;
                }
            }
            dp[i] = Math.max(dp[i - 1], max + 1);
            ans = Math.max(ans, dp[i]);
        }
        return intervals.length - ans;
    }
```

### #4 Greedy Approach
```java
    // TC: O(nlogn), SC: O(1)
    public int eraseOverlapIntervals_Greedy(int[][] intervals) {
        if (intervals.length == 0) return 0;

        Arrays.sort(intervals, Comparator.comparingInt(a -> a[1]));
        int prev = 0, count = 0;
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[prev][1] > intervals[i][0]) {
                if (intervals[prev][1] > intervals[i][1]) prev = i;
                count++;
            } else prev = i;
        }
        return count;
    }
```

## Common Pitfalls to Avoid
- N/A

---