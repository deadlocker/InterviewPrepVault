# Sliding Window

# [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

## Intro

![Untitled](1_Blind75/Sliding%20Wi%2064d86/Untitled.png)

## Thoughts

- BruteForce is pretty easy.
- The next thought has to be sliding window. You can use HashSet and keep shrinking the window as long as its not valid(that is the window has duplicates) and on the contrary, keep expanding the window if the window is currently valid(that is window has no duplicates).
- The next optimization for this is that instead of using inner while you can use if condition.
- Then the better optimization is to use Map to store last seen index and advance the i(shrinking pointer) directly to last seen occurrence + 1. (be careful here, there is one edge case, check pitfalls)

## Solutions

### #1 BruteForce

```java
// TC: O(n^3), SC: O(min(n, k)) where k can be 26 for english alphabet
public int lengthOfLongestSubstring_BruteForce(String s) {
    int max = 0;
    for (int start = 0; start < s.length(); start++) {
        for (int end = start + 1; end <= s.length(); end++) {
            if (allUnique(s, start, end)) {
                max = Math.max(max, end - start);
            }
        }
    }
    return max;
}

private boolean allUnique(String s, int start, int end) {
    Set<Character> set = new HashSet<>();
    for (int i = start; i < end; i++) {
        char c = s.charAt(i);
        if (set.contains(c)) return false;
        set.add(c);
    }
    return true;
}
```

### #2 Sliding Window Two Loops

```java
// TC: O(2n), SC: O(min(n,k))
public int lengthOfLongestSubstring_SlidingWindow_AnotherWay(String s) {
    int max = 0, i = 0, j = 0;
    Set<Character> set = new HashSet<>();
    while (j < s.length()) {
        while (set.contains(s.charAt(j))) {
            set.remove(s.charAt(i++));
        }
        set.add(s.charAt(j));
        max = Math.max(max, j - i + 1);
        j++;
    }
    return max;
}
```

### #3 Sliding Window Single Loop

```java
// TC: O(2n), SC: O(min(n, k))
public int lengthOfLongestSubstring_SlidingWindow(String s) {
    int max = 0, i = 0, j = 0;
    Set<Character> set = new HashSet<>();
    while (i < s.length() && j < s.length()) {
        if (set.contains(s.charAt(j))) {
            set.remove(s.charAt(i++));
        } else {
            set.add(s.charAt(j++));
            max = Math.max(max, j - i);
        }
    }
    return max;
}
```

### #4 Sliding Window Optimized(direct jump to 1 + lastOccurrence)

```java
// TC: O(n), SC: O(min(n, k))
public int lengthOfLongestSubstring_SlidingWindow_Optimized(String s) {
    Map<Character, Integer> map = new HashMap<>();
    int max = 0, i = 0;
    for (int j = 0; j < s.length(); j++) {
        char c = s.charAt(j);
        if (map.containsKey(c)) {
            // Very important to use Math.max, we dont want to cross i pointer
            i = Math.max(i, map.get(c) + 1);
        }
        map.put(c, j);
        max = Math.max(max, j - i + 1);
    }
    return max;
}
```

## Common Pitfalls to Avoid

- In #4 if you don’t use Math.max(i, map.get(c) + 1), then it will fail for input: abba. `Expected: 2, but returned: 3`
- consider a string like this xy...x.....i....
- In this case i pointer has already moved forward(probably because of y). However x is also a duplicate which appears before y. Meaning x’s sibling duplicate occurs much later than y siblings. Therefore if you just do map.get(x) + 1, then you will move i pointer backwards. This is like moving the window backwards even though we have moved away from that point.

---

# Longest Repeating Character Replacement
## Intro
https://leetcode.com/problems/longest-repeating-character-replacement/
![[Pasted image 20220321044259.png]]

## Thoughts
- list

## Solutions
### #1 Sliding Window For Loop

```java
    // TC: O(n), SC: O(1)
    public int characterReplacement_SlidingWindow_ForLoop(String s, int k) {
        int[] count = new int[26];
        int start = 0, charWithMaxLength = 0, maxLength = 0;
        for (int end = 0; end < s.length(); end++) {
            charWithMaxLength = Math.max(charWithMaxLength, ++count[s.charAt(end) - 'A']);
            // end-start+1 is windowSize
            if (end - start + 1 - charWithMaxLength > k) {
                count[s.charAt(start) - 'A']--;
                start++;
            }
            maxLength = Math.max(maxLength, end - start + 1);
        }
        return maxLength;
    }
```

### #2 Sliding Window While Loop
```java
    // TC: O(n), SC: O(1)
    public int characterReplacement_SlidingWindow_WhileLoop(String s, int k) {
        int[] count = new int[26];
        int i = 0, j = 0, charWithMaxLength = 0, maxLength = 0;
        while (j < s.length()) {
            charWithMaxLength = Math.max(charWithMaxLength, ++count[s.charAt(j) - 'A']);
            while (j - i + 1 - charWithMaxLength > k) {
                count[s.charAt(i) - 'A']--;
                i++;
            }
            maxLength = Math.max(maxLength, j - i + 1);
            j++;
        }
        return maxLength;
    }
```

## Common Pitfalls to Avoid
- pitfall1

---

# Minimum Window Substring
## Intro
https://leetcode.com/problems/minimum-window-substring/
![[Pasted image 20220321050802.png]]
## Thoughts
- list

## Solutions
### #1 BruteForce

```java
    // TC: O(s^3), SC: O(t) t is t.length() and s is s.length()
    public String minWindow_BruteForce(String s, String t) {
        int[] smallest = {0, s.length()};
        boolean entered = false;
        for (int start = 0; start < s.length(); start++) {
            for (int end = start + 1; end <= s.length(); end++) {
                if (isPresent(s, start, end, t)) {
                    // entered flag is needed to avoid issues with inputs as shown below
                    // s = "a", t = "a"
                    // s = "a", t = "aa"
                    entered = true;
                    if (end - start < smallest[1] - smallest[0]) {
                        smallest[0] = start;
                        smallest[1] = end;
                    }
                }
            }
        }
        return entered ? s.substring(smallest[0], smallest[1]) : "";
    }

    // TC: O(s), SC: O(t)
    private boolean isPresent(String s, int start, int end, String t) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : t.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);

        for (int k = start; k < end; k++) {
            char c = s.charAt(k);
            if (map.isEmpty()) return true;

            if (map.containsKey(c)) {
                if (map.get(c) == 1) map.remove(c);
                else map.put(c, map.get(c) - 1);
            }
        }

        return map.isEmpty();
    }
```

### #2 LC Way
```java
    // TC: O(s + t), SC: O(s + t)
    public String minWindow_SlidingWindow(String s, String t) {
        if (s.length() == 0 || t.length() == 0) return "";

        Map<Character, Integer> freqMap = new HashMap<>();

        for (char c : t.toCharArray()) freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);

        int i = 0, j = 0;

        // formed is used to keep track of how many unique chars in t are present in the
        // current window in its desired frequency
        int formed = 0;

        // ans list of the form (window length, left, right)
        int[] ans = {-1, 0, 0};

        // map which keeps a count of all unique chars in the current window
        Map<Character, Integer> windowCounts = new HashMap<>();

        while (j < s.length()) {
            char c = s.charAt(j);
            windowCounts.put(c, windowCounts.getOrDefault(c, 0) + 1);

            // if freq of current char added equals to the desired count in t
            // then increment the formed count by 1
            if (freqMap.containsKey(c) && windowCounts.get(c).intValue() == freqMap.get(c).intValue()) formed++;

            // try and contract the window till the point where it ceases to be desirable
            while (i <= j && formed == freqMap.size()) {
                c = s.charAt(i);
                // save smallest window till now
                if (ans[0] == -1 || j - i + 1 < ans[0]) {
                    ans[0] = j - i + 1;
                    ans[1] = i;
                    ans[2] = j;
                }

                // the character at the position pointed by the left pointer
                // is no longer a part of the window
                windowCounts.put(c, windowCounts.get(c) - 1);
                if (freqMap.containsKey(c) && windowCounts.get(c).intValue() < freqMap.get(c).intValue()) formed--;

                // move the left pointer ahead, this would help to look for a new window
                i++;
            }
            // keep expanding the window once we are done contracting
            j++;
        }

        return ans[0] == -1 ? "" : s.substring(ans[1], ans[2] + 1);
    }
```

### #3 Shorter Code
```java
    // TC: O(s + t), SC: O(t)
    public String minWindow_Better_Shorter(String s, String t) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : t.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);

        int i = 0, j = 0, minStart = 0, minLen = Integer.MAX_VALUE, remaining = t.length();
        while (j < s.length()) {
            char c = s.charAt(j++);
            if (map.containsKey(c)) {
                map.put(c, map.get(c) - 1);
                if (map.get(c) >= 0) remaining--;
            }
            while (remaining == 0) {
                if (j - i < minLen) {
                    minLen = j - i;
                    minStart = i;
                }
                c = s.charAt(i++);
                if (map.containsKey(c)) {
                    map.put(c, map.get(c) + 1);
                    if (map.get(c) > 0) remaining++;
                }
            }
        }
        return minLen == Integer.MAX_VALUE ? "" : s.substring(minStart, minStart + minLen);
    }
```

### #4 My Way
```java
    // TC: O(s + t), SC: O(t)
    public String minWindow_SlightVariation(String s, String t) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : t.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);
        // note remaining is mapped to map.size here while in below its t.length(), why?
        // This is because here we if(map.get(c) == 0) remaining-- whereas in below method, we do
        // if(map.get(c) >= 0) remaining--;
        // note the difference between equals and >= symbol
        int start = 0, minLength = Integer.MAX_VALUE, i = 0, remaining = map.size();
        for (int j = 0; j < s.length(); j++) {
            char c = s.charAt(j);
            if (map.containsKey(c)) {
                map.put(c, map.get(c) - 1);
                if (map.get(c) == 0) remaining--;
            }
            while (remaining == 0) {
                if (j - i + 1 < minLength) {
                    minLength = j - i + 1;
                    start = i;
                }
                c = s.charAt(i++);
                if (map.containsKey(c)) {
                    map.put(c, map.get(c) + 1);
                    if (map.get(c) > 0) remaining++;
                }
            }
        }
        return minLength == Integer.MAX_VALUE ? "" : s.substring(start, start + minLength);
    }

    // TC: O(s + t), SC: O(s + t)
    public String minWindow_SlidingWindow_MyWay_Another(String s, String t) {
        Map<Character, Integer> tMap = new HashMap<>();
        for (char c : t.toCharArray()) tMap.put(c, tMap.getOrDefault(c, 0) + 1);
        // start, end, length
        int res[] = {0, 0, Integer.MAX_VALUE}, j = 0, formed = 0;
        for (int i = 0; i < s.length(); i++) {
            while (j < s.length() && formed != tMap.size()) {
                char c = s.charAt(j++);
                if (tMap.containsKey(c)) {
                    tMap.put(c, tMap.get(c) - 1);
                    if (tMap.get(c) == 0) formed++;
                }
            }
            int size = j - i;
            if (formed == tMap.size() && size < res[2]) {
                res[0] = i;
                res[1] = j;
                res[2] = size;
            }
            char c = s.charAt(i);
            if (tMap.containsKey(c)) {
                tMap.put(c, tMap.get(c) + 1);
                if (tMap.get(c) == 1) formed--;
            }
        }
        return s.substring(res[0], res[1]);
    }
```

## Common Pitfalls to Avoid
- N/A

---