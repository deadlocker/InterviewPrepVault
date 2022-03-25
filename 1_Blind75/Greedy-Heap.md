# Greedy/Heap

# [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

## Intro

![Untitled](1_Blind75/Greedy%20Hea%2034f16/Untitled.png)

## Thoughts

- Whenever there is topK, think of PQ

## Solutions

### #1 PQ nlogn

```java
// TC: O(nlogn), SC: O(2n), PQ size is full n
public int[] topKFrequent_PQ_Array(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();

    for (int num : nums) map.put(num, map.getOrDefault(num, 0) + 1);

    // a[0] is the actual number while a[1] is the frequency
    Queue<int[]> pq = new PriorityQueue<>((a, b) -> b[1] - a[1]);

    map.forEach((num, freq) -> pq.add(new int[]{num, freq}));

    int res[] = new int[k];
    while (k > 0) res[--k] = pq.poll()[0];
    return res;
}
```

### #2 PQ nlogk

```java
// This is better than topKFrequent_PQ_Array
// TC: O(nlogk), SC: O(n+k), PQ size is always <= k
public int[] topKFrequent_PQ_Map_Better(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int num : nums) map.put(num, map.getOrDefault(num, 0) + 1);

    // minheap
    Queue<Integer> pq = new PriorityQueue<>(Comparator.comparingInt(map::get));

    // n - k times poll the kick out the min. At the end of this loop, we are left with topk elements only in PQ
    for (int key : map.keySet()) {
        pq.offer(key);
        if (pq.size() > k) pq.poll();
    }

    int[] res = new int[k];
    while (!pq.isEmpty()) res[--k] = pq.poll();
    return res;
```

### #3 Bucket sort O(n) (redo this and understand it a bit more)

```java
// TC: O(n), SC: O(n)
public int[] topKFrequent_BucketSort(int[] A, int k) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int num : A) map.put(num, map.getOrDefault(num, 0) + 1);

    int n = A.length;
    List<Integer>[] buckets = new List[n];

    map.forEach((num, freq) -> {
        int index = n - freq;
        if (buckets[index] == null) buckets[index] = new ArrayList<>();
        buckets[index].add(num);
    });

    int i = 0, res[] = new int[k];

    for (List<Integer> bucket : buckets) {
        if (bucket == null) continue;

        for (int num : bucket) {
            res[i++] = num;
            if (i == k) return res;
        }
    }

    return res;
}
```

### #4 QuickSelect Recursive

```java
int[] unique;
Map<Integer, Integer> count = new HashMap<>();
private final Random random = new Random();

// TC: O(n) avg/best case, SC: O(n), SC coz of hashmap and unique array.
// TC: O(n^2) in worst case
public int[] topKFrequent(int[] nums, int k) {
    for (int num : nums) count.put(num, count.getOrDefault(num, 0) + 1);

    int n = count.size(), i = 0;
    unique = new int[n];
    for (int num : count.keySet()) unique[i++] = num;

    quickselect(0, n - 1, n - k);
    return Arrays.copyOfRange(unique, n - k, n);
}

// put kth smallest/less frequent in its rightful position.
// k is zero indexed btw. 3rd smallest is actually index 2.
private void quickselect(int left, int right, int k) {
    if (left == right) return;

    int pivotIndex = left + random.nextInt(right - left);

    pivotIndex = partition(left, right, pivotIndex);

    if (pivotIndex == k) return;
        // go left
    else if (k < pivotIndex) quickselect(left, pivotIndex - 1, k);
        // go right
    else quickselect(pivotIndex + 1, right, k);
}

private int partition(int left, int right, int pivotIndex) {
    int pivotFreq = count.get(unique[pivotIndex]);
    swap(unique, pivotIndex, right);
    int storeIndex = left;

    for (int i = left; i <= right; i++) {
        if (count.get(unique[i]) < pivotFreq) swap(unique, storeIndex++, i);
    }
    swap(unique, storeIndex, right);
    return storeIndex;
}
```

### #5 QuickSelect Iterative

```java
// TC: O(n) avg/best case, SC: O(n), SC coz of hashmap and unique array.
// TC: O(n^2) in worst case
public int[] topKFrequent_QuickSelectIterative(int[] nums, int k) {
    Map<Integer, Integer> freqMap = new HashMap<>();
    for (int num : nums) freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);

    int n = freqMap.size(), A[] = new int[n], i = 0;
    for (int num : freqMap.keySet()) A[i++] = num;

    int pivotIndex = 0, l = 0, r = n - 1;
    k = n - k;
    while (pivotIndex != k) {
        pivotIndex = partition(A, l, r, freqMap);
        if (pivotIndex < k) l = pivotIndex + 1;
        else r = pivotIndex - 1;
    }
    return Arrays.copyOfRange(A, k, n);
}

private int partition(int[] A, int l, int r, Map<Integer, Integer> freqMap) {
    // note you can also use randomized pivotIndex: l + random.nextInt(r - l + 1)
    int pivotIndex = l + (r - l) / 2, k = l;
    swap(A, pivotIndex, r);
    for (int i = l; i <= r - 1; i++) if (freqMap.get(A[i]) <= freqMap.get(A[r])) swap(A, i, k++);
    swap(A, k, r);
    return k;
}
```

## Common Pitfalls to Avoid

- Redo QuickSelect solution

---

# [Meeting Rooms](https://leetcode.com/problems/meeting-rooms/)

## Intro

![Untitled](1_Blind75/Array%20Hash%2037970/Untitled%204.png)

## Thoughts

- Sort based on start point and just check if there is an intersection(if so return false).

## Solutions

### #1 BruteForce

```java
// TC: O(n^2), SC: O(1)
public boolean canAttendMeetings_BruteForce(int[][] intervals) {
    for (int i = 0; i < intervals.length; i++) {
        for (int j = i + 1; j < intervals.length; j++) {
            if (overlap(intervals[i], intervals[j])) return false;
        }
    }
    return true;
}

private boolean overlap(int[] i1, int[] i2) {
    // This can also be written as below:
    // return Math.min(i1[1], i2[1]) > Math.max(i1[0], i2[0]);
    return (i1[0] >= i2[0] && i1[0] < i2[1])
            || (i2[0] >= i1[0] && i2[0] < i1[1]);
}
```

### #2 Sorting

```java
// TC: O(nlogn), SC: O(1)
public boolean canAttendMeetings_SortApproach(int[][] intervals) {
    Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));
    for (int i = 1; i < intervals.length; i++) {
        int[] cur = intervals[i];
        int[] prev = intervals[i - 1];
        if (prev[1] > cur[0]) return false;
    }
    return true;
}
```

### #3 Priority Queue

```java
// TC: O(nlogn), SC: O(n)
public boolean canAttendMeetings_PQ(int[][] A) {
    if (A.length == 0) return true;
    Queue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
    for (int[] cur : A) pq.offer(cur);
    int[] prev = pq.poll();
    while (!pq.isEmpty()) {
        int[] cur = pq.poll();
        if (cur[0] < prev[1]) return false;
        prev = cur;
    }
    return true;
}
```

## Common Pitfalls to Avoid

- When using lamba comparator with Integer objects, don’t do a - b. Instead use [Integer.compare](http://Integer.compare)(a - b). Integer.compare is much more safer than relying on reference comparison. For Integer objects, reference comparison seems to work(but only for values of -128 to 127). [https://javarevisited.blogspot.com/2010/10/what-is-problem-while-using-in.html](https://javarevisited.blogspot.com/2010/10/what-is-problem-while-using-in.html)

---

# [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)

## Intro

![Untitled](1_Blind75/Greedy%20Hea%2034f16/Untitled%201.png)

## Thoughts

- PQ Approach/Simulation is more intuitive than the chronological approach shown below. But how to derive this? Very hard question for me.

## Solutions

### #1 PQ

```java
// TC: O(nlogn), SC: O(n)
public int minMeetingRooms_PQ_Approach(int[][] intervals) {
    if (intervals.length == 0) return 0;

    Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));

    Queue<Integer> pq = new PriorityQueue<>();
    // add first interval's end time
    pq.add(intervals[0][1]);

    for (int i = 1; i < intervals.length; i++) {
        int[] interval = intervals[i];
        // if the room which is due to free up the earliest happens to be free, assign that room to this meeting
        if (interval[0] >= pq.peek()) pq.poll();

        // if a new room is to be assigned, then also we add to the heap,
        // if an old room is allocated, then also we have to add to the heap with updated end time.
        pq.add(interval[1]);
    }

    return pq.size();
}
```

### #2 Chrono Approach

```java
// TC: O(nlogn), SC: O(n)
// This approach is extremely similar to PQ approach above. endPointer corresponds to pq while startPointer corresponds to for loop iteration.
public int minMeetingRooms_Chronological_Approach(int[][] intervals) {
    if (intervals.length == 0) return 0;

    int[] start = new int[intervals.length], end = new int[intervals.length];

    for (int i = 0; i < intervals.length; i++) {
        start[i] = intervals[i][0];
        end[i] = intervals[i][1];
    }

    Arrays.sort(start);
    Arrays.sort(end);

    int startPointer = 0, endPointer = 0, usedRooms = 0;

    while (startPointer < intervals.length) {
        if (start[startPointer] >= end[endPointer]) {
            usedRooms--;
            endPointer++;
        }

        usedRooms++;
        startPointer++;
    }

    return usedRooms;
```

## Common Pitfalls to Avoid

- N/A

---

# Merge K Sorted Lists
## Intro
https://leetcode.com/problems/merge-k-sorted-lists/
![[Pasted image 20220321052048.png]]

## Thoughts
- list

## Solutions
### #1 BruteForce While Loop

```java
    // TC: O(nk), SC: O(n), k is lists.length, n is total number of nodes in final answer
    // SC coz creating ans(a new linked list) takes O(n) space
    public ListNode mergeKLists_BruteForce_WhileLoop(ListNode[] lists) {
        ListNode dummy = new ListNode(0), cur = dummy;
        int minIndex = 0;
        while (true) {
            boolean shouldBreak = true;
            int min = Integer.MAX_VALUE;
            for (int i = 0; i < lists.length; i++) {
                if (lists[i] != null) {
                    if (lists[i].val < min) {
                        min = lists[i].val;
                        minIndex = i;
                    }
                    shouldBreak = false;
                }
            }
            if (shouldBreak) break;
            cur.next = new ListNode(lists[minIndex].val);
            cur = cur.next;
            lists[minIndex] = lists[minIndex].next;
        }
        cur.next = null;
        return dummy.next;
    }
```

### #2 BruteForce Do-While Loop
```java
    // TC: O(nk), SC: O(n), k is lists.length, n is total number of nodes in final answer
    // SC coz creating ans(a new linked list) takes O(n) space
    public ListNode mergeKLists_BruteForce_DoWhile(ListNode[] lists) {
        ListNode dummy = new ListNode(-1), cur = dummy;
        int minIndex = -1;
        do {
            minIndex = -1;
            int minValue = Integer.MAX_VALUE;
            for (int i = 0; i < lists.length; i++) {
                if (lists[i] != null && lists[i].val < minValue) {
                    minValue = lists[i].val;
                    minIndex = i;
                }
            }
            if (minIndex != -1) {
                cur.next = lists[minIndex];
                cur = cur.next;
                lists[minIndex] = lists[minIndex].next;
            }
        } while (minIndex != -1);
        return dummy.next;
    }
```

### #3 Dump All Into PQ
```java
    // TC: O(nlogn), SC: O(n)
    public ListNode mergeKLists_Dump_All_Into_PQ(ListNode[] lists) {
        Queue<ListNode> pq = new PriorityQueue<>((a, b) -> a.val - b.val);
        // another way to iterate as well
//        for(ListNode ln : lists) {
//            while(ln != null) {
//                pq.add(ln);
//                ln = ln.next;
//            }
//        }
        while (true) {
            boolean shouldBreak = true;
            for (int i = 0; i < lists.length; i++) {
                if (lists[i] != null) {
                    pq.add(lists[i]);
                    lists[i] = lists[i].next;
                    shouldBreak = false;
                }
            }
            if (shouldBreak) break;
        }

        ListNode dummy = new ListNode(0), cur = dummy;
        while (!pq.isEmpty()) {
            ListNode node = pq.poll();
            node.next = null;
            cur.next = node;
            cur = cur.next;
        }
        return dummy.next;
    }
```

### #4  Dump All Into ArrayList
```java
    // TC: O(nlogn), SC: O(n)
    public ListNode mergeKLists_Dump_All_Into_ArrayList(ListNode[] lists) {
        List<Integer> list = new ArrayList<>();

        for (ListNode cur : lists) {
            while (cur != null) {
                list.add(cur.val);
                cur = cur.next;
            }
        }

        Collections.sort(list);

        ListNode dummy = new ListNode(0), cur = dummy;
        for (int num : list) {
            cur.next = new ListNode(num);
            cur = cur.next;
        }
        return dummy.next;
    }
```

### #5 PQ's Best Approach
```java
    // TC: O(nlogk), SC: O(k), SC coz of PQ size
    public ListNode mergeKLists_Best_PQ_Approach(ListNode[] lists) {
        Queue<ListNode> pq = new PriorityQueue<>((a, b) -> a.val - b.val);

        for (ListNode node : lists) if (node != null) pq.offer(node);

        ListNode backup = new ListNode(-1), cur = backup;
        while (!pq.isEmpty()) {
            ListNode node = pq.poll();
            cur.next = node;
            cur = cur.next;
            if (node.next != null) pq.add(node.next);
        }
        return backup.next;
    }
```

### #6 Divide And Conquer the best known solution
```java
    // TC: O(nlogk), SC: O(1)
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) return null;
        for (int interval = 1; interval < lists.length; interval *= 2) {
            for (int i = 0; i < lists.length - interval; i += (2 * interval)) {
                lists[i] = mergeTwoLists(lists[i], lists[i + interval]);
            }
        }
        return lists[0];
    }

    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1), cur = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        cur.next = l1 == null ? l2 : l1;
        return dummy.next;
    }
```


## Common Pitfalls to Avoid
- N/A

---