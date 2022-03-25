
# Encode and Decode Strings
## Intro
https://leetcode.com/problems/encode-and-decode-strings/
![[Pasted image 20220321045215.png]]
![[Pasted image 20220321045226.png]]

## Thoughts
- list

## Solutions
### #1 Using Delimiter

```java
    private String emptyCharDelimiter = Character.toString((char) 257);
    private String delimiter = Character.toString((char) 256);

    // Encodes a list of strings to a single string.
    // TC: O(n), SC: O(1)
    public String encode_UsingDelimiter(List<String> arr) {
        if (arr.isEmpty()) return emptyCharDelimiter;

        StringBuilder sb = new StringBuilder();
        for (String s : arr) sb.append(s).append(delimiter);
        sb.deleteCharAt(sb.length() - 1);
        return sb.toString();
    }

    // Decodes a single string to a list of strings.
    // TC: O(n), SC: O(1)
    public List<String> decode_UsingDelimiter(String s) {
        // Split -1 will split delimiter into two empty strings, if -1 it means apply split operation as many times as possible
        return s.equals(emptyCharDelimiter) ? new ArrayList<>() : Arrays.asList(s.split(delimiter, -1));
    }
```

### #2 Using Chunk Encoding/Decoding
```java
    // Encodes a list of strings to a single string.
    // TC: O(n), SC: O(1)
    public String encode_UsingChunkDecoding(List<String> arr) {
        StringBuilder sb = new StringBuilder();
        for (String s : arr) sb.append(intToString(s)).append(s);
        return sb.toString();
    }

    // Decodes a single string to a list of strings.
    // TC: O(n), SC: O(1)
    public List<String> decode_UsingChunkEncoding(String s) {
        int i = 0, n = s.length();
        List<String> res = new ArrayList<>();
        while (i < n) {
            int length = stringToInt(s.substring(i, i + 4));
            i += 4;
            res.add(s.substring(i, i + length));
            i += length;
        }
        return res;
    }

    // encodes string length to bytes string
    private String intToString(String s) {
        int x = s.length();
        char[] bytes = new char[4];
        for (int i = 3; i >= 0; i--) bytes[3 - i] = (char) (x >> (i * 8) & 0xff);
        return new String(bytes);
    }

    // decodes bytes string to int
    private int stringToInt(String s) {
        int res = 0;
        for (char c : s.toCharArray()) res = (res << 8) + (int) c;
        return res;
    }
```

## Common Pitfalls to Avoid
- pitfall1

---


# Find Median From Data Stream
## Intro
https://leetcode.com/problems/find-median-from-data-stream/

## Thoughts
- list

## Solutions
### #1 Sorting

```java
public class P295_FindMedianFromDataStream_SortWay {

    List<Integer> list = new ArrayList<>();

    // TC: O(nlogn), SC: O(n)
    public void addNum(int num) {
        list.add(num);
        Collections.sort(list);
    }

    public double findMedian() {
        int n = list.size();
        return (n % 2 == 1) ? list.get(n / 2) : (list.get(n / 2) + list.get(n / 2 - 1)) / 2.0;
    }

}
```

### #2 TreeMap
```java
public class P295_FindMedianFromDataStream_TreeMap {
    private final TreeMap<Integer, Integer> tree = new TreeMap<>();
    private int n, midValue, midIndex;

    // TC: O(4logn), SC: O(n)
    public void addNum(int num) {
        tree.put(num, tree.getOrDefault(num, 0) + 1);
        n++;

        if (n == 1) {
            midValue = num;
            midIndex = 0;
        } else {
            if (num >= midValue) {
                midIndex++;
                if (midIndex / 2 >= tree.get(midValue)) {
                    midValue = tree.higherKey(midValue);
                    midIndex = 0;
                }
            } else {
                midIndex--;
                if (midIndex < 0) {
                    midValue = tree.lowerKey(midValue);
                    midIndex = tree.get(midValue) * 2 - 1;
                }
            }
        }
    }

    // TC: O(2logn), SC: O(1)
    public double findMedian() {
        if (n % 2 == 0 && (midIndex + 1) / 2 >= tree.get(midValue)) {
            int nextMidValue = tree.higherKey(midValue);
            return (midValue + nextMidValue) / 2d;
        }
        return midValue;
    }
}
```

### #3 Insertion Sort
```java
public class P295_FindMedianFromDataStream_InsertionSort {
    List<Integer> list = new ArrayList<>();

    // TC: O(n + logn), SC: O(n)
    // TC: logn for binarySearch while n for inserting(we may to shift all elements to right in worst case)
    public void addNum(int num) {
        if (list.isEmpty()) list.add(num);
        else {
            int index = Collections.binarySearch(list, num);
            if (index < 0) index = -(index + 1);
            list.add(index, num);
        }
    }

    public double findMedian() {
        int n = list.size();
        return (n & 1) == 1 ? list.get(n / 2) : (list.get(n / 2) + list.get(n / 2 - 1)) / 2.0;
    }
}
```

### #4 PQ Nice Code
```java
public class P295_FindMedianFromDataStream_PQ_Nice_Code {
    Queue<Integer> left = new PriorityQueue<>((a, b) -> b - a);
    Queue<Integer> right = new PriorityQueue<>(Comparator.comparingInt(a -> a));

    // TC: O(5logn), SC: O(n)
    public void addNum(int num) {
        left.add(num);

        right.add(left.poll());

        if (left.size() < right.size()) left.add(right.poll());
    }

    public double findMedian() {
        return left.size() > right.size() ? left.peek() : (left.peek() + right.peek()) / 2.0;
    }
}
```

### #5 PQ My Way
```java
// My solution is ugly but tiny bit better than leetcode solution as my solution does less logn operations.
public class P295_FindMedianFromDataStream_MyWay {
    Queue<Integer> lo = new PriorityQueue<>((a, b) -> b - a);
    Queue<Integer> hi = new PriorityQueue<>();
    double median = 0;

    // TC: O(4 logn), SC: O(n), where n is total number of elements(sum of both heaps)
    public void addNum(int num) {
        if (lo.size() + hi.size() == 0) {
            lo.add(num);
            median = num;
            return;
        }

        // add num in respective heap
        if (num <= lo.peek()) lo.add(num);
        else hi.add(num);

        // if difference between them becomes larger than 1, then rebalance
        if (abs(lo.size() - hi.size()) > 1) {
            // rebalance
            if (lo.size() > hi.size()) hi.add(lo.poll());
            else lo.add(hi.poll());
        }

        // Now set the median properly
        int leftVal = lo.peek(), rightVal = hi.peek();
        if ((lo.size() + hi.size()) % 2 == 0) median = (leftVal + rightVal) / 2.0;
        else {
            if (lo.size() > hi.size()) median = leftVal;
            else median = rightVal;
        }
    }

    public double findMedian() {
        return median;
    }
}
```

### #6 PQ My Way 2
```java
public class P295_FindMedianFromDataStream_MyWay2 {
    Queue<Integer> left = new PriorityQueue<>((a, b) -> b - a);
    Queue<Integer> right = new PriorityQueue<>();

    // TC: O(5logn), SC: O(n)
    public void addNum(int num) {
        if (!left.isEmpty() && num < left.peek()) left.offer(num);
        else right.offer(num);

        // rebalance and make sure that difference is always <=1 and left has more elements
        if (Math.abs(left.size() - right.size()) > 1) {
            if (left.size() > right.size()) right.offer(left.poll());
            else left.offer(right.poll());
        }
        if (left.size() < right.size() && !right.isEmpty()) left.offer(right.poll());
    }

    // TC: O(1), SC: O(1)
    public double findMedian() {
        int n = left.size() + right.size();
        return n % 2 == 1 ? left.peek() : (left.peek() + right.peek()) / 2.0;
    }
}
```

## Common Pitfalls to Avoid
- N/A

---