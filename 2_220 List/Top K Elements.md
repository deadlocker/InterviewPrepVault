
# Top K Numbers(Grokking)
## Intro
![[Pasted image 20220322103609.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(nlogn), SC: O(1)
    public static List<Integer> findKLargestNumbers_BruteForce(int[] nums, int k) {
        Arrays.sort(nums);
        List<Integer> res = new ArrayList<>();
        for (int i = nums.length - 1; i >= nums.length - k; i--) res.add(nums[i]);
        return res;
    }
```

### #2 Minheap
```java
    // TC: O(nlogk + (n-k)logk + klogk), SC: O(k)
    // TC: O(2nlogk), SC: O(k)
    public static List<Integer> findKLargestNumbers_MinHeap(int[] nums, int k) {
        Queue<Integer> pq = new PriorityQueue<>();
        for (int num : nums) {
            pq.offer(num);
            if (pq.size() > k) pq.poll();
        }
        List<Integer> res = new ArrayList<>();
        while (!pq.isEmpty()) res.add(pq.poll());
        return res;
    }
    
    // TC: O(klogk + (n-k)logk + nlogk), SC: O(k)
    // TC: O(2nlogk), SC: O(k)
    public static List<Integer> findKLargestNumbers_Grokking_Min_Heap(int[] nums, int k) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(Comparator.comparingInt(n -> n));
        // put first 'K' numbers in the min heap
        for (int i = 0; i < k; i++) minHeap.add(nums[i]);

        // go through the remaining numbers of the array, if the number from the array is bigger than the
        // top (smallest) number of the min-heap, remove the top number from heap and add the number from array
        for (int i = k; i < nums.length; i++) {
            if (nums[i] > minHeap.peek()) {
                minHeap.poll();
                minHeap.add(nums[i]);
            }
        }

        // the heap has the top 'K' numbers, return them in a list
        return new ArrayList<>(minHeap);
    }
```

### #3 Max Heap
```java
    // TC: O(nlogn + klogn), SC: O(n)
    public static List<Integer> findKLargestNumbers_MaxHeap(int[] nums, int k) {
        Queue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        for (int num : nums) pq.offer(num);

        List<Integer> res = new ArrayList<>();
        while (!pq.isEmpty() && res.size() < k) res.add(pq.poll());
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Kth Largest Element In An Array
## Intro
https://leetcode.com/problems/kth-largest-element-in-an-array/
![[Pasted image 20220322103935.png]]

## Thoughts
- N/A

## Solutions
### #1 Sorting
```java
    // TC: O(nlogn), SC: O(1)
    public int findKthLargest_Sorting(int[] nums, int k) {
        Arrays.sort(nums);
        return nums[nums.length - k];
    }
```

### #2 MinHeap
```java
    // TC: O(nlogk + (n-k)logk), SC: O(k)
    public int findKthLargest_MinHeap(int[] nums, int k) {
        Queue<Integer> pq = new PriorityQueue<>();
        for (int i = 0; i < nums.length; i++) {
            pq.offer(nums[i]);
            if (i >= k) pq.poll();
        }
        return pq.poll();
    }
```

### #3 MaxHeap
```java
    // TC: O(nlogn + klogn), SC: O(n)
    public int findKthLargest_MaxHeap(int[] nums, int k) {
        Queue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        for (int num : nums) pq.offer(num);
        int i = 0;
        while (i++ < k - 1) pq.poll();
        return pq.poll();
    }
```

### #4 Quick Select
```java
    // TC: O(n), SC: O(1) avg case
    // TC: O(n^2), SC: O(1) worst case
    public int findKthLargest_QuickSelect(int[] A, int k) {
        k = A.length - k;
        int i = 0, j = A.length - 1;
        while (i <= j) {
            int pivot = partition(A, i, j);
            if (k == pivot) return A[k];
            if (k > pivot) i = pivot + 1;
            else j = pivot - 1;
        }
        return -1;
    }

    private int partition(int[] A, int l, int r) {
        // note you can also use this randomized partition for faster operation
//        int pivotIndex = l + random.nextInt(r - l + 1), k = l, pivot = A[pivotIndex];
        int pivotIndex = l + (r - l) / 2, k = l, pivot = A[pivotIndex];
        swap(A, pivotIndex, r);
        for (int i = l; i <= r - 1; i++) if (A[i] <= pivot) swap(A, i, k++);
        swap(A, k, r);
        return k;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Kth Closest Points To Origin
## Intro
https://leetcode.com/problems/k-closest-points-to-origin/
![[Pasted image 20220322104050.png]]
![[Pasted image 20220322104057.png]]
## Thoughts
- N/A

## Solutions
### #1 Sorting
```java
    // TC: O(nlogn), SC: O(n)
    public int[][] kClosest_UsingSort(int[][] points, int k) {
        Arrays.sort(points, Comparator.comparingInt(p -> p[0] * p[0] + p[1] * p[1]));
//        Arrays.sort(points, (p, q) -> p[0] * p[0] + p[1] * p[1] - (q[0] * q[0] + q[1] * q[1]));
        return Arrays.copyOfRange(points, 0, k);
    }
```

### #2 Minheap
```java
    // TC: O(2nlogn - klogn), SC: O(n), SC for points array, if not it can be O(k) coz of heap size.
    public int[][] kClosest_MinHeap(int[][] points, int k) {
        Queue<int[]> minHeap = new PriorityQueue<>(Comparator.comparingInt(p -> p[1] * p[1] + p[0] * p[0]));
        minHeap.addAll(Arrays.asList(points));
        int res[][] = new int[k][2], i = 0;
        while (minHeap.size() > points.length - k) res[i++] = minHeap.poll();
        return res;
    }
```

### #3 Maxheap
```java
    // TC: O(2nlogk + klogk), SC: O(n), SC for points array, if not it can be O(k) coz of heap size.
    public int[][] kClosest_MaxHeap(int[][] points, int k) {
        Queue<int[]> maxHeap = new PriorityQueue<>((p, q) -> q[1] * q[1] + q[0] * q[0] - (p[1] * p[1] + p[0] * p[0]));

        for (int[] point : points) {
            maxHeap.offer(point);
            if (maxHeap.size() > k) maxHeap.poll();
        }
        return maxHeap.toArray(new int[0][]);
    }
```

### #4 QuickSelect
```java
    // TC: O(n), SC: O(1)
    public int[][] kClosest(int[][] points, int k) {
        int n = points.length, l = 0, r = n - 1, pivotIndex = n;
        while (pivotIndex != k - 1) {
            pivotIndex = partition(points, l, r);
            if (pivotIndex < k) l = pivotIndex + 1;
            else r = pivotIndex - 1;
        }

        return Arrays.copyOf(points, k);
    }

    private int partition(int[][] A, int l, int r) {
        // l + random.nextInt(r - l + 1) --> can also use randomized pivot
        int pivotIndex = l + (r - l) / 2, pivot[] = A[pivotIndex], k = l;
        swap(A, pivotIndex, r);
        for (int i = l; i <= r - 1; i++) {
            int[] point = A[i];
            if (dist(point) < dist(pivot)) swap(A, i, k++);
        }
        swap(A, k, r);
        return k;
    }
```

### #5 Divide and Conquer
```java
    // TC: O(n) in avg case, O(n^2) in worst case, SC: O(n)
    public int[][] kClosest_DivideAndConquer(int[][] points, int k) {
        sort(points, 0, points.length - 1, k);
        return Arrays.copyOfRange(points, 0, k);
    }

    private void sort(int[][] points, int i, int j, int k) {
        if (i >= j) return;

        // move elem to last
        swap(points, i, j);

        int mid = partition_divideAndConquer(points, i, j);
        int leftLength = mid - i + 1;
        if (k < leftLength) sort(points, i, mid - 1, k);
        else if (k > leftLength) sort(points, mid + 1, j, k - leftLength);
    }

    private int partition_divideAndConquer(int[][] points, int i, int j) {
        int oldI = i, pivot = dist(points, i);
        i++;

        while (true) {
            while (i < j && dist(points, i) < pivot) i++;
            while (i <= j && dist(points, j) > pivot) j--;
            if (i >= j) break;
            swap(points, i, j);
        }
        swap(points, oldI, j);
        return j;
    }

    private int dist(int[][] p, int i) {
        return p[i][0] * p[i][0] + p[i][1] * p[i][1];
    }

    private int dist(int[] A) {
        return A[0] * A[0] + A[1] * A[1];
    }

    private void swap(int[][] p, int i, int j) {
        int[] temp = p[i];
        p[i] = p[j];
        p[j] = temp;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Minimum Cost To Connect Sticks
## Intro
https://leetcode.com/problems/minimum-cost-to-connect-sticks/
![[Pasted image 20220322104245.png]]

## Thoughts
- N/A

## Solutions
### #1 Priority Queue
```java
    // TC: O(nlogn), SC: O(n)
    // Also read the LC solution for greedy algo proof. Its very nicely document
    public int connectSticks_PQ(int[] sticks) {
        int res = 0;
        Queue<Integer> pq = new PriorityQueue<>();
        for (int num : sticks) pq.offer(num);
        while (pq.size() >= 2) {
            int newStick = pq.poll() + pq.poll();
            res += newStick;
            pq.offer(newStick);
        }
        return res;
    }
```

### #2 Sorting
```java
    // TC: O(?), SC: O(?)
    // This is much faster on LC. check why and understand what this approach is all about
    public int connectSticks_Some_Fast_Approach(int[] sticks) {
        if (sticks == null || sticks.length == 0) return 0;

        Arrays.sort(sticks);

        int minTotalCost = 0, i = 0, j = 0, mergesDone = 0, mergesLeft = sticks.length - 1;
        int[] out = new int[sticks.length - 1];

        Arrays.fill(out, Integer.MAX_VALUE);

        while (mergesLeft > 0) {
            int currSum = 0, sticksToPick = 2;

            while (sticksToPick > 0) {
                if (i < sticks.length && sticks[i] <= out[j]) {
                    currSum += sticks[i];
                    i++;
                } else if (j < out.length) {
                    currSum += out[j];
                    j++;
                }
                sticksToPick--;
            }

            out[mergesDone++] = currSum;
            minTotalCost += currSum;
            mergesLeft--;
        }

        return minTotalCost;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Sort Characters By Frequency
## Intro
https://leetcode.com/problems/sort-characters-by-frequency/
![[Pasted image 20220322104425.png]]

## Thoughts
- N/A

## Solutions
### #1 HashMap Sort
```java
    // TC: O(nlogn), SC: O(n) or more precisely
    // TC: O(n + klogk), SC: O(n), where n = s.length(), k is no.of unique chars in s
    public String frequencySort_HashMapSort(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);

        List<Character> keys = new ArrayList<>(map.keySet());
        keys.sort((a, b) -> map.get(b) - map.get(a));

        StringBuilder sb = new StringBuilder();
        for (char c : keys) {
            int copies = map.get(c);
            for (int i = 0; i < copies; i++) sb.append(c);
        }
        return sb.toString();
    }
```

### #2 PQ Approach
```java
    // TC: O(nlogn), SC: O(n)
    public String frequencySort_PQApproach(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);

        Queue<Character> pq = new PriorityQueue<>((a, b) -> map.get(b) - map.get(a));

        map.forEach((k, v) -> pq.offer(k));

        StringBuilder sb = new StringBuilder();
        while (!pq.isEmpty()) {
            char c = pq.poll();
            int freq = map.get(c);
            while (freq-- != 0) sb.append(c);
        }
        return sb.toString();
    }
```

### #3 Bucket Sort
```java
    // TC: O(n), SC: O(n)
    public String frequencySort_BucketSort(String s) {
        if (s == null || s.isEmpty()) return s;

        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);

        int maxFreq = Collections.max(map.values());

        List<List<Character>> buckets = new ArrayList<>();
        for (int i = 0; i <= maxFreq; i++) buckets.add(new ArrayList<>());

        map.forEach((k, freq) -> buckets.get(freq).add(k));

        StringBuilder sb = new StringBuilder();
        for (int i = buckets.size() - 1; i >= 1; i--) {
            for (char c : buckets.get(i)) {
                for (int j = 0; j < i; j++) sb.append(c);
            }
        }
        return sb.toString();
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Kth Largest Element In A Stream
## Intro
https://leetcode.com/problems/kth-largest-element-in-a-stream/
![[Pasted image 20220322104526.png]]

## Thoughts
- N/A

## Solutions
### #1 PQ Approach
```java
public class P703_KthLargestElementInAStream {
    int k;
    Queue<Integer> pq = new PriorityQueue<>();

    public P703_KthLargestElementInAStream(int k, int[] nums) {
        this.k = k;
        for (int num : nums) add(num);
    }

    // TC: O(logk), SC: O(k)
    public int add(int val) {
        pq.offer(val);
        if (pq.size() > this.k) pq.poll();
        return pq.peek();
    }
}
```

## Common Pitfalls to Avoid
- N/A

---

# Find K Closest Elements
## Intro
https://leetcode.com/problems/find-k-closest-elements/
![[Pasted image 20220322104626.png]]

## Thoughts
- N/A

## Solutions
### #1 Sort 
```java

    // TC: O(nlogn), SC: O(k), SC coz of generating sublist of length k
    public List<Integer> findClosestElements_SortApproach(int[] nums, int k, int x) {
        List<Integer> arr = Arrays.stream(nums).boxed().collect(Collectors.toList());
        arr.sort((a, b) -> a.equals(b) ? 0 : abs(a - x) - abs(b - x));
        arr = arr.subList(0, k);
        Collections.sort(arr);
        return arr;
    }
```

### #2 Heap
```java
    // TC: O(nlogk), SC: O(k), SC coz of heap size
    public List<Integer> findClosestElements_HeapApproach(int[] arr, int k, int x) {
        Queue<Integer> pq = new PriorityQueue<>((a, b) -> abs(a - x) == abs(b - x) ? b - a : abs(b - x) - abs(a - x));

        for (int num : arr) {
            pq.add(num);
            if (pq.size() > k) pq.poll();
        }

        List<Integer> res = new ArrayList<>(pq);
        Collections.sort(res);
        return res;
    }
```

### #3 MinHeap Binary Search
```java
    // TC: O(logn + 2klog(2k) + klogk + klogk), SC: O(2k)
    // TC: O(logn + klogk), SC: O(k)
    public List<Integer> findClosestElements_MinHeap_BinarySearch(int[] arr, int k, int x) {
        int index = Arrays.binarySearch(arr, x);
        if (index < 0) index = -index - 1;

        int low = max(index - k, 0), hi = min(index + k, arr.length - 1);

        // arr[0] = distance, arr[1] = index
        Queue<int[]> pq = new PriorityQueue<>((i1, i2) -> i1[0] == i2[0] ? i1[1] - i2[1] : i1[0] - i2[0]);

        for (int i = low; i <= hi; i++) pq.offer(new int[]{abs(arr[i] - x), i});

        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            int[] item = pq.poll();
            int j = item[1];
            res.add(arr[j]);
        }
        Collections.sort(res);
        return res;
    }
```

### #4 Best Short Code
```java
    // TC: O(logn + k), SC: O(k)
    public List<Integer> findClosestElements_Best(int[] arr, int k, int x) {
        int left = 0, right = arr.length - k;
        while (left < right) {
            int mid = (left + right) / 2;
            if (x - arr[mid] > arr[mid + k] - x) left = mid + 1;
            else right = mid;
        }
        List<Integer> res = new ArrayList<>();
        for (int i = left; i < left + k; i++) res.add(arr[i]);
        return res;
    }

    // TC: O(logn), SC: O(k)
    public List<Integer> findClosestElements_BestWithExplanation(int[] arr, int k, int x) {
        //-------- Main idea behind the binary search algorithm ----------//
        // 1) res will be a consecutive subarray of k size
        // 2) say if we need to pick 4 elems, now we r looking at 5 elem n1, n2, n3, n4, n5
        //    we need to compare two ends: diff(x, n1) and diff(x, n5), the number with bigger diff on the end will be eleminated
        //----------------------------------------------------------------//
        // lo and hi: range of all possible start of subarray with size k + 1, so we could compare both ends
        int lo = 0, hi = arr.length - k - 1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            // for subarray starting at mid with size k+1, we compare element of two ends to eliminate the loser
            if (x - arr[mid] > arr[mid + k] - x) {
                lo = mid + 1; // arr[mid] is the one further away from x, eliminate range[lo, mid]
            } else {
                hi = mid - 1; // arr[mid+k] is the one further away, all [mid, hi] will have simiar situation, elimiate them
            }
        }
        // return subarray
        List<Integer> res = new ArrayList<>(k);
        for (int i = lo; i < lo + k; i++) res.add(arr[i]);
        return res;
    }
```

### #5 Binary Search My Way
```java
    // TC: O(logn + k), SC: O(k)
    public List<Integer> findClosestElements_BinarySearch_MyWay(int[] arr, int k, int x) {
        int i = Arrays.binarySearch(arr, x);
        if (i < 0) i = -i - 1;

        int left = i - 1, right = i;

        List<Integer> res = new ArrayList<>();
        while (res.size() < k && left >= 0 && right < arr.length) {
            int leftDiff = abs(arr[left] - x), rightDiff = abs(arr[right] - x);
            if (leftDiff <= rightDiff) res.add(arr[left--]);
            else res.add(arr[right++]);
        }
        while (res.size() < k && left >= 0) res.add(arr[left--]);
        while (res.size() < k && right < arr.length) res.add(arr[right++]);

        Collections.sort(res);
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Least Number of Unique Integers After K Removals
## Intro
https://leetcode.com/problems/least-number-of-unique-integers-after-k-removals/
![[Pasted image 20220322104811.png]]

## Thoughts
- N/A

## Solutions
### #1 PQ Approach
```java
    // TC: O(nlogn), SC: O(n)
    public int findLeastNumOfUniqueInts_PQ(int[] arr, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int num : arr) freq.put(num, freq.getOrDefault(num, 0) + 1);
        Queue<Integer> pq = new PriorityQueue<>(Comparator.comparingInt(freq::get));
        freq.forEach((key, v) -> pq.offer(key));
        while (k > 0 && !pq.isEmpty()) k -= freq.get(pq.poll());
        return k < 0 ? pq.size() + 1 : pq.size();
    }
```

### #2 Counting Approach
```java
    // TC: O(n), SC: O(n)
    public int findLeastNumOfUniqueInts_Counting(int[] arr, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int num : arr) freq.put(num, 1 + freq.getOrDefault(num, 0));
        int remaining = freq.size(), occur = 1, occurrenceCount[] = new int[arr.length + 1];
        freq.values().forEach(v -> ++occurrenceCount[v]);

        while (k > 0) {
            int diff = k - occur * occurrenceCount[occur];
            if (diff >= 0) {
                k = diff;
                remaining -= occurrenceCount[occur++];
            } else return remaining - k / occur;
        }
        return remaining;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Sum of Elements(Grokking)
## Intro
![[Pasted image 20220322104905.png]]

## Thoughts
- N/A

## Solutions
### #1 MinHeap
```java
    // TC: O(nlogn + k1logn + (k2-k1)logn), SC: O(n)
    public static int findSumOfElements_MinHeap_MyWay(int[] nums, int k1, int k2) {
        k2 -= k1;
        Queue<Integer> pq = new PriorityQueue<>();
        for (int num : nums) pq.offer(num);
        while (!pq.isEmpty() && k1-- > 0) pq.poll();

        int sum = 0;
        while (!pq.isEmpty() && --k2 > 0) sum += pq.poll();
        return sum;
    }

    // TC: O(nlogn), SC: O(n)
    public static int findSumOfElements_MinHeap_Grokking(int[] nums, int k1, int k2) {
        Queue<Integer> pq = new PriorityQueue<>();
        // insert all numbers to the min heap
        for (int num : nums) pq.add(num);

        // remove k1 small numbers from the min heap
        for (int i = 0; i < k1; i++) pq.poll();

        int sum = 0;
        // sum next k2-k1-1 numbers
        for (int i = 0; i < k2 - k1 - 1; i++) sum += pq.poll();

        return sum;
    }
```

### #2 MaxHeap
```java
    // // TC: O(nlogk2), SC: O(k2)
    public static int findSumOfElements_MyAnotherWay(int[] A, int k1, int k2) {
        Queue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        for (int num : A) {
            pq.offer(num);
            if (pq.size() > k2 - 1) pq.poll();
        }
        int sum = 0;
        for (int i = 0; i < k2 - k1 - 1; i++) sum += pq.poll();
        return sum;
    }

    // TC: O(nlogk2), SC: O(k2)
    public static int findSumOfElements_MaxHeap_Grokking_Better(int[] nums, int k1, int k2) {
        Queue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        // keep smallest k2 numbers in the max heap
        for (int i = 0; i < nums.length; i++) {
            if (i < k2 - 1) pq.add(nums[i]);
            else if (nums[i] < pq.peek()) {
                pq.poll(); // as we are interested only in the smallest k2 numbers
                pq.add(nums[i]);
            }
        }

        // get the sum of numbers between k1 and k2 indices
        // these numbers will be at the top of the max heap
        int sum = 0;
        for (int i = 0; i < k2 - k1 - 1; i++) sum += pq.poll();

        return sum;
    }
```

### #3 Quick Select
```java
    // TC: O(3n), SC: O(1)
    public static int findSumOfElements_QuickSelect(int[] A, int k1, int k2) {
        // subtract -- coz arrays are 0 indexed while question asks 1st smallest number etc
        quickSelect(A, k1--);
        quickSelect(A, k2--);
        int sum = 0;
        for (int i = k1 + 1; i < k2; i++) sum += A[i];
        return sum;
    }

    private static void quickSelect(int[] A, int k) {
        int l = 0, r = A.length - 1;
        while (l <= r) {
            int pivot = partition(A, l, r);
            if (pivot == k) return;
            if (pivot < k) l = pivot + 1;
            else r = pivot - 1;
        }
    }

    private static int partition(int[] A, int l, int r) {
        int pivotIndex = l + (r - l) / 2, pivot = A[pivotIndex], k = l;
        swap(A, pivotIndex, r);
        for (int i = l; i <= r - 1; i++) if (A[i] <= pivot) swap(A, i, k++);
        swap(A, k, r);
        return k;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Reorganize String
## Intro
https://leetcode.com/problems/reorganize-string/
![[Pasted image 20220322105118.png]]

## Thoughts
- N/A

## Solutions
### #1 MaxHeap
```java
    // TC: O(nlogn), SC: O(n)
    public String reorganizeString_MaxHeap(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);

        Queue<Character> pq = new PriorityQueue<>((a, b) -> map.get(b) - map.get(a));
        pq.addAll(map.keySet());

        StringBuilder sb = new StringBuilder();
        Character prev = null;
        while (!pq.isEmpty()) {
            Character current = pq.poll();
            if (prev != null && map.get(prev) > 0) pq.offer(prev);
            sb.append(current);
            map.put(current, map.get(current) - 1);
            prev = current;
        }
        return sb.length() == s.length() ? sb.toString() : "";
    }
```

### #2 Best
```java
    // TC: O(n), SC: O(n + 26)
    // todo how does this work?
    public String reorganizeString_Best(String s) {
        int freq[] = new int[26], n = s.length();
        for (int i = 0; i < n; i++) freq[s.charAt(i) - 'a']++;
        int max = 0, letter = 0;
        for (int i = 0; i < freq.length; i++) {
            if (freq[i] > max) {
                max = freq[i];
                letter = i;
            }
        }
        if (max > (n + 1) / 2) return "";

        char[] res = new char[n];
        int idx = 0;
        while (freq[letter]-- > 0) {
            res[idx] = (char) (letter + 'a');
            idx += 2;
        }
        for (int i = 0; i < freq.length; i++) {
            while (freq[i]-- > 0) {
                if (idx >= res.length) idx = 1;
                res[idx] = (char) (i + 'a');
                idx += 2;
            }
        }
        return String.valueOf(res);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Rearrange String K Distance Apart
## Intro
https://leetcode.com/problems/rearrange-string-k-distance-apart/
![[Pasted image 20220322105204.png]]

## Thoughts
- N/A

## Solutions
### #1 MaxHeap
```java
    // TC: O(nlogn), SC: O(n)
    public String rearrangeString_MaxHeap(String s, int k) {
        if (k <= 1) return s;

        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);

        Queue<Character> pq = new PriorityQueue<>((a, b) -> map.get(b) - map.get(a));

        pq.addAll(map.keySet());

        Queue<Character> q = new LinkedList<>();
        StringBuilder sb = new StringBuilder();
        while (!pq.isEmpty()) {
            Character current = pq.poll();
            sb.append(current);
            map.put(current, map.get(current) - 1);
            q.offer(current);
            if (q.size() == k) {
                Character prev = q.poll();
                if (map.get(prev) > 0) pq.offer(prev);
            }
        }

        return sb.length() == s.length() ? sb.toString() : "";
```

## Common Pitfalls to Avoid
- N/A

---

# Task Scheduler
## Intro
https://leetcode.com/problems/task-scheduler/
![[Pasted image 20220322105304.png]]
![[Pasted image 20220322105309.png]]

## Thoughts
- N/A

## Solutions
### #1 Math
```java
    // TC: O(m), SC: O(1), m = tasks.length. Sorting is also constant since array size is only 26
    public int leastInterval_Math(char[] tasks, int n) {
        int freqs[] = new int[26], fMax = 0;

        for (int t : tasks) {
            freqs[t - 'A']++;
            fMax = Math.max(fMax, freqs[t - 'A']);
        }

        // count the number of most frequent tasks
        int nMax = 0;
        for (int f : freqs) {
            if (f == fMax) nMax++;
        }

        return Math.max(tasks.length, nMax + (fMax - 1) * (n + 1));
    }
```

### #2 Greedy
```java
    // TC: O(m), SC: O(1), m = tasks.length. Sorting is also constant since array size is only 26
    public int leastInterval_Greedy(char[] tasks, int n) {
        int[] freqs = new int[26];

        for (int t : tasks) freqs[t - 'A']++;

        Arrays.sort(freqs);

        int fMax = freqs[25], idleTime = (fMax - 1) * n;

        for (int i = freqs.length - 2; i >= 0; i--) idleTime -= Math.min(freqs[i], fMax - 1);

        // make sure that idleTime doesnt go negative
        return tasks.length + Math.max(0, idleTime);
    }
```

### #3 MaxHeap
```java
    // TC: O(nlogn), SC: O(n)
    public int leastInterval_MaxHeapApproach(char[] tasks, int k) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : tasks) map.put(c, map.getOrDefault(c, 0) + 1);

        int res = 0;
        Queue<Character> pq = new PriorityQueue<>((a, b) -> map.get(b) - map.get(a));
        pq.addAll(map.keySet());

        while (!pq.isEmpty()) {
            List<Character> waitList = new ArrayList<>();
            int n = k + 1;

            while (n > 0 && !pq.isEmpty()) {
                res++;
                Character c = pq.poll();
                if (map.get(c) > 1) {
                    map.put(c, map.get(c) - 1);
                    waitList.add(c);
                }
                n--;
            }

            pq.addAll(waitList);
            if (!pq.isEmpty()) res += n;
        }

        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Max Frequency Stack
## Intro
https://leetcode.com/problems/maximum-frequency-stack/
![[Pasted image 20220322105411.png]]

## Thoughts
- N/A

## Solutions
### #1 MaxHeap
```java
public class P895_MaximumFrequencyStack_MaxHeap {

    int sequenceNumber = 0;
    Map<Integer, Integer> freqMap = new HashMap<>();
    Queue<Item> pq = new PriorityQueue<>((i1, i2) -> i1.frequency == i2.frequency
            ? i2.sequenceNumber - i1.sequenceNumber
            : i2.frequency - i1.frequency);

    // TC: O(logn), SC: O(n)
    public void push(int val) {
        freqMap.put(val, freqMap.getOrDefault(val, 0) + 1);
        pq.offer(new Item(val, sequenceNumber++, freqMap.get(val)));
    }

    // TC: O(logn), SC: O(n)
    public int pop() {
        Item item = pq.poll();
        int num = item.number;

        freqMap.put(num, freqMap.get(num) - 1);
        if (freqMap.get(num) <= 0) freqMap.remove(num);

        return num;
    }

    @AllArgsConstructor
    static
    class Item {
        int number, sequenceNumber, frequency;
    }
}
```

### #2 Stack of Stacks
```java
public class P895_MaximumFrequencyStack_StackOfStacks {
    Map<Integer, Integer> freq = new HashMap<>();
    Map<Integer, Stack<Integer>> group = new HashMap<>();
    int maxFreq;

    // TC: O(1), SC: O(n)
    public void push(int x) {
        int f = freq.getOrDefault(x, 0) + 1;
        freq.put(x, f);
        if (f > maxFreq) maxFreq = f;

        group.computeIfAbsent(f, z -> new Stack<>()).push(x);
    }

    // TC: O(1), SC: O(n)
    public int pop() {
        int x = group.get(maxFreq).pop();
        freq.put(x, freq.get(x) - 1);

        // good for cleanup
        // if(freq.get(x) == 0)freq.remove(x);

        if (group.get(maxFreq).isEmpty()) maxFreq--;
        return x;
    }
}
```

## Common Pitfalls to Avoid
- N/A

---

# Kth Smallest Number
## Intro
![[Pasted image 20220322134003.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // // TC: O(nlogn), SC: O(1)
    public static int findKthSmallestNumber_Grokking_BruteForce_Sort(int[] nums, int k) {
        Arrays.sort(nums);
        return nums[k - 1];
    }

    // todo understand how this works
    // // TC: O(n * k), SC: O(1)
    public static int findKthSmallestNumber_Grokking_Full_BruteForce(int[] nums, int k) {
        int previousSmallestNum = Integer.MIN_VALUE;
        int previousSmallestIndex = -1;
        int currentSmallestNum = Integer.MAX_VALUE;
        int currentSmallestIndex = -1;
        for (int i = 0; i < k; i++) {
            for (int j = 0; j < nums.length; j++) {
                if (nums[j] > previousSmallestNum && nums[j] < currentSmallestNum) {
                    // found the next smallest number
                    currentSmallestNum = nums[j];
                    currentSmallestIndex = j;
                } else if (nums[j] == previousSmallestNum && j > previousSmallestIndex) {
                    // found a number which is equal to the previous smallest number; since numbers can repeat,
                    // we will consider 'nums[j]' only if it has a different index than previous smallest
                    currentSmallestNum = nums[j];
                    currentSmallestIndex = j;
                    break; // break here as we have found our definitive next smallest number
                }
            }
            // current smallest number becomes previous smallest number for the next iteration
            previousSmallestNum = currentSmallestNum;
            previousSmallestIndex = currentSmallestIndex;
            currentSmallestNum = Integer.MAX_VALUE;
        }

        return previousSmallestNum;
    }
```

### #2 MaxHeap
```java
    // TC: O(klogk + (n-k)logk), SC: O(k) or simply
    // TC: O(nlogk), SC: O(k)
    public static int findKthSmallestNumber_Grokking_Max_Heap(int[] nums, int k) {
        Queue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
        // put first k numbers in the max heap
        for (int i = 0; i < k; i++) maxHeap.add(nums[i]);

        // go through the remaining numbers of the array, if the number from the array is smaller than the
        // top (biggest) number of the heap, remove the top number from heap and add the number from array
        for (int i = k; i < nums.length; i++) {
            if (nums[i] < maxHeap.peek()) {
                maxHeap.poll();
                maxHeap.add(nums[i]);
            }
        }

        // the root of the heap has the Kth smallest number
        return maxHeap.peek();
    }

    // TC: O(klogk + (n-k)logk), SC: O(k) or simply
    // TC: O(nlogk), SC: O(k)
    public static int findKthSmallestNumber_Mine_MaxHeap(int[] nums, int k) {
        Queue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        for (int num : nums) {
            pq.offer(num);
            if (pq.size() > k) pq.poll();
        }
        return pq.poll();
    }
```

### #3 QuickSelect
```java
    // TC: O(n), SC: O(1) avg case
    // TC: O(n^2), SC: O(1) worst case
    public static int findKthSmallestNumber_Mine_QuickSelect(int[] nums, int k) {
        int l = 0, r = nums.length - 1;
        while (l <= r) {
            int i = partition(nums, l, r);
            if (i == k - 1) return nums[i];
            else if (i > k - 1) r = i - 1;
            else l = i + 1;
        }
        return -1;
    }

    private static int partition(int[] nums, int l, int r) {
        int pivot = nums[r], i = l;
        for (int j = l; j <= r - 1; j++) {
            if (nums[j] <= pivot) swap(nums, j, i++);
        }
        swap(nums, i, r);
        return i;
    }

    // note you can also use this randomized partition for faster operation
    private static int partition_randomized(int[] nums, int l, int r) {
        int pivotIndex = l + random.nextInt(r - l + 1);
        int pivot = nums[pivotIndex], i = l;
        swap(nums, r, pivotIndex);
        for (int j = l; j <= r - 1; j++) {
            if (nums[j] <= pivot) swap(nums, j, i++);
        }
        swap(nums, i, r);
        return i;
    }

    // TC: O(n), SC: O(1) avg case
    // TC: O(n^2), SC: O(1) worst case
    public static int findKthSmallestNumber_Grokking_QuickSelect(int[] nums, int k) {
        return findKthSmallestNumberRec_QuickSelect(nums, k, 0, nums.length - 1);
    }

    public static int findKthSmallestNumberRec_QuickSelect(int[] nums, int k, int start, int end) {
        int p = partition_Grokking(nums, start, end);

        if (p == k - 1) return nums[p];

        // search lower part
        if (p > k - 1) return findKthSmallestNumberRec_QuickSelect(nums, k, start, p - 1);

        // search higher part
        return findKthSmallestNumberRec_QuickSelect(nums, k, p + 1, end);
    }

    private static int partition_Grokking(int[] nums, int low, int high) {
        if (low == high) return low;

        int pivot = nums[high];
        for (int i = low; i < high; i++) {
            // all elements less than 'pivot' will be before the index 'low'
            if (nums[i] < pivot) swap(nums, low++, i);
        }
        // put the pivot in its correct place
        swap(nums, low, high);
        return low;
    }
```

### #4 Median of Medians
```java

    private static Random random = new Random();

    // TC: O(N), SC: O(N)
    // Note: This has a guaranteed worst case TC of O(n) unlike quick select which has TC O(n^2) in worst case
    public static int findKthSmallestNumber_Grokking_MedianOfMedians(int[] nums, int k) {
        return findKthSmallestNumberRec_MedianOfMedians(nums, k, 0, nums.length - 1);
    }

    public static int findKthSmallestNumberRec_MedianOfMedians(int[] nums, int k, int start, int end) {
        int p = partition_medianOfMedians(nums, start, end);

        if (p == k - 1) return nums[p];

        // search the lower part
        if (p > k - 1) return findKthSmallestNumberRec_MedianOfMedians(nums, k, start, p - 1);

        // search the higher part
        return findKthSmallestNumberRec_MedianOfMedians(nums, k, p + 1, end);
    }

    private static int partition_medianOfMedians(int[] nums, int low, int high) {
        if (low == high) return low;

        int median = medianOfMedians(nums, low, high);
        // find the median in the array and swap it with 'nums[high]' which will become our pivot
        for (int i = low; i < high; i++) {
            if (nums[i] == median) {
                swap(nums, i, high);
                break;
            }
        }

        int pivot = nums[high];
        for (int i = low; i < high; i++) {
            // all elements less than 'pivot' will be before the index 'low'
            if (nums[i] < pivot) swap(nums, low++, i);
        }
        // put the pivot in its correct place, remember nums[high] is our pivot
        swap(nums, low, high);
        return low;
    }

    private static int medianOfMedians(int[] nums, int low, int high) {
        int n = high - low + 1;
        // if we have less than 5 elements, ignore the partitioning algorithm
        if (n < 5) return nums[low];

        // for simplicity, lets ignore any partition with less than 5 elements
        int numOfPartitions = n / 5; // represents total number of 5 elements partitions
        int[] medians = new int[numOfPartitions];
        for (int i = 0; i < numOfPartitions; i++) {
            int partitionStart = low + i * 5; // starting index of the current partition
            Arrays.sort(nums, partitionStart, partitionStart + 5); // sort the 5 elements array
            medians[i] = nums[partitionStart + 2]; // get the middle element (or the median)
        }

        return partition_medianOfMedians(medians, 0, numOfPartitions - 1);
    }
```

## Common Pitfalls to Avoid
- N/A

---