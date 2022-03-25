
# Interval List Intersections
## Intro
https://leetcode.com/problems/interval-list-intersections/
![[Pasted image 20220322112523.png]]
## Thoughts
- N/A

## Solutions
### #1 LC Way
```java
    // TC: O(m+n), SC: O(m+n)
    public int[][] intervalIntersection_LCWay(int[][] first, int[][] second) {
        List<int[]> res = new ArrayList<>();
        int i = 0, j = 0;
        while (i < first.length && j < second.length) {
            int[] a = first[i], b = second[j];
            int lo = max(a[0], b[0]);
            int hi = min(a[1], b[1]);
            if (lo <= hi) res.add(new int[]{lo, hi});

            if (a[1] < b[1]) i++;
            else j++;
        }
        return res.toArray(new int[0][]);
    }
```

### #2 My Way
```java
    // TC: O(m+n), SC: O(m+n)
    public int[][] intervalIntersection_MyWay(int[][] firstList, int[][] secondList) {
        List<int[]> res = new ArrayList<>();
        int i = 0, j = 0;
        while (i < firstList.length && j < secondList.length) {
            int[] a = firstList[i], b = secondList[j];
            // if overlap
            if (a[0] <= b[1] && b[0] <= a[1]) {
                int min = max(a[0], b[0]);
                int max = min(a[1], b[1]);
                res.add(new int[]{min, max});
            }

            // think carefully about why we need to check a[1] <= b[1] here instead of a[1] <= b[0]?
            // this is because if we check a[1] <= b[1] we can be sure that after i++, we won' have any intersection left.
            // try visualizing it on paper and see the difference between two conditions. This is what tripped you before.
            if (a[1] <= b[1]) i++;
            else j++;
        }
        return res.toArray(new int[0][]);
    }

    // TC: O(m+n), SC: O(m+n)
    public int[][] intervalIntersection_MyWay_AnotherWay(int[][] A, int[][] B) {
        int i = 0, j = 0;
        List<int[]> res = new ArrayList<>();
        while (i < A.length && j < B.length) {
            if (overlap(A[i], B[j])) {
                int min = Math.max(A[i][0], B[j][0]);
                int max = Math.min(A[i][1], B[j][1]);
                res.add(new int[]{min, max});
            }
            if (A[i][1] < B[j][1]) i++;
            else j++;
        }
        return res.toArray(new int[0][]);
    }

    private boolean overlap(int[] A, int[] B) {
        return (B[0] <= A[1] && B[1] >= A[0]);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Employee Free Time
## Intro
https://leetcode.com/problems/employee-free-time/
![[Pasted image 20220322112620.png]]

## Thoughts
- N/A

## Solutions
### #1 Using Merge Intervals
```java
    // TC: O(nlogn), SC: O(n)
    public List<Interval> employeeFreeTime_UsingMergeIntervals(List<List<Interval>> schedule) {
        List<Interval> res = new ArrayList<>();
        List<int[]> all = new ArrayList<>();
        for (List<Interval> emps : schedule) {
            for (Interval i : emps) all.add(new int[]{i.start, i.end});
        }

        all.sort(Comparator.comparingInt(a -> a[0]));
        int[][] merged = merge(all.toArray(new int[0][]));

        for (int i = 1; i < merged.length; i++) {
            int[] prev = merged[i - 1], cur = merged[i];
            if (cur[0] > prev[1]) res.add(new Interval(prev[1], cur[0]));
        }
        return res;
    }

    // TC: O(nlogn), SC: O(logn or n), SC depends on sort whether its in place or not.
    public int[][] merge(int[][] intervals) {
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));
        List<int[]> res = new ArrayList<>();
        for (int[] cur : intervals) {
            if (res.isEmpty() || !overlap_long(res.get(res.size() - 1), cur)) res.add(cur);
            else res.get(res.size() - 1)[1] = Math.max(res.get(res.size() - 1)[1], cur[1]);
        }

        return res.toArray(new int[0][]);
    }

    private boolean overlap_long(int[] a, int[] b) {
        return (b[0] >= a[0] && b[0] <= a[1]) || (b[0] <= a[0] && b[1] >= a[0]);
    }

    @AllArgsConstructor
    static class EmployeeInterval {
        Interval interval;
        int employeeIndex;
        int intervalIndex;
    }

    @AllArgsConstructor
    static class Job {
        int eid, index;
    }
```

### #2 MergeInterval Better
```java
    // TC: O(nlogn), SC: O(n)
    public List<Interval> employeeFreeTime_MergeInterval_Better(List<List<Interval>> schedule) {
        List<Interval> intervals = new ArrayList<>(), res = new ArrayList<>();
        for (List<Interval> emps : schedule) intervals.addAll(emps);
        intervals.sort(Comparator.comparingInt(a -> a.start));

        int end = intervals.get(0).end;

        for (int i = 1; i < intervals.size(); i++) {
            Interval interval = intervals.get(i);

            if (interval.start > end) res.add(new Interval(end, interval.start));

            end = Math.max(end, interval.end);
        }
        return res;
    }
```

### #3 Mine
```java
    // TC: O(nlogk), SC: O(k), n is total no.of intervals across all employees, k is total no.of employees itself
    public List<Interval> employeeFreeTime_Mine(List<List<Interval>> schedule) {
        List<Interval> res = new ArrayList<>();
        Queue<EmployeeInterval> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a.interval.start));
        for (int i = 0; i < schedule.size(); i++)
            pq.offer(new EmployeeInterval(schedule.get(i).get(0), i, 0));

        Interval prev = pq.peek().interval;
        while (!pq.isEmpty()) {
            EmployeeInterval top = pq.poll();
            // if not overlapping
            if (top.interval.start > prev.end) {
                res.add(new Interval(prev.end, top.interval.start));
                prev = top.interval;
            } else if (prev.end < top.interval.end) prev = top.interval;

            List<Interval> es = schedule.get(top.employeeIndex);
            if (top.intervalIndex + 1 < es.size()) {
                pq.offer(new EmployeeInterval(es.get(top.intervalIndex + 1), top.employeeIndex, top.intervalIndex + 1));
            }
        }
        return res;
    }
```

### #4 LC Better
```java
    // TC: O(nlogk), SC: O(k), n is total no.of intervals across all employees, k is total no.of employees itself
    public List<Interval> employeeFreeTime_LC_Way(List<List<Interval>> avails) {
        List<Interval> ans = new ArrayList<>();
        Queue<Job> pq = new PriorityQueue<>(Comparator.comparingInt(a -> avails.get(a.eid).get(a.index).start));
        int ei = 0, anchor = Integer.MAX_VALUE;

        for (List<Interval> employee : avails) {
            pq.offer(new Job(ei++, 0));
            anchor = Math.min(anchor, employee.get(0).start);
        }

        while (!pq.isEmpty()) {
            Job job = pq.poll();
            Interval interval = avails.get(job.eid).get(job.index);
            if (anchor < interval.start) ans.add(new Interval(anchor, interval.start));
            anchor = Math.max(anchor, interval.end);
            if (++job.index < avails.get(job.eid).size()) pq.offer(job);
        }

        return ans;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Maximum CPU Load
## Intro
![[Pasted image 20220322112814.png]]

## Thoughts
- N/A

## Solutions
### #1 Grokking
```java
    // TC: O(nlogn), SC: O(n)
    public static int findMaxCPULoad_Grokking(List<Job> jobs) {
        // sort the jobs by start time
        jobs.sort(Comparator.comparingInt(a -> a.start));

        int maxCPULoad = 0, currentCPULoad = 0;
        PriorityQueue<Job> minHeap = new PriorityQueue<>(Comparator.comparingInt(a -> a.end));
        for (Job job : jobs) {
            // remove all jobs that have ended
            while (!minHeap.isEmpty() && job.start > minHeap.peek().end)
                currentCPULoad -= minHeap.poll().cpuLoad;

            // add the current job into the minHeap
            minHeap.offer(job);
            currentCPULoad += job.cpuLoad;
            maxCPULoad = max(maxCPULoad, currentCPULoad);
        }
        return maxCPULoad;
    }

    @AllArgsConstructor
    static
    class Job {
        int start;
        int end;
        int cpuLoad;
    }
```

### #2 Mine
```java
    // TC: O(nlogn), SC: O(n)
    public static int findMaxCPULoad_Mine(List<Job> jobs) {
        if (jobs.isEmpty()) return 0;

        jobs.sort(Comparator.comparingInt(a -> a.start));
        // [x, y], x is end time, y is cpu load
        Queue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
        Job firstJob = jobs.get(0);
        pq.offer(new int[]{firstJob.end, firstJob.cpuLoad});
        int max = firstJob.cpuLoad, curCpuLoad = max;
        for (int i = 1; i < jobs.size(); i++) {
            Job cur = jobs.get(i);
            if (cur.start >= pq.peek()[0]) {
                int[] arr = pq.poll();
                curCpuLoad = min(0, curCpuLoad - arr[1]);
            }
            pq.offer(new int[]{cur.end, cur.cpuLoad});
            curCpuLoad += cur.cpuLoad;
            max = max(max, curCpuLoad);
        }
        return max;
    }
```

## Common Pitfalls to Avoid
- N/A

---