# Bits

# [Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/)

## Intro

![Untitled](1_Blind75/Bits%20702f7/Untitled.png)

## Thoughts

- This looks like a easy question the surface. Just keep ANDing with 1 and right shifting until n becomes zero.

## Solutions

### #1 Unsigned Right Shift Operator(>>>)

```java
// >> is signed rightshift operator while >>> is unsigned right shift operator
// >>> will always add 0 to leftMost Side, while >> is not adding in my jdk
// TC: O(32) or O(1), SC: O(1)
// https://stackoverflow.com/questions/2244387/bitwise-shift-operators-signed-and-unsigned
public int loopAndFlip_Using_UnsignedRightShiftOperator(int n) {
    int count = 0;
    while (n != 0) {
        count += (n & 1);
        n >>>= 1;
    }
    return count;
}
```

### #2 Bit trick(n & n-1)

```java
// least significant(aka rightMost) 1 bit in n will always be a zero in n-1. 
// Therefore if we AND n & n-1, then least significant(aka rightMost) 1 bit will 
// become zero. We do above process until n!=0
// TC: O(32) or O(1), SC: O(1)
public int bitManipulationTrick(int n) {
    int count = 0;
    while (n != 0) {
        count++;
        n = n & (n - 1);
    }
    return count;
}
```

## Common Pitfalls to Avoid

- Java has two right shift operators. `>>(signed right shift operator)` and `>>>(unsigned right shift operator)`. >> will not work for this problem, instead use `>>>`.  >> will always use the leftMostBit as filler. So even if you keep right shifting, if leftMostBit is 1, then that 1 will always stay in the number irrespective of how many times you right shift. On the other hand >>> will always use zero as filler([SO link](https://stackoverflow.com/questions/2244387/bitwise-shift-operators-signed-and-unsigned))

# [Reverse Bits](https://leetcode.com/problems/reverse-bits/)

## Intro

![Untitled](1_Blind75/Bits%20702f7/Untitled%201.png)

## Thoughts

- Get LSB(rightMost) in n and then move it all the way to MSB(leftMost). Now do this repeatedly. Next time, move it to MSB + 1 and do rightShift on n.
- There are other solutions, but don’t spend too much time worrying about those. If they are expected in interviews, so be it(dont want to work for such companies/roles anyway).

## Solutions

### Approach #1

```java
// TC: O(32)/O(1), SC: O(1)
public int reverseBits_BitByBit(int n) {
    int res = 0;
    for (int power = 31; power >= 0; power--) {
        res += (n & 1) << power;
        n >>= 1;
    }
    return res;
}
```

### Approach #2

## Common Pitfalls to Avoid

- N/A

# [Counting Bits](https://leetcode.com/problems/counting-bits/)

## Intro

![Untitled](1_Blind75/Bits%20702f7/Untitled%202.png)

## Thoughts

- Straightforward approach is to run a loop and use n & (n - 1) trick that we learned before.

## Solutions

### #1 nlogn approach: n & (n - 1) trick

```java
// TC: O(n * k), SC: O(n), where k is no.of operations in count_FastTrick.
// And k will be number of 1 bits in a given num. This will be a bit faster than countBits_SlowerLoop
// TC: O(nlogn), SC: O(n)
public int[] countBits_FasterLoop(int num) {
    int res[] = new int[num + 1];
    for (int i = 0; i < res.length; i++) res[i] = count_FastTrick(i);
    return res;
}

private int count_FastTrick(int n) {
    int count = 0;
    while (n != 0) {
        // zeroing out the least significant non-zero bit
        n &= n - 1;
        count++;
    }
    return count;
}
```

### #2 Power of 2 trick

```java
// TC: O(n * log(32)) ???, SC: O(n), I think log32 coz of Math functions
public int[] countBits_DP_Math_Log_Way(int num) {
    if (num == 0) return new int[1];
    int res[] = new int[num + 1];
    res[0] = 0;
    res[1] = 1;
    for (int i = 2; i < res.length; i++) {
        if (isPowerOfTwo(i)) res[i] = 1;
        else {
            int c = (int) (log(i) / log(2));
            int k = (int) pow(2, c);
            res[i] = res[k] + res[i - k];
        }
    }
    return res;
}

private boolean isPowerOfTwo(int x) {
    return x != 0 && (x & (x - 1)) == 0;
}
```

### #3 LSB trick

```java
// TC: O(n), SC: O(n)
public int[] countBits_DP_LSB(int num) {
    int[] res = new int[num + 1];
    for (int i = 1; i <= num; i++) {
        res[i] = res[i / 2] + (i % 2);
        // can also be written as below
        // res[i] = res[i >> 1] + (i & 1);
    }
    return res;
}
```

### #4 DP Last Set Bit trick

```java
// TC: O(n), SC: O(n)
public int[] countBits_DP_LastSetBit(int num) {
    int res[] = new int[num + 1];
    for (int i = 1; i <= num; i++) res[i] = res[i & (i - 1)] + 1;
    return res;
}
```

## Common Pitfalls to Avoid

- N/A

# [Sum of Two Integers](https://leetcode.com/problems/sum-of-two-integers/)

## Intro

![Untitled](1_Blind75/Bits%20702f7/Untitled%203.png)

## Thoughts

- There is no way to solve this if it comes in an interview, just skip this question

## Solutions

### #1 Generic

```java
// TC: O(1), SC: O(1)
public int getSum(int a, int b) {
    int x = Math.abs(a), y = Math.abs(b);

    // ensure that x >= y
    if (x < y) return getSum(b, a);

    int sign = a > 0 ? 1 : -1;

    if (a * b >= 0) {
        // sum of two positive integers: x + y
        while (y != 0) {
            int answer = x ^ y;
            int carry = (x & y) << 1;
            x = answer;
            y = carry;
        }
    } else {
        // difference of two positive intergers x + y
        while (y != 0) {
            int answer = x ^ y;
            int borrow = ((~x) & y) << 1;
            x = answer;
            y = borrow;
        }

    }

    return x * sign;
}
```

### #2 Java Specific

```java
// TC: O(1), SC: O(1)
public int getSum_JavaSpecific(int a, int b) {
    while (b != 0) {
        int answer = a ^ b;
        int carry = (a & b) << 1;
        a = answer;
        b = carry;
    }
    return a;
}
```

## Common Pitfalls to Avoid

- N/A