
# Reverse Linked List II
## Intro
https://leetcode.com/problems/reverse-linked-list-ii/
![[Pasted image 20220322114130.png]]

## Thoughts
- N/A

## Solutions
### #1 Recursive
```java
    // TODO understand how this works. and also how does one come up with a solution like this in the first place?
    // TC: O(n), SC: O(n)
    public ListNode reverseBetween_Recursive(ListNode head, int m, int n) {
        this.left = head;
        recurseAndReverse(head, m, n);
        return head;
    }

    private void recurseAndReverse(ListNode right, int m, int n) {
        if (n == 1) return;

        // keep moving the right pointer one step forward until n == 1
        right = right.next;

        // keep moving the left pointer to the right until we reach the proper node
        if (m > 1) left = left.next;

        // recurse with m and n reduced
        recurseAndReverse(right, m - 1, n - 1);

        // in case both pointers cross each other or become equal, we stop
        if (left == right || right.next == left) stop = true;

        // until boolean stop is false, swap data
        if (!stop) {
            int t = left.val;
            left.val = right.val;
            right.val = t;

            // move left one step to the right
            // the right pointer moves one step back via backtracking
            left = left.next;
        }
    }
```

### #2 Iterative
```java
    // TC: O(n), SC: O(1)
    public ListNode reverseBetween_Iterative(ListNode head, int m, int n) {
        ListNode cur = head, prev = null;
        n -= (m - 1);
        while (m > 1) {
            m--;
            prev = cur;
            cur = cur.next;
            // uncomment this if you remove n -= (m - 1); in the above line before while loop
            // n--;
        }

        ListNode tail = cur, con = prev, next = null;
        while (n > 0) {
            next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
            n--;
        }

        if (con == null) head = prev;
        else con.next = prev;

        tail.next = cur;
        return head;
    }
```

### #3 My Way
```java
    private boolean stop;
    private ListNode left;

    // TC: O(n), SC: O(1)
    public ListNode reverseBetween_MyWay(ListNode head, int m, int n) {
        ListNode tail = head, nodeBeforeReversedSection = null;
        for (int i = 1; i < m; i++) {
            nodeBeforeReversedSection = tail;
            tail = tail.next;
        }

        ListNode ptr = tail, prev = null;
        for (int i = 0; i < n - m + 1; i++) {
            ListNode next = ptr.next;
            ptr.next = prev;
            prev = ptr;
            ptr = next;
        }
        tail.next = ptr;

        if (nodeBeforeReversedSection != null) nodeBeforeReversedSection.next = prev;
        return nodeBeforeReversedSection == null ? prev : head;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Reverse Nodes in K Group
## Intro
https://leetcode.com/problems/reverse-nodes-in-k-group/
![[Pasted image 20220322114245.png]]
![[Pasted image 20220322114250.png]]

## Thoughts
- N/A

## Solutions
### #1 Recursive Mine
```java
    // TC: O(n), SC: O(n/k)
    public ListNode reverseKGroup_Recursive_My_Solution(ListNode head, int k) {
        int n = 0;
        ListNode cur = head;
        while (cur != null) {
            cur = cur.next;
            n++;
        }
        return helperMine(head, k, 0, n);
    }

    private ListNode helperMine(ListNode head, int k, int i, int n) {
        if (head == null || i + k - 1 > n - 1) return head;

        int count = 0;
        ListNode cur = head, prev = null;
        while (cur != null && count++ < k) {
            ListNode next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
        }
        head.next = helperMine(cur, k, i + k, n);
        return prev;
    }
```

### #2 Recursive LC Solution
```java
    // TC: O(n), SC: O(n/k)
    public ListNode reverseKGroup_Recursive_LC_Solution(ListNode head, int k) {
        int count = 0;
        ListNode cur = head;
        // the below loop is just to check if we have at least k nodes
        while (cur != null && count < k) {
            cur = cur.next;
            count++;
        }

        if (count == k) {
            ListNode reversedHead = reverse(head, k);
            head.next = reverseKGroup_Recursive_LC_Solution(cur, k);
            return reversedHead;
        }
        return head;
    }

    private ListNode reverse(ListNode head, int k) {
        ListNode cur = head, prev = null;
        while (k-- > 0) {
            ListNode next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
        }
        return prev;
    }
```

### #3 LC Iterative
```java
    // TC: O(n), SC: O(1)
    public ListNode reverseKGroup_Iterative_LC_Solution(ListNode head, int k) {
        ListNode cur = head, kTail = null, newHead = null;

        while (cur != null) {
            int count = 0;

            cur = head;

            while (count < k && cur != null) {
                cur = cur.next;
                count++;
            }

            if (count == k) {
                ListNode revHead = reverse(head, k);

                if (newHead == null) newHead = revHead;
                if (kTail != null) kTail.next = revHead;

                kTail = head;
                head = cur;
            }
        }

        if (kTail != null) kTail.next = head;
        return newHead == null ? head : newHead;
    }
```

### #4 Iterative Simpler
```java
    // TC: O(n), SC: O(1)
    public ListNode reverseKGroup_Iterative_Simpler(ListNode head, int k) {
        int n = 0;
        ListNode cur = head;
        while (cur != null) {
            n++;
            cur = cur.next;
        }

        ListNode dummy = new ListNode(-1), nHead = dummy, nTail = null;
        for (int i = 0; i < n / k; i++) {
            nTail = head; // save position to jump later
            for (int j = 0; j < k; j++) {
                ListNode next = head.next;
                head.next = nHead.next;
                nHead.next = head;
                head = next;
            }
            nHead = nTail;
        }
        nTail.next = head;
        return dummy.next;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Rotate List
## Intro
https://leetcode.com/problems/rotate-list/
![[Pasted image 20220322114425.png]]
![[Pasted image 20220322114430.png]]

## Thoughts
- N/A

## Solutions
### #1 Iterative LC
```java
    // TC: O(n), SC: O(1)
    public ListNode rotateRight_Iterative_LCWay(ListNode head, int k) {
        if (head == null || head.next == null) return head;

        ListNode oldTail = head;
        int n;
        for (n = 1; oldTail.next != null; n++) oldTail = oldTail.next;

        oldTail.next = head;

        ListNode newTail = head;
        for (int i = 0; i < n - (k % n) - 1; i++) newTail = newTail.next;

        ListNode newHead = newTail.next;
        newTail.next = null;
        return newHead;
    }
```

### #2 Iterative My Way
```java
    // TC: O(n), SC: O(1)
    public ListNode rotateRight_Iterative_MyWay(ListNode head, int k) {
        if (head == null || k == 0) return head;

        ListNode tail = null, cur = head;
        int n = 0;
        while (cur != null) {
            tail = cur;
            cur = cur.next;
            n++;
        }
        k %= n;
        int m = n - k;

        cur = head;
        while (m-- > 1) cur = cur.next;
        tail.next = head;
        head = cur.next;
        cur.next = null;
        return head;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Reverse Alternating K Sub List
## Intro
![[Pasted image 20220322114619.png]]

## Thoughts
- N/A

## Solutions
### #1 Grokking
```java
    // TC: O(n), SC: O(1)
    public static ListNode reverse(ListNode head, int k) {
        if (k <= 1 || head == null)
            return head;

        ListNode current = head, previous = null;
        while (current != null) { // break if we've reached the end of the list
            ListNode lastNodeOfPreviousPart = previous;
            // after reversing the LinkedList 'current' will become the last node of the sub-list
            ListNode lastNodeOfSubList = current;
            ListNode next = null; // will be used to temporarily store the next node
            // reverse 'k' nodes
            for (int i = 0; current != null && i < k; i++) {
                next = current.next;
                current.next = previous;
                previous = current;
                current = next;
            }

            // connect with the previous part
            if (lastNodeOfPreviousPart != null)
                lastNodeOfPreviousPart.next = previous; // 'previous' is now the first node of the sub-list
            else // this means we are changing the first node (head) of the LinkedList
                head = previous;

            // connect with the next part
            lastNodeOfSubList.next = current;

            // skip 'k' nodes
            for (int i = 0; current != null && i < k; ++i) {
                previous = current;
                current = current.next;
            }
        }

        return head;
    }
```

## Common Pitfalls to Avoid
- N/A

---