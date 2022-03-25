
# LinkedList Cycle Length(Grokking)
## Intro
![[Pasted image 20220322131616.png]]

## Thoughts
- N/A

## Solutions
### #1 Grokking
```java
    // TC: O(n), SC: O(1)
    public static int findCycleLength(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            // found the cycle
            if (slow == fast) return calculateLength(slow);
        }
        return 0;
    }

    private static int calculateLength(ListNode slow) {
        ListNode current = slow;
        int cycleLength = 0;
        do {
            current = current.next;
            cycleLength++;
        } while (current != slow);
        return cycleLength;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Problem Name
## Intro
https://leetcode.com/problems/linked-list-cycle-ii/
![[Pasted image 20220322131801.png]]
![[Pasted image 20220322131806.png]]

## Thoughts
- N/A

## Solutions
### #1 Two Pointers
```java
    // TC: O(n), SC: O(n)
    public ListNode detectCycle_AnotherWay(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) break;
        }
        if (fast == null || fast.next == null) return null;
        slow = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }

    // TC: O(n), SC: O(1)
    public ListNode detectCycle_MyWay(ListNode head) {
        if (head == null || head.next == null) return null;
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) break;
        }
        slow = head;
        while (slow != fast) {
            if (fast == null) return null;
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }
```

### #2 LC
```java
    // TC: O(n), SC: O(1)
    public ListNode detectCycle_Best_LCWay(ListNode head) {
        if (head == null) return null;

        ListNode meetingNode = getIntersect(head);
        if (meetingNode == null) return null;

        ListNode l1 = head, l2 = meetingNode;

        while (l1 != l2) {
            l1 = l1.next;
            l2 = l2.next;
        }

        return l1;
    }

    private ListNode getIntersect(ListNode head) {
        ListNode tortoise = head, hare = head;

        while (hare != null && hare.next != null) {
            tortoise = tortoise.next;
            hare = hare.next.next;
            if (tortoise == hare) return tortoise;
        }
        return null;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Happy Number
## Intro
https://leetcode.com/problems/happy-number/
![[Pasted image 20220322131903.png]]

## Thoughts
- N/A

## Solutions
### #1 Set
```java
    // TC: O(logn), SC: O(logn)
    public boolean isHappy_HashSet(int n) {
        Set<Integer> set = new HashSet<>();
        while (n != 1) {
            if (set.contains(n)) return false;
            set.add(n);
            n = getSquared(n);
        }
        return true;
    }
```

### #2 Fast and Slow
```java
    // TC: O(logn), SC: O(logn)
    // TODO why is TC and SC logn?
    public boolean isHappy(int n) {
        int slow = n, fast = n;
        do {
            slow = getSquared(slow);
            fast = getSquared(getSquared(fast));
        } while (slow != fast);
        return slow == 1;
    }

    private int getSquared(int n) {
        int sum = 0;
        while (n > 0) {
            int rem = n % 10;
            sum += rem * rem;
            n /= 10;
        }
        return sum;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Middle of the linked list
## Intro
https://leetcode.com/problems/middle-of-the-linked-list/
![[Pasted image 20220322131948.png]]

## Thoughts
- N/A

## Solutions
### #1 Two Pointers
```java
    // TC: O(n), SC: O(1)
    public ListNode middleNode(ListNode head) {
        if (head == null) return null;
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Palindrome Linked List
## Intro
https://leetcode.com/problems/palindrome-linked-list/
![[Pasted image 20220322132020.png]]

## Thoughts
- N/A

## Solutions
### #1 Recursion
```java
    ListNode frontPointer;

    // TC: O(n), SC: O(n)
    public boolean isPalindrome_Recursion(ListNode head) {
        frontPointer = head;
        return recurse(head);
    }

    private boolean recurse(ListNode cur) {
        if (cur != null) {
            if (!recurse(cur.next) || cur.val != frontPointer.val) return false;
            frontPointer = frontPointer.next;
        }
        return true;
    }
```

### #2 Two Pointers
```java
    // TC: O(n), SC: O(1)
    public boolean isPalindrome(ListNode head) {
        if (head == null) return true;
        ListNode middle = middle(head), reversed = reverse(middle.next);
        ListNode first = head, second = reversed;
        boolean res = true;
        while (second != null) {
            if (first.val != second.val) {
                res = false;
                break;
            }
            first = first.next;
            second = second.next;
        }
        // reverse(reversed) --> this is also correct. No need to set middle.next
        middle.next = reverse(reversed);
        return res;
    }

    private ListNode middle(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

    private ListNode reverse(ListNode head) {
        ListNode cur = head, prev = null;
        while (cur != null) {
            ListNode next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
        }
        return prev;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Circular Array Loop
## Intro
https://leetcode.com/problems/circular-array-loop/
![[Pasted image 20220322132104.png]]
![[Pasted image 20220322132109.png]]

## Thoughts
- N/A

## Solutions
### #1 Floyd
```java
    // todo tc?, it might be O(n) or O(n^2) revisit this.
    // grokking says its O(n^2), but I am not so sure.
    public boolean circularArrayLoop_Floyd(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            boolean isForward = nums[i] >= 0;
            int slow = i, fast = i;
            do {
                slow = getNext(nums, isForward, slow);
                fast = getNext(nums, isForward, fast);
                if (fast != -1) fast = getNext(nums, isForward, fast);
            } while (slow != -1 && fast != -1 && slow != fast);
            if (slow != -1 && slow == fast) return true;
        }
        return false;
    }

    private int getNext(int[] nums, boolean isForward, int i) {
        boolean direction = nums[i] >= 0;
        if (isForward != direction) return -1;

        int nextIndex = (i + nums[i]) % nums.length;
        if (nextIndex < 0) nextIndex += nums.length;

        return nextIndex == i ? -1 : nextIndex;
    }
```

### #2 DFS
```java
    private static final int NOT_VISITED = 0;
    private static final int VISITING = 1;
    private static final int VISITED = 2;

    public boolean circularArrayLoop_DFS(int[] A) {
        int n = A.length, visited[] = new int[n];
        for (int i = 0; i < n; ++i) if (visited[i] == NOT_VISITED && dfs(i, visited, A)) return true;
        return false;
    }

    private boolean dfs(int cur, int[] visited, int[] A) {
        if (visited[cur] == VISITING) return true;
        if (visited[cur] == VISITED) return false;

        visited[cur] = VISITING;

        int next = getNext(A, cur);

        // not a valid cycle if the length is 1
        // not a valid cycle if coming from different directions
        if (next == cur || A[next] * A[cur] < 0) {
            visited[cur] = VISITED;
            return false;
        }

        if (dfs(next, visited, A)) return true;
        visited[cur] = VISITED;
        return false;
    }

    private int getNext(int[] A, int i) {
        int j = i + A[i], n = A.length;
        if (j >= 0) return j % n;
        // correct formula for negative modulo
        return n - 1 - ((-1 - j) % n);
    }
```

## Common Pitfalls to Avoid
- N/A

---