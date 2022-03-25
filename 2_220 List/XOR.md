
# Single Number
## Intro
https://leetcode.com/problems/single-number/
![[Pasted image 20220322113542.png]]

## Thoughts
- N/A

## Solutions
### #1 Set
```java
    // TC: O(n), SC: O(n)
    public int singleNumber_AnotherSetWay(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (set.contains(num)) set.remove(num);
            else set.add(num);
        }
        for (int num : set) return num;
        return -1;
    }

    // TC: O(n), SC: O(n)
    public int singleNumber_Set(int[] nums) {
        int sumOfSet = 0, sumOfNums = 0;
        Set<Integer> set = new HashSet<>();

        for (int num : nums) {
            if (!set.contains(num)) {
                set.add(num);
                sumOfSet += num;
            }
            sumOfNums += num;
        }
        return 2 * sumOfSet - sumOfNums;
    }
```

### #2 HashMap
```java
    // TC: O(n), SC: O(n)
    public int singleNumber_HashMapApproach(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums) map.put(num, map.getOrDefault(num, 0) + 1);
        for (int num : nums) {
            if (map.get(num) == 1) return num;
        }
        return -1;
    }
```

### #3 XOR
```java
    // TC: O(n), SC: O(1)
    public int singleNumber_XOR(int[] nums) {
        int a = 0;
        for (int num : nums) a = a ^ num;
        return a;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Complement of Base 10 Integer
## Intro
https://leetcode.com/problems/complement-of-base-10-integer/
![[Pasted image 20220322113650.png]]

## Thoughts
- N/A

## Solutions
### #1 Flip Bit By Bit
```java
    // TC: O(1), SC: O(1)
    // A total of 32 iterations at worst
    public int bitwiseComplement_FlipBitByBit(int n) {
        if (n == 0) return 1;
        int todo = n, bit = 1;
        while (todo != 0) {
            n ^= bit;
            bit <<= 1;
            todo >>= 1;
        }
        return n;
    }
```

### #2 BitMask
```java
    // TC: O(1), SC: O(1)
    // same approach as below, but using java's builtin methods
    public int bitwiseComplement_BitMask_BuiltIn(int n) {
        return n == 0 ? 1 : (Integer.highestOneBit(n) << 1) - n - 1;
    }

    // TC: O(1), SC: O(1)
    public int bitwiseComplement_BitMask(int n) {
        // l is no.of 1s in n
        int l = (int) (Math.log(n) / Math.log(2)) + 1;
        // bitmask has same length as n and contains only ones
        int bitmask = (1 << l) - 1;
        // flip all bits
        return bitmask ^ n;
    }
```

### #3 Highest One Bit
```java
    // TC: O(1), SC: O(1)
    // This is the best solution and is actually the implementation for Integer.highestOneBit(_) method
    public int bitwiseComplement_Java_HighestOneBit_Implementation(int n) {
        if (n == 0) return 1;
        int bitmask = n;
        bitmask |= (bitmask >> 1);
        bitmask |= (bitmask >> 2);
        bitmask |= (bitmask >> 4);
        bitmask |= (bitmask >> 8);
        bitmask |= (bitmask >> 16);
        // flip all bits
        return bitmask ^ n;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Flipping an Image
## Intro
https://leetcode.com/problems/flipping-an-image/
![[Pasted image 20220322113811.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce
```java
    // TC: O(mn), SC: O(mn)
    // extra space coz this is not in place
    public int[][] flipAndInvertImage_BruteForce(int[][] image) {
        int rows = image.length, cols = image[0].length;
        int[][] res = new int[rows][cols];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                res[i][cols - j - 1] = image[i][j] ^ 1;
            }
        }
        return res;
    }
```

### #2 InPlace Mine
```java
    // TC: O(mn), SC: O(1)
    public int[][] flipAndInvertImage_InPlace_Mine(int[][] image) {
        int rows = image.length, cols = image[0].length;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < (cols + 1) / 2; j++) {
                int tmp = image[i][j] ^ 1;
                image[i][j] = image[i][cols - j - 1] ^ 1;
                image[i][cols - j - 1] = tmp;
            }
        }
        return image;
    }
```

### #3 InPlace LC
```java
    // TC: O(mn), SC: O(1)
    public int[][] flipAndInvertImage_InPlace_LC(int[][] image) {
        int cols = image[0].length;
        for (int[] row : image)
            for (int i = 0; i < (cols + 1) / 2; ++i) {
                int tmp = row[i] ^ 1;
                row[i] = row[cols - 1 - i] ^ 1;
                row[cols - 1 - i] = tmp;
            }

        return image;
    }
```

## Common Pitfalls to Avoid
- N/A

---