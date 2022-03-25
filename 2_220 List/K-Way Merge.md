
# Kth Smallest Number in M Sorted Lists
## Intro
![[Pasted image 20220322105529.png]]
## Thoughts
- N/A

## Solutions
### #1 MinHeap
```java
    // TC: O(klogm), SC: O(m), SC coz of minHeap will be storing at least one element from all M arrays
    // MinHeap size is bounded by m(not k)
    public static int findKthSmallest_Grokking_MinHeap(List<Integer[]> lists, int k) {
        Queue<Node> pq = new PriorityQueue<>(Comparator.comparingInt(n -> lists.get(n.arrayIndex)[n.elementIndex]));

        // put the 1st element of each array in the min heap
        for (int i = 0; i < lists.size(); i++) if (lists.get(i) != null) pq.offer(new Node(0, i));

        // take the smallest (top) element form the min heap, if the running count is equal to k return the number
        // if the array of the top element has more elements, add the next element to the heap
        int numberCount = 0, result = 0;
        while (!pq.isEmpty()) {
            Node node = pq.poll();
            result = lists.get(node.arrayIndex)[node.elementIndex];
            if (++numberCount == k) break;
            node.elementIndex++;
            if (lists.get(node.arrayIndex).length > node.elementIndex) pq.offer(node);
        }
        return result;
    }

    @AllArgsConstructor
    static class Node {
        int elementIndex;
        int arrayIndex;
    }
```

### #2 Short Code
```java
    // TC: O(klogm), SC: O(m)
    public static int findKthSmallest_MyWay(List<Integer[]> lists, int k) {
        Queue<Item> pq = new PriorityQueue<>((i1, i2) -> lists.get(i2.outerIndex)[i2.innerIndex] - lists.get(i1.outerIndex)[i1.innerIndex]);
        for (int i = 0; i < lists.size(); i++) pq.offer(new Item(0, i));

        while (!pq.isEmpty() && k-- >= 0) {
            Item item = pq.poll();
            if (item.innerIndex + 1 < lists.get(item.outerIndex).length) {
                pq.offer(new Item(item.innerIndex + 1, item.outerIndex));
            }
        }
        Item res = pq.peek();
        return lists.get(res.outerIndex)[res.innerIndex];
    }

    @AllArgsConstructor
    static class Item {
        int outerIndex, innerIndex;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Kth Smallest Element In A Sorted Matrix
## Intro
https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/
![[Pasted image 20220322105819.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(mn log(mn)), SC: O(mn), where matrix is of order m by n
    public int kthSmallest_BruteForce(int[][] matrix, int k) {
        List<Integer> list = new ArrayList<>();
        for (int[] ints : matrix) {
            for (int j = 0; j < matrix[0].length; j++) list.add(ints[j]);
        }
        Collections.sort(list);
        return list.get(k - 1);
    }
```

### #2 PQ BruteForce
```java
    // TC: O(n^2logk + (n^2-k)logk), SC: O(k), where matrix is of order n by n
    // TC: O(2n^2logk - klogk), SC: O(k), SC coz of PQ
    public int kthSmallest_PQ_BruteForce(int[][] matrix, int k) {
        Queue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                pq.offer(matrix[i][j]);
                if (pq.size() > k) pq.poll();
            }
        }
        return pq.poll();
    }
```

### #3 PQ LC Way
```java
    // TC: O(x + klog(x)), SC: O(x), x = min(k,n)
    public int kthSmallest_PQ_LCWay(int[][] matrix, int k) {
        int n = matrix.length;
        Queue<int[]> pq = new PriorityQueue<>(min(n, k), Comparator.comparingInt(a -> a[0]));

        for (int r = 0; r < min(n, k); r++) pq.offer(new int[]{matrix[r][0], r, 0});

        int[] elem = pq.peek();
        while (k-- > 0) {
            elem = pq.poll();
            int r = elem[1], c = elem[2];

            if (c < n - 1) pq.offer(new int[]{matrix[r][c + 1], r, c + 1});
        }
        return elem[0];
    }
```

### #4 Binary Search
```java
    // TC: O(n*log(max - min)), SC: O(1), max is max element in matrix, min is min element in matrix
    public int kthSmallest_BinarySearch_LCWay(int[][] matrix, int k) {
        int n = matrix.length;
        int lo = matrix[0][0], hi = matrix[n - 1][n - 1];
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            int[] smallLargePair = {matrix[0][0], matrix[n - 1][n - 1]};

            int count = smallerThan(matrix, mid, smallLargePair);

            if (count == k) return smallLargePair[0];

            if (count < k) lo = smallLargePair[1];
            else hi = smallLargePair[0];
        }
        return lo;
    }

    private int smallerThan(int[][] matrix, int mid, int[] smallLargePair) {
        int count = 0, n = matrix.length, row = n - 1, col = 0;
        while (row >= 0 && col < n) {
            int num = matrix[row][col];
            if (num > mid) {
                smallLargePair[1] = min(smallLargePair[1], num);
                row--;
            } else {
                smallLargePair[0] = max(smallLargePair[0], num);
                count += row + 1;
                col++;
            }
        }
        return count;
    }
```

### #5 Binary Search Shorter
```java
    // TC: O(n*log(max - min)), SC: O(1), max is max element in matrix, min is min element in matrix
    public int kthSmallest_BinarySearch_Better(int[][] matrix, int k) {
        int n = matrix.length, lo = matrix[0][0], hi = matrix[n - 1][n - 1] + 1;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            int count = 0, j = n - 1;
            for (int i = 0; i < n; i++) {
                while (j >= 0 && matrix[i][j] > mid) j--;
                count += j + 1;
            }
            if (count < k) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find K Pairs With Smallest Sums
## Intro
https://leetcode.com/problems/find-k-pairs-with-smallest-sums/

## Thoughts
- N/A

## Solutions
### #1 PQ BruteForce
```java
    // TC: O((mn + k) log(mn)), SC: O(mn), where m = nums1.length, n = nums2.length
    public List<List<Integer>> kSmallestPairs_PQ_BruteForce(int[] nums1, int[] nums2, int k) {
        Queue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> (a[0] + a[1])));

        for (int a : nums1) {
            for (int b : nums2) pq.offer(new int[]{a, b});
        }

        List<List<Integer>> res = new ArrayList<>();
        while (k-- > 0) {
            int[] elem = pq.poll();
            if (elem != null) res.add(new ArrayList<>(Arrays.asList(elem[0], elem[1])));
        }
        return res;
    }
```

### #2 PQ Better
```java
    // TC: O(klogk), SC: O(k)
    public List<List<Integer>> kSmallestPairs_PQ_Better(int[] nums1, int[] nums2, int k) {
        Queue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(arr -> (arr[0] + arr[1])));
        List<List<Integer>> res = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) return res;
        for (int i = 0; i < nums1.length && i < k; i++) pq.offer(new int[]{nums1[i], nums2[0], 0});
        while (k-- > 0 && !pq.isEmpty()) {
            int[] cur = pq.poll();
            res.add(new ArrayList<>(Arrays.asList(cur[0], cur[1])));
            if (cur[2] == nums2.length - 1) continue;

            pq.offer(new int[]{cur[0], nums2[cur[2] + 1], cur[2] + 1});
        }
        return res;
    }
```

### #3 PQ Best
```java
    // TC: O(klogk), SC: O(k)
    public List<List<Integer>> kSmallestPairs_PQ_Best_Short(int[] A, int[] B, int k) {
        Queue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> A[a[0]] + B[a[1]]));
        for (int i = 0; i < Math.min(k, A.length); i++) pq.offer(new int[]{i, 0});
        List<List<Integer>> res = new ArrayList<>();
        while (!pq.isEmpty() && k-- > 0) {
            int[] cur = pq.poll();
            res.add(new ArrayList<>(Arrays.asList(A[cur[0]], B[cur[1]])));
            if (++cur[1] < B.length) pq.offer(cur);
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---