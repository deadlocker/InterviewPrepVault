
# Sliding Window Median
## Intro
https://leetcode.com/problems/sliding-window-median/
![[Pasted image 20220322112947.png]]
## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n(k logk)), SC: O(k)
    public double[] medianSlidingWindow_BruteForce(int[] nums, int k) {
        int n = nums.length;
        double[] res = new double[n - k + 1];
        for (int i = 0; i < res.length; i++) {
            int tmp[] = Arrays.copyOfRange(nums, i, i + k);
            Arrays.sort(tmp);
            if (k % 2 == 1) res[i] = tmp[k / 2];
            else res[i] = (tmp[(k / 2) - 1] / 2.0) + (tmp[k / 2] / 2.0);
        }
        return res;
    }
```

### #2 PQ Approach
```java
    // TC: O(n * (logk + k)), SC: O(k)
    // or simply TC: O(nk), SC: O(k)
    public double[] medianSlidingWindow_PQApproach(int[] nums, int k) {
        int n = nums.length;
        double[] res = new double[n - k + 1];
        Queue<Integer> left = new PriorityQueue<>(Comparator.reverseOrder());
        Queue<Integer> right = new PriorityQueue<>();
        for (int i = 0; i < nums.length; i++) {
            if (left.size() <= right.size()) {
                right.offer(nums[i]);
                left.offer(right.poll());
            } else {
                left.offer(nums[i]);
                right.offer(left.poll());
            }

            int size = left.size() + right.size();
            if (size == k) {
                int start = i - k + 1;
                res[start] = (size % 2 == 1) ? left.peek() : (left.peek() / 2.0 + right.peek() / 2.0);
                if (!left.remove(nums[start])) right.remove(nums[start]);
            }
        }
        return res;
    }

    // TC: O(n * (logk + k)), SC: O(k)
    // or simply TC: O(nk), SC: O(k)
    public double[] medianSlidingWindow_PQApproach_Better(int[] A, int k) {
        int n = A.length;
        double[] res = new double[n - k + 1];
        Queue<Integer> left = new PriorityQueue<>((a, b) -> Integer.compare(b, a));
        Queue<Integer> right = new PriorityQueue<>();

        for (int i = 0; i < n; i++) {
            left.offer(A[i]);
            right.offer(left.poll());
            if (left.size() < right.size()) left.offer(right.poll());

            if (i >= k - 1) {
                res[i - k + 1] = left.size() != right.size() ? left.peek() : (left.peek() / 2.0 + right.peek() / 2.0);
                if (!left.remove(A[i - k + 1])) right.remove(A[i - k + 1]);
            }
        }
        return res;
    }
```

### #3 TreeSet Approach
```java
    // TC: O(nlogk), SC: O(k)
    public double[] medianSlidingWindow_TreeSetApproach(int[] nums, int k) {
        int n = nums.length;
        double[] res = new double[n - k + 1];

        // sort on array value, break ties by sorting on array index. we store index not array element.
        TreeSet<Integer> lower = new TreeSet<>((a, b) -> nums[a] != nums[b] ? Integer.compare(nums[a], nums[b]) : Integer.compare(a, b));
        TreeSet<Integer> greater = new TreeSet<>((a, b) -> nums[a] != nums[b] ? Integer.compare(nums[a], nums[b]) : Integer.compare(a, b));

        for (int i = 0; i < n; i++) {
            greater.add(i);
            lower.add(greater.pollFirst());

            if (lower.size() > greater.size()) greater.add(lower.pollLast());

            if (lower.size() + greater.size() == k) {
                res[i - k + 1] = lower.size() == greater.size() ? ((double) nums[lower.last()] + (double) nums[greater.first()]) / 2.0 : nums[greater.first()];

                lower.remove(i - k + 1);
                greater.remove(i - k + 1);
            }
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# IPO
## Intro
https://leetcode.com/problems/ipo/
![[Pasted image 20220322113140.png]]

## Thoughts
- N/A

## Solutions
### #1 PQ
```java
    // TC: O(nlogn + klogn), SC: O(n)
    // where n is total no.of projects while k is no.of projects we are supposed to choose
    public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
        // to speed up: if all projects are available
        boolean speedUp = true;
        for (int c : capital)
            if (w < c) {
                speedUp = false;
                break;
            }
        if (speedUp) {
            Queue<Integer> minHeap = new PriorityQueue<>();
            for (int p : profits) {
                minHeap.add(p);
                if (minHeap.size() > k) minHeap.poll();
            }
            for (int h : minHeap) w += h;
            return w;
        }
        Queue<Integer> capitalHeap = new PriorityQueue<>(Comparator.comparingInt(i -> capital[i]));
        Queue<Integer> profitsHeap = new PriorityQueue<>((i, j) -> Integer.compare(profits[j], profits[i]));

        for (int i = 0; i < capital.length; i++) capitalHeap.offer(i);

        int availableCapital = w;
        for (int i = 0; i < k; i++) {
            while (!capitalHeap.isEmpty() && availableCapital >= capital[capitalHeap.peek()]) {
                profitsHeap.offer(capitalHeap.poll());
            }
            if (!profitsHeap.isEmpty()) availableCapital += profits[profitsHeap.poll()];
        }
        return availableCapital;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find Right Interval
## Intro
https://leetcode.com/problems/find-right-interval/
![[Pasted image 20220322113216.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n^2), SC: O(n), SC coz of output array
    public int[] findRightInterval_BruteForce(int[][] intervals) {
        int[] res = new int[intervals.length];
        for (int i = 0; i < intervals.length; i++) {
            int minIndex = -1, minStart = Integer.MAX_VALUE, end = intervals[i][1];
            for (int j = 0; j < intervals.length; j++) {
                int start = intervals[j][0];
                if (start >= end && start < minStart) {
                    minIndex = j;
                    minStart = start;
                }
            }
            res[i] = minIndex;
        }
        return res;
    }
```

### #2 Sorting
```java
    // TC: O(n^2), SC: O(n), SC coz of hashmap and output array
    public int[] findRightInterval_SortingAndScanning(int[][] intervals) {
        int[] res = new int[intervals.length];
        Map<int[], Integer> elemToIndexMap = new HashMap<>();
        for (int i = 0; i < intervals.length; i++) elemToIndexMap.put(intervals[i], i);
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));

        for (int i = 0; i < intervals.length; i++) {
            int minindex = -1;
            for (int j = i; j < intervals.length; j++) {
                if (intervals[j][0] >= intervals[i][1]) {
                    minindex = elemToIndexMap.get(intervals[j]);
                    break;
                }
            }
            res[elemToIndexMap.get(intervals[i])] = minindex;
        }
        return res;
    }
```

### #3 Binary Search Recursive
```java
    // TC: O(nlogn), SC: O(n)
    public int[] findRightInterval_BinarySearch_Recursive(int[][] intervals) {
        int[] res = new int[intervals.length];
        Map<int[], Integer> elemToIndexMap = new HashMap<>();
        for (int i = 0; i < intervals.length; i++) elemToIndexMap.put(intervals[i], i);
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));

        for (int[] interval : intervals) {
            int[] resInterval = binSearch_Recursive(intervals, interval[1], 0, intervals.length - 1);
            res[elemToIndexMap.get(interval)] = resInterval == null ? -1 : elemToIndexMap.get(resInterval);
        }
        return res;
    }

    private int[] binSearch_Recursive(int[][] intervals, int target, int l, int r) {
        if (l >= r) {
            if (intervals[l][0] >= target) return intervals[l];
            else return null;
        }

        int mid = l + (r - l) / 2;
        if (intervals[mid][0] < target) return binSearch_Recursive(intervals, target, mid + 1, r);
        else return binSearch_Recursive(intervals, target, l, mid);
    }
```

### #4 Binary Search Iterative
```java
    // TC: O(nlogn), SC: O(n)
    public int[] findRightInterval_BinarySearch_Iterative(int[][] intervals) {
        int[] res = new int[intervals.length];
        Map<int[], Integer> elemToIndexMap = new HashMap<>();
        for (int i = 0; i < intervals.length; i++) elemToIndexMap.put(intervals[i], i);
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));

        for (int[] interval : intervals) {
            int[] resInterval = binSearch_Iterative(intervals, interval[1]);
            res[elemToIndexMap.get(interval)] = resInterval == null ? -1 : elemToIndexMap.get(resInterval);
        }
        return res;
    }

    private int[] binSearch_Iterative(int[][] intervals, int target) {
        int l = 0, r = intervals.length - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (intervals[mid][0] < target) l = mid + 1;
            else r = mid;
        }
        return intervals[l][0] >= target ? intervals[l] : null;
    }
```

### #5 TreeMap
```java
    // TC: O(nlogn), SC: O(n)
    // Inserting into treemap is log(n). we do this n times hence nlogn tc.
    // Also ceilingEntry method takes log(n) time and n such searches are done. Therefore O(nlogn) TC
    public int[] findRightInterval_Treemap(int[][] intervals) {
        int[] res = new int[intervals.length];
        TreeMap<Integer, Integer> map = new TreeMap<>();
        for (int i = 0; i < intervals.length; i++) map.put(intervals[i][0], i);
        for (int i = 0; i < intervals.length; i++) {
            Map.Entry<Integer, Integer> pos = map.ceilingEntry(intervals[i][1]);
            res[i] = pos == null ? -1 : pos.getValue();
        }
        return res;
    }
```

### #6 Two Arrays
```java
    // TC: O(nlogn + n), SC: O(n)
    public int[] findRightInterval_TwoArrays(int[][] startIntervals) {
        int[][] endIntervals = Arrays.copyOf(startIntervals, startIntervals.length);
        Map<int[], Integer> elemToIndexMap = new HashMap<>();
        for (int i = 0; i < startIntervals.length; i++) elemToIndexMap.put(startIntervals[i], i);
        Arrays.sort(startIntervals, Comparator.comparingInt(a -> a[0])); // sort based on start
        Arrays.sort(endIntervals, Comparator.comparingInt(a -> a[1])); // sort based on end

        int j = 0, res[] = new int[startIntervals.length];
        for (int[] endInterval : endIntervals) {
            while (j < startIntervals.length && startIntervals[j][0] < endInterval[1]) j++;
            res[elemToIndexMap.get(endInterval)] = j == startIntervals.length ? -1 : elemToIndexMap.get(startIntervals[j]);
        }
        return res;
    }
```

### #7 PQ
```java
    // TC: O(nlogn), SC: O(n)
    public int[] findRightInterval_PQApproach(int[][] intervals) {
        int[] res = new int[intervals.length];
        Queue<Integer> maxStart = new PriorityQueue<>((i, j) -> intervals[j][0] - intervals[i][0]);
        Queue<Integer> maxEnd = new PriorityQueue<>((i, j) -> intervals[j][1] - intervals[i][1]);
        for (int i = 0; i < intervals.length; i++) {
            maxStart.offer(i);
            maxEnd.offer(i);
        }

        for (int i = 0; i < intervals.length; i++) {
            int topEnd = maxEnd.poll();
            res[topEnd] = -1;
            if (intervals[maxStart.peek()][0] >= intervals[topEnd][1]) {
                int topStart = maxStart.poll();
                while (!maxStart.isEmpty() && intervals[maxStart.peek()][0] >= intervals[topEnd][1])
                    topStart = maxStart.poll();
                res[topEnd] = topStart;
                maxStart.offer(topStart);
            }
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---