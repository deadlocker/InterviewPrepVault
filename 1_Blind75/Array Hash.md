# Array/Hash

# [Two Sum](https://leetcode.com/problems/two-sum/)

## Intro

![Untitled](1_Blind75/Array%20Hash%2037970/Untitled.png)

## Thoughts

- Bruteforce is pretty straightforward, however be careful with bruteforce test cases where if you have j = 0 in the inner loop, then it will fail for `A = [5], T = 10`. That is it will count 5 two times even though we have a single 5 in the array.
- Apart from that, sorting approach(aka two pointers approach) is very useful and is in fact what will be used for 3Sum-esque problems.
- Finally hashmap is pretty straightforward. Essentially we are avoiding the inner for loop in bruteForce by using a hashmap.

## Solutions

### BruteForce

```java
// TC: O(n^2), SC: O(1)
  public int[] twoSum_BruteForce(int[] nums, int target) {
      for (int i = 0; i < nums.length - 1; i++) {
          for (int j = i + 1; j < nums.length; j++) {
              if (nums[i] + nums[j] == target) return new int[]{i, j};
          }
      }
      return new int[0];
  }
```

### Sorting

```java
// TC: O(nlogn), SC: O(n)
public int[] twoSum_Sort_Simple(int[] A, int T) {
  List<Pair<Integer, Integer>> list = new ArrayList<>();
  for (int i = 0; i < A.length; i++) list.add(new Pair<>(i, A[i]));
  list.sort(Comparator.comparingInt(Pair::getValue));
  int i = 0, j = A.length - 1;
  while (i < j) {
      int sum = list.get(i).getValue() + list.get(j).getValue();
      if (sum == T) return new int[]{list.get(i).getKey(), list.get(j).getKey()};
      if (sum < T) i++;
      else j--;
  }
  return new int[0];
}
```

### HashMap

```java
// TC: O(n), SC: O(n)
public int[] twoSum_HashMap(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int diff = target - nums[i];
        if (map.containsKey(diff)) return new int[]{map.get(diff), i};
        else map.put(nums[i], i);
    }
    return new int[2];
}
```

## Common Pitfalls to Avoid

- In **bruteforce**, make sure `j = i + 1` in inner loop. If `j = 0`, then make sure to check for `i ≠ j`
- Don’t forget to do `Arrays.sort` for **sorting** approach

# [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)

## Intro

![Untitled](1_Blind75/Array%20Hash%2037970/Untitled%201.png)

## Thoughts
- Pretty straightforward hash set solution

## Solutions

### #1 BruteForce

```java
// TC: O(n ^ 2), SC: O(1)
public boolean containsDuplicate_BruteForce(int[] nums) {
    for (int i = 0; i < nums.length - 1; i++) {
        for (int j = i + 1; j < nums.length; j++) {
            if (nums[j] == nums[i]) return true;
        }
    }
    return false;
}
```

### #2 Sorting

```java

// TC: O(nlogn), SC: O(1)
public boolean containsDuplicate_Sort(int[] nums) {
    Arrays.sort(nums);
    for (int i = 1; i < nums.length; i++) {
        if (nums[i] == nums[i - 1]) return true;
    }
    return false;
}
```

### #3 HashSet

```java
// TC: O(n), SC: O(n)
public boolean containsDuplicate_HashSet(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        if (set.contains(num)) return true;
        set.add(num);
    }
    return false;
}
```

## Common Pitfalls to Avoid
- N/A

# [Valid Anagram](https://leetcode.com/problems/valid-anagram/)

## Intro

![Untitled](1_Blind75/Array%20Hash%2037970/Untitled%202.png)

## Thoughts

- Use HashMap. This will take care of followup as well. If input has other unicode characters, then HashMap is the best choice. If it has only lowercase(or limited set), then you could use a normal 26 length array for example. But better to go with Hashmap for extensibility.

## Solutions

### #1 Sorting

```java
// TC: O(nlogn), SC: O(1)
public boolean isAnagram_Sort_ShortCode(String s, String t) {
    if (s.length() != t.length()) return false;

    char[] arr1 = s.toCharArray();
    char[] arr2 = t.toCharArray();
    Arrays.sort(arr1);
    Arrays.sort(arr2);
    return Arrays.equals(arr1, arr2);
```

### #2 HashMap

```java
// TC: O(n), SC: O(n)
public boolean isAnagram_HashMap_AnotherWay(String s, String t) {
    if (s.length() != t.length()) return false;

    Map<Character, Integer> map = new HashMap<>();
    for (char c : s.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);

    for (char c : t.toCharArray()) {
        if (!map.containsKey(c) || map.get(c) == 0) return false;
        map.put(c, map.get(c) - 1);
    }
    return true;
}

// TC: O(n), SC: O(n)
public boolean isAnagram_HashMap(String s, String t) {
    Map<Character, Integer> map = new HashMap<>();
    for (char c : s.toCharArray()) map.put(c, 1 + map.getOrDefault(c, 0));

    for (char c : t.toCharArray()) {
        if (map.containsKey(c)) {
            int count = map.get(c) - 1;
            if (count == 0) map.remove(c);
            else map.put(c, count);
        } else return false;
    }

    return map.size() == 0;
}
```

## Common Pitfalls to Avoid

- remember to check map.size() == 0 at the end as well. You could also map.isEmpty() btw.

# [Missing Number](https://leetcode.com/problems/missing-number/)

## Intro

![Untitled](1_Blind75/Array%20Hash%2037970/Untitled%203.png)

## Thoughts

- Pretty straightforward to use HashSet.
- For O(1) space, you need to use XOR. Lets say n = 4 and missing number is 3. A = [1,2,4]. First xor all the numbers in the array and also xor the range(1,n) which is (1,4).
- So we will have (1 ^ 2 ^ 4) ^ (1 ^ 2 ^ 3 ^ 4). For XOR, a ^ a = 0. Meaning if you xor same number with itself, then it will cancel itself. Therefore 1,2,4 will be canceled in above expression and we will be left with the missing number 3.

## Solutions

### #1 Sorting

```java
// TC: O(nlogn), SC: O(1)
public int missingNumber_SortApproach(int[] nums) {
    Arrays.sort(nums);

    int n = nums.length;

    if (nums[n - 1] != n) return n;
    else if (nums[0] != 0) return 0;

    for (int i = 1; i < n; i++) {
        int expectedNum = nums[i - 1] + 1;
        if (nums[i] != expectedNum) return expectedNum;
    }

    return -1;
}
```

### #2 HashSet

```java
// TC: O(n), SC: O(n)
public int missingNumber_HashSetWay(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) set.add(num);

    int expectedNumCount = nums.length + 1;
    for (int number = 0; number < expectedNumCount; number++) {
        if (!set.contains(number)) return number;
    }
    return -1;
}
```

### #3 HashMap or bucket way

```java
// TC: O(n), SC: O(n)
public int missingNumber_BucketWay(int[] nums) {
    int bucket[] = new int[nums.length + 1];
    for (int num : nums) bucket[num] = 1;
    for (int i = 0; i < bucket.length; i++) {
        if (bucket[i] != 1) return i;
    }
    return 0;
}
```

### #4 Math Sum Way

```java
// TC: O(n), SC: O(1)
public int missingNumber_MathWay(int[] nums) {
    int n = nums.length, actualSum = n * (n + 1) / 2;
    int arraySum = Arrays.stream(nums).sum();
    return actualSum - arraySum;
}
```

### #5 XOR Way

```java
// Awesome explanation by Vivekanand https://www.youtube.com/watch?v=Dq0jQX3YNKg
// TC: O(n), SC: O(1)
public int missingNumber_XORWay(int[] nums) {
    int ans = nums.length;
    for (int i = 0; i < nums.length; i++) ans ^= nums[i] ^ i;
    return ans;
}
```

## Common Pitfalls to Avoid

- N/A

---

# [Group Anagrams](https://leetcode.com/problems/group-anagrams/)

## Intro

![Untitled](1_Blind75/Array%20Hash%2037970/Untitled%205.png)

## Thoughts

- The immediate thought would be to have sorted string key and then value to be `List<String>`
- However while sorting each word, you get log TC
- To improve this, you can have an ascii array of 26 and then use that to build a unique key.
- key can be of the form `#1#0#2` etc where index represents the character while the number represents the frequency.

## Solutions

### #1 Sorting Key

```java
// TC: O(w * nlogn), SC: O(wn)
// where w is no.of words and n is length of longest word
public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> map = new HashMap<>();
    for (String s : strs) {
        char arr[] = s.toCharArray();
        Arrays.sort(arr);
        String key = new String(arr);
        if (!map.containsKey(key)) map.put(key, new ArrayList<>());
        map.get(key).add(s);
    }

    return new ArrayList<>(map.values());
```

### #2 Clever HashKey Scheme

```java
// TC: O(wn), SC: O(wn)
// This is better than other solutions below because there is no sorting of keys involved
public List<List<String>> groupAnagrams_Hash(String[] strs) {
    Map<String, List<String>> map = new HashMap<>();
    int count[] = new int[26];
    for (String s : strs) {
        Arrays.fill(count, 0);
        for (char c : s.toCharArray()) count[c - 'a']++;

        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < count.length; i++) sb.append('#').append(count[i]);

        String key = sb.toString();
        if (!map.containsKey(key)) map.put(key, new ArrayList<>());
        map.get(key).add(s);
    }
    return new ArrayList<>(map.values());
}
```

## Common Pitfalls to Avoid
- N/A