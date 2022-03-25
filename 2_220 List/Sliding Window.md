
# Maximum Sum Subarray of Size K
## Intro
![[Pasted image 20220322115237.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n*k), SC: O(1)
    public static int findMaxSumSubArray_BruteForce(int k, int[] arr) {
        int maxSum = 0, windowSum;
        for (int i = 0; i <= arr.length - k; i++) {
            windowSum = 0;
            for (int j = i; j < i + k; j++) windowSum += arr[j];
            maxSum = max(maxSum, windowSum);
        }
        return maxSum;
    }
```

### #2 SlidingWindow
```java
    // TC: O(n), SC: O(1)
    public static int findMaxSumSubArray_SlidingWindow_Mine(int k, int[] arr) {
        int max = Integer.MIN_VALUE, n = arr.length, sum = 0;
        for (int j = 0; j < n; j++) {
            sum += arr[j];
            if (j >= k - 1) {
                max = max(max, sum);
                sum -= arr[j - k + 1];
            }
        }
        return max;
    }

    // TC: O(n), SC: O(1)
    public static int findMaxSumSubArray_SlidingWindow_Grokking(int k, int[] arr) {
        int windowSum = 0, maxSum = 0;
        int windowStart = 0;
        for (int windowEnd = 0; windowEnd < arr.length; windowEnd++) {
            windowSum += arr[windowEnd]; // add the next element
            // slide the window, we don't need to slide if we've not hit the required window size of 'k'
            if (windowEnd >= k - 1) {
                maxSum = max(maxSum, windowSum);
                windowSum -= arr[windowStart]; // subtract the element going out
                windowStart++; // slide the window ahead
            }
        }
        return maxSum;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Smallest Subarray With a Greater Sum
## Intro
![[Pasted image 20220322115340.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n^2), SC: O(1)
    public static int findMinSubArray_BruteForce(int S, int[] arr) {
        int res = Integer.MAX_VALUE;
        for (int i = 0; i < arr.length; i++) {
            int sum = 0;
            for (int j = i; j < arr.length; j++) {
                sum += arr[j];
                if (sum >= S) {
                    res = min(res, j - i + 1);
                    break;
                }
            }
        }
        return res == Integer.MAX_VALUE ? 0 : res;
    }
```

### #2 Sliding Window
```java
    public static int findMinSubArray_SlidingWindow_Grokking(int targetSum, int[] arr) {
        int windowSum = 0, minLength = Integer.MAX_VALUE;
        int windowStart = 0;
        for (int windowEnd = 0; windowEnd < arr.length; windowEnd++) {
            windowSum += arr[windowEnd]; // add the next element
            // shrink the window as small as possible until the 'windowSum' is smaller than 'targetSum'
            while (windowSum >= targetSum) {
                minLength = min(minLength, windowEnd - windowStart + 1);
                windowSum -= arr[windowStart]; // subtract the element going out
                windowStart++; // slide the window ahead
            }
        }

        return minLength == Integer.MAX_VALUE ? 0 : minLength;
    }

    // TC: O(n), SC: O(1)
    public static int findMinSubArray_SlidingWindow_Mine(int targetSum, int[] arr) {
        int res = Integer.MAX_VALUE, i = 0, j = 0, sum = 0;
        while (j < arr.length) {
            sum += arr[j];
            while (sum >= targetSum) {
                res = min(res, j - i + 1);
                sum -= arr[i++];
            }
            j++;
        }
        return res == Integer.MAX_VALUE ? 0 : res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Longest Substring With At Most K Distinct Characters
## Intro
https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/
![[Pasted image 20220322115924.png]]

## Thoughts
- N/A

## Solutions
### #1 Sliding Window
```java
    // TC: O(n), SC: O(k)
    public int lengthOfLongestSubstringKDistinct_WhileLoop(String s, int k) {
        Map<Character, Integer> map = new HashMap<>();
        int max = Integer.MIN_VALUE, i = 0, j = 0;
        while (j < s.length()) {
            char c = s.charAt(j++);
            map.put(c, map.getOrDefault(c, 0) + 1);

            // the below can also be changed to if and it still works
            while (map.size() > k) {
                c = s.charAt(i++);
                map.put(c, map.get(c) - 1);
                if (map.get(c) == 0) map.remove(c);
            }
            max = Math.max(max, j - i);
        }
        return max;
    }

    // // TC: O(n), SC: O(k)
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        int max = 0, i = 0;
        Map<Character, Integer> map = new HashMap<>();
        for (int j = 0; j < s.length(); j++) {
            char c = s.charAt(j);
            map.put(c, map.getOrDefault(c, 0) + 1);
            while (map.size() > k) {
                c = s.charAt(i);
                map.put(c, map.getOrDefault(c, 0) - 1);
                if (map.get(c) == 0) map.remove(c);
                i++;
            }
            max = Math.max(max, j - i + 1);
        }
        return max;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Fruit Into Baskets
## Intro
https://leetcode.com/problems/fruit-into-baskets/
![[Pasted image 20220322120003.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n^3), SC: O(n)
    public int totalFruit_BruteForce(int[] tree) {
        int max = 0;
        for (int start = 0; start < tree.length; start++) {
            for (int end = start; end < tree.length; end++) {
                if (doesSubArrayHaveOnlyTwoElems(tree, start, end)) max = max(max, end - start + 1);
            }
        }
        return max;
    }

    private boolean doesSubArrayHaveOnlyTwoElems(int[] tree, int start, int end) {
        Set<Integer> set = new HashSet<>();
        for (int i = start; i <= end; i++) {
            if (set.size() > 2) return false;
            set.add(tree[i]);
        }
        return set.size() <= 2;
    }
```

### #2 Sliding Window
```java
    // TC: O(n), SC: O(2)
    // TODO: Understand this, very intuitive. Read the comment by aramik and xingHong.
    // Whenever [i,j] becomes the longest window, it will NEVER DECREASE.
    // However if [i,j] is still not the longest, window only j will increase, not i.
    // Therefore at the end j - i will always contain the answer. Very Clever indeed.
    public int totalFruit_SlidingWindow_Crazy(int[] tree) {
        int max = 0, i = 0;
        Map<Integer, Integer> map = new HashMap<>();
        for (int j = 0; j < tree.length; j++) {
            int num = tree[j];
            map.put(num, map.getOrDefault(num, 0) + 1);
            while (map.size() > 2) {
                map.put(tree[i], map.getOrDefault(tree[i], 0) - 1);
                if (map.get(tree[i]) <= 0) map.remove(tree[i]);
                i++;
            }
            max = max(max, j - i + 1);
        }
        return max;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Max Consecutive Ones III
## Intro
https://leetcode.com/problems/max-consecutive-ones-iii/
![[Pasted image 20220322120057.png]]

## Thoughts
- N/A

## Solutions
### #1 Sliding Window
```java
    // TC: O(n), SC: O(1)
    public int longestOnes_NoArray(int[] nums, int k) {
        int max = Integer.MIN_VALUE, i = 0, oneCount = 0;
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] == 1) oneCount++;

            if (j - i + 1 - oneCount > k && nums[i++] == 1) oneCount--;

            max = Math.max(max, j - i + 1);
        }
        return max;
    }

    // TC: O(n), SC: O(1)
    public int longestOnes(int[] nums, int k) {
        int max = Integer.MIN_VALUE, i = 0, arr[] = new int[2];
        for (int j = 0; j < nums.length; j++) {
            arr[nums[j]]++;

            // even while works. todo why?
            if (j - i + 1 - arr[1] > k) arr[nums[i++]]--;

            max = Math.max(max, j - i + 1);
        }
        return max;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Permutation In String
## Intro
https://leetcode.com/problems/permutation-in-string/
![[Pasted image 20220322120333.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n^2), SC: O(n + k)
    public boolean checkInclusion_BetterBruteForce(String t, String s) {
        Map<Character, Integer> tMap = new HashMap<>();
        for (char c : t.toCharArray()) tMap.put(c, tMap.getOrDefault(c, 0) + 1);

        int k = t.length();
        for (int i = 0; i <= s.length() - k; i++) {
            Map<Character, Integer> sMap = new HashMap<>();
            for (int j = i; j < i + k; j++) {
                char c = s.charAt(j);
                sMap.put(c, sMap.getOrDefault(c, 0) + 1);
            }
            if (valid(sMap, tMap)) return true;
        }
        return false;
    }

    private boolean valid(Map<Character, Integer> sMap, Map<Character, Integer> tMap) {
        if (sMap.size() != tMap.size()) return false;
        for (char c : tMap.keySet()) if (!sMap.containsKey(c) || !sMap.get(c).equals(tMap.get(c))) return false;
        return true;
    }

    // TC: O(n*k^2), SC: O(k), n = s.length(), k = pattern.length();
    // possible brute force
    public boolean checkInclusion_BruteForce_TLE(String pattern, String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : pattern.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);
        for (int i = 0; i <= s.length() - pattern.length(); i++) {
            for (int j = i; j < i + pattern.length(); j++) {
                if (exists(s, j, i + pattern.length(), new HashMap<>(map), pattern.length())) return true;
            }
        }
        return false;
    }

    private boolean exists(String s, int start, int end, Map<Character, Integer> map, int k) {
        int count = 0;
        for (int i = start; i < end; i++) {
            char c = s.charAt(i);
            if (map.containsKey(c)) {
                map.put(c, map.get(c) - 1);
                if (map.get(c) == 0) count++;
                if (count == map.size()) return true;
            }
        }
        return false;
    }
```

### #2 Sliding Window
```java
    // TC: O(l1 + l2 * 26)), SC: O(26 + 26) or O(1)
    public boolean checkInclusion_SlidingWindow_FromBruteForce(String t, String s) {
        Map<Character, Integer> tMap = new HashMap<>();
        for (char c : t.toCharArray()) tMap.put(c, tMap.getOrDefault(c, 0) + 1);

        int k = t.length(), i = 0;
        Map<Character, Integer> sMap = new HashMap<>();
        for (int j = 0; j < s.length(); j++) {
            char c = s.charAt(j);
            if (j < k) sMap.put(c, sMap.getOrDefault(c, 0) + 1);
            else {
                if (valid(sMap, tMap)) return true;
                sMap.put(c, sMap.getOrDefault(c, 0) + 1);

                c = s.charAt(i++);
                sMap.put(c, sMap.get(c) - 1);
                if (sMap.get(c) == 0) sMap.remove(c);
            }
        }
        return valid(sMap, tMap);
    }
```

### #3 Using HashMap
```java
    // TC: O(l1 + 26*l1*(l2-l1)), SC: O(1)
    public boolean checkInclusion_UsingHashMap(String s1, String s2) {
        if (s1.length() > s2.length()) return false;

        Map<Character, Integer> s1Map = new HashMap<>();
        for (char c : s1.toCharArray()) s1Map.put(c, s1Map.getOrDefault(c, 0) + 1);

        for (int i = 0; i <= s2.length() - s1.length(); i++) {
            Map<Character, Integer> s2Map = new HashMap<>();
            for (int j = 0; j < s1.length(); j++) {
                char c = s2.charAt(i + j);
                s2Map.put(c, s2Map.getOrDefault(c, 0) + 1);
            }
            if (matches(s1Map, s2Map)) return true;
        }
        return false;
    }

    private boolean matches(Map<Character, Integer> map1, Map<Character, Integer> map2) {
        for (char key : map1.keySet()) if (!map1.get(key).equals(map2.getOrDefault(key, -1))) return false;
        return true;
    }
```

### #4 Using Array
```java
    // TC: O(l1 + 26*l1*(l2-l1)), SC: O(1)
    public boolean checkInclusion_UsingArray(String s1, String s2) {
        if (s1.length() > s2.length()) return false;

        int[] s1Map = new int[26];
        for (char c : s1.toCharArray()) s1Map[c - 'a']++;

        for (int i = 0; i <= s2.length() - s1.length(); i++) {
            int[] s2Map = new int[26];
            for (int j = 0; j < s1.length(); j++) {
                char c = s2.charAt(i + j);
                s2Map[c - 'a']++;
            }
            if (matches(s1Map, s2Map)) return true;
        }
        return false;
    }

    private boolean matches(int[] map1, int[] map2) {
        for (int i = 0; i < map1.length; i++) if (map1[i] != map2[i]) return false;
        return true;
    }
```

### #5 Optimized
```java
    // TC: O(l1 + l2 * 26), SC: O(1)
    public boolean checkInclusion_My_Optimized(String t, String s) {
        int tMap[] = new int[26], k = t.length(), i = 0, sMap[] = new int[26];
        for (char c : t.toCharArray()) tMap[c - 'a']++;

        for (int j = 0; j < s.length(); j++) {
            char c = s.charAt(j);
            if (j < k) sMap[c - 'a']++;
            else {
                if (matches(sMap, tMap)) return true;
                sMap[c - 'a']++;

                c = s.charAt(i++);
                sMap[c - 'a']--;
            }
        }
        return matches(sMap, tMap);
    }

    // TC: O(l1 + 26*(l2 - l1)), SC: O(1)
    public boolean checkInclusion_UsingArray_Optimized(String s1, String s2) {
        if (s1.length() > s2.length()) return false;

        int[] s1Map = new int[26], s2Map = new int[26];
        for (int i = 0; i < s1.length(); i++) {
            s1Map[s1.charAt(i) - 'a']++;
            s2Map[s2.charAt(i) - 'a']++;
        }

        for (int i = 0; i < s2.length() - s1.length(); i++) {
            if (matches(s1Map, s2Map)) return true;
            s2Map[s2.charAt(i + s1.length()) - 'a']++;
            s2Map[s2.charAt(i) - 'a']--;
        }
        return matches(s1Map, s2Map);
    }
```

### #6 While Loop
```java
    // TC: O(l1 + 26 * (l2 - l1)), SC: O(1), SC is O(1) coz string contains only lowercase alpha according to question
    public boolean checkInclusion_SlidingWindow_WhileLoop(String s1, String s2) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s1.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);
        int count = map.size(), i = 0, j = 0;
        while (j < s2.length()) {
            char c = s2.charAt(j);
            if (map.containsKey(c)) {
                map.put(c, map.get(c) - 1);
                if (map.get(c) == 0) count--;
            }
            while (count == 0) {
                if (j - i + 1 == s1.length()) return true;
                c = s2.charAt(i++);
                if (map.containsKey(c)) {
                    map.put(c, map.get(c) + 1);
                    if (map.get(c) > 0) count++;
                }
            }
            j++;
        }
        return false;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Find All Anagrams In A String
## Intro
https://leetcode.com/problems/find-all-anagrams-in-a-string/
![[Pasted image 20220322120718.png]]

## Thoughts
- N/A

## Solutions
### #1 Sliding Window
```java
    // TC: O(s + p), SC: O(1), SC is constant coz of lower case letters
    public List<Integer> findAnagrams_LC_Solution(String s, String p) {
        int ns = s.length(), np = p.length();
        if (ns < np) return new ArrayList<>();

        int[] pMap = new int[26], sMap = new int[26];
        for (char c : p.toCharArray()) pMap[c - 'a']++;
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < ns; i++) {
            char c = s.charAt(i);
            sMap[c - 'a']++;
            if (i >= np) sMap[s.charAt(i - np) - 'a']--;
            // nice trick to use Arrays.equal
            if (Arrays.equals(sMap, pMap)) res.add(i - np + 1);
        }
        return res;
    }

    // TC: O(s + p), SC: O(1), SC is constant coz of lower case letters
    public List<Integer> findAnagrams_SlidingWindow_Mine(String s, String p) {
        List<Integer> res = new ArrayList<>();
        Map<Character, Integer> map = new HashMap<>();
        for (char c : p.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);
        int i = 0, count = map.size();
        for (int j = 0; j < s.length(); j++) {
            char c = s.charAt(j);
            if (map.containsKey(c)) {
                map.put(c, map.get(c) - 1);
                if (map.get(c) == 0) count--;
            }

            while (count == 0) {
                if (j - i + 1 == p.length()) res.add(i);
                c = s.charAt(i++);
                if (map.containsKey(c)) {
                    map.put(c, map.get(c) + 1);
                    if (map.get(c) > 0) count++;
                }
            }
        }
        return res;
    }
```

### #2 Another Solution
```java
    // TC: O(s + p), SC: O(1)
    public List<Integer> findAnagrams_Mine_Using_PermutationInStringCode(String s, String p) {
        int tMap[] = new int[26], k = p.length(), i = 0, sMap[] = new int[26];
        List<Integer> res = new ArrayList<>();
        for (char c : p.toCharArray()) tMap[c - 'a']++;

        for (int j = 0; j < s.length(); j++) {
            char c = s.charAt(j);
            if (j < k) sMap[c - 'a']++;
            else {
                if (Arrays.equals(sMap, tMap)) res.add(i);
                sMap[c - 'a']++;

                c = s.charAt(i++);
                sMap[c - 'a']--;
            }
        }
        if (Arrays.equals(sMap, tMap)) res.add(i);
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# SubString with Concatenation of All Words
## Intro
https://leetcode.com/problems/substring-with-concatenation-of-all-words/
![[Pasted image 20220322120806.png]]

## Thoughts
- N/A

## Solutions
### #1 Sliding Window
```java
    // TC: O(n*m*len), SC: O(n+m), n = s.length(), m = words.length, len = words[0].length
    public List<Integer> findSubstring_Mine(String s, String[] words) {
        List<Integer> res = new ArrayList<>();
        Map<String, Integer> freqMap = new HashMap<>();
        for (String word : words) freqMap.put(word, freqMap.getOrDefault(word, 0) + 1);
        int wordsCount = words.length, wordLength = words[0].length();

        for (int i = 0; i <= s.length() - wordsCount * wordLength; i++) {
            Map<String, Integer> wordsSeen = new HashMap<>();
            for (int j = 0; j < wordsCount; j++) {
                int nextWordIndex = i + j * wordLength;
                String word = s.substring(nextWordIndex, nextWordIndex + wordLength);
                if (!freqMap.containsKey(word)) break;

                wordsSeen.put(word, wordsSeen.getOrDefault(word, 0) + 1);

                if (wordsSeen.get(word) > freqMap.getOrDefault(word, 0)) break;

                if (j + 1 == wordsCount) res.add(i);
            }
        }
        return res;
```

## Common Pitfalls to Avoid
- N/A

---

# Minimum Size Subarray Sum
## Intro
https://leetcode.com/problems/minimum-size-subarray-sum/
![[Pasted image 20220322121008.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n^3), SC: O(1)
    public int minSubArrayLen_BruteForce(int target, int[] nums) {
        int size = Integer.MAX_VALUE;
        for (int start = 0; start < nums.length; start++) {
            for (int end = start; end < nums.length; end++) {
                int sum = 0;
                for (int i = start; i <= end; i++) sum += nums[i];
                if (sum >= target) size = Math.min(size, end - start + 1);
            }
        }
        return size == Integer.MAX_VALUE ? 0 : size;
    }
```

### #2 Better BruteForce
```java
    // TC: O(n^2), SC: O(n)
    public int minSubArrayLen_BetterBruteForce(int target, int[] nums) {
        if (nums.length == 0) return 0;
        int size = Integer.MAX_VALUE, sums[] = new int[nums.length];
        sums[0] = nums[0];
        for (int i = 1; i < nums.length; i++) sums[i] = sums[i - 1] + nums[i];
        for (int start = 0; start < nums.length; start++) {
            for (int end = start; end < nums.length; end++) {
                int sum = sums[end] - sums[start] + nums[start];
                if (sum >= target) {
                    size = Math.min(size, end - start + 1);
                    break;
                }
            }
        }
        return size != Integer.MAX_VALUE ? size : 0;
    }
```

### #3 Two Pointers
```java
    // TC: O(2n), SC: O(1)
    public int minSubArrayLen_TwoPointers(int target, int[] nums) {
        int size = Integer.MAX_VALUE, i = 0, j = 0, sum = 0;
        while (j < nums.length) {
            sum += nums[j];
            while (sum >= target) {
                size = Math.min(size, j - i + 1);
                sum -= nums[i++];
            }
            j++;
        }
        return size == Integer.MAX_VALUE ? 0 : size;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Sliding Window Maximum
## Intro
https://leetcode.com/problems/sliding-window-maximum/

![[Pasted image 20220322121105.png]]
## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(n^2), SC: O(n)
    public int[] maxSlidingWindow_BruteForce(int[] nums, int k) {
        int[] res = new int[nums.length - k + 1];
        for (int i = 0; i <= nums.length - k; i++) {
            int max = nums[i];
            for (int j = i; j < i + k; j++) max = max(max, nums[j]);
            res[i] = max;
        }
        return res;
    }
```

### #2 SlidingWindow
```java
    // TC: O(?), SC: O(?)
    public int[] maxSlidingWindowHeapApproach(int[] nums, int k) {
        int n = nums.length, l = 0, result[] = new int[n - k + 1];
        // a[0] = index, a[1] = element
        Queue<int[]> pq = new PriorityQueue<>((a, b) -> b[1] - a[1]);
        int maxIndex = 0;
        for (int i = 0; i < n; i++) {
            pq.offer(new int[]{i, nums[i]});
            if (pq.size() >= k) {
                while (nums[i] < pq.peek()[1] && maxIndex <= i - k) {
                    pq.poll();
                    maxIndex = pq.peek()[0];
                }
                maxIndex = pq.peek()[0];
                result[l++] = pq.peek()[1];
            }
        }
        return result;
    }
```

### #3 PQ Simpler
```java
    // TC: O(?), SC: O(?)
    public int[] maxSlidingWindow_PQ_Simpler(int[] A, int k) {
        int n = A.length, res[] = new int[n - k + 1], maxIndex = 0, l = 0;
        Queue<Integer> pq = new PriorityQueue<>((i, j) -> A[j] - A[i]);
        for (int i = 0; i < n; i++) {
            pq.offer(i);
            if (pq.size() >= k) {
                while (A[i] < A[pq.peek()] && maxIndex <= i - k) {
                    pq.poll();
                    maxIndex = pq.peek();
                }
                maxIndex = pq.peek();
                res[l++] = A[pq.peek()];
            }
        }
        return res;
    }
```

### #4 TreeMap
```java
    // TC: O(nlogk), SC: O(n - k + 1)
    public int[] maxSlidingWindowTreeMapApproach(int[] nums, int k) {
        TreeMap<Integer, Integer> map = new TreeMap<>();
        int res[] = new int[nums.length - k + 1], j = 0;
        for (int i = 0; i < nums.length; i++) {
            map.put(nums[i], 1 + map.getOrDefault(nums[i], 0));

            if (i >= k - 1) {
                res[j++] = map.lastKey();

                int numAtWindowStart = nums[i - k + 1];
                map.put(numAtWindowStart, map.get(numAtWindowStart) - 1);

                if (map.get(numAtWindowStart) == 0) map.remove(numAtWindowStart);
            }
        }
        return res;
    }
```

### #5 Deque
```java
    // TC: O(n), SC: O(n - k + 1)
    public int[] maxSlidingWindow_Dequeue(int[] A, int k) {
        int n = A.length, maxIndex = 0, res[] = new int[n - k + 1];
        Deque<Integer> deq = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            cleanDeque(deq, A, i, k);
            deq.addLast(i);

            if (i >= k) res[i - k + 1] = A[deq.getFirst()];
            else if (A[i] > A[maxIndex]) maxIndex = i;
        }
        res[0] = A[maxIndex];
        return res;
    }

    private void cleanDeque(Deque<Integer> deq, int[] nums, int i, int k) {
        if (!deq.isEmpty() && deq.getFirst() == i - k) deq.removeFirst();

        while (!deq.isEmpty() && nums[i] > nums[deq.getLast()]) deq.removeLast();
    }
```

### #6 DP
```java
    // TC: O(n), SC: O(n)
    public int[] maxSlidingWindow_DP(int[] nums, int k) {
        int n = nums.length;
        if (n * k == 0) return new int[0];
        if (k == 1) return nums;

        int left[] = new int[n], right[] = new int[n];
        left[0] = nums[0];
        right[n - 1] = nums[n - 1];
        for (int i = 1; i < n; i++) {
            if (i % k == 0) left[i] = nums[i];
            else left[i] = Math.max(left[i - 1], nums[i]);

            int j = n - i - 1;
            if ((j + 1) % k == 0) right[j] = nums[j];
            else right[j] = Math.max(right[j + 1], nums[j]);
        }

        int[] res = new int[n - k + 1];
        for (int i = 0; i < res.length; i++) res[i] = Math.max(left[i + k - 1], right[i]);
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---