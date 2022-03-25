
# Remove Linked List Elements
## Intro
https://leetcode.com/problems/remove-linked-list-elements/
![[Pasted image 20220321145134.png]]

## Thoughts
- N/A

## Solutions
### #1 Simple

```java
    // TC: O(n), SC: O(1)
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(-1), prev = dummy, cur = head;
        while (cur != null) {
            if (cur.val != val) {
                prev.next = cur;
                prev = prev.next;
            }
            cur = cur.next;
        }
        prev.next = null;
        return dummy.next;
    }

    // TC: O(n), SC: O(1)
    public ListNode removeElements_LC_WAY(ListNode head, int val) {
        ListNode dummy = new ListNode(-1), prev = dummy, cur = head;
        dummy.next = head;
        while (cur != null) {
            if (cur.val == val) prev.next = cur.next;
            else prev = cur;
            cur = cur.next;
        }
        return dummy.next;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Remove Duplicates From Sorted Array
## Intro
https://leetcode.com/problems/remove-duplicates-from-sorted-array/
![[Pasted image 20220321145239.png]]
![[Pasted image 20220321145247.png]]

## Thoughts
- N/A

## Solutions
### #1 Approach 1

```java
    // TC: O(n), SC: O(1)
    public static int remove_Mine(int[] A) {
        if (A.length <= 1) return A.length;
        int k = 0;
        for (int i = 1; i < A.length; i++) {
            if (A[k] != A[i]) A[++k] = A[i];
        }
        return k + 1;
    }
```

### #2 Approach 2
```java
    // TC: O(n), SC: O(1)
    public int removeDuplicates_MineAnotherWay(int[] A) {
        int k = 0;
        for (int i = 0; i < A.length; i++) {
            if (i == 0 || A[i] != A[i - 1]) A[k++] = A[i];
        }
        return k;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Odd Even Linked List
## Intro
https://leetcode.com/problems/odd-even-linked-list/
![[Pasted image 20220321145357.png]]

## Thoughts
- N/A

## Solutions
### #1 Simple

```java
    // TC: O(n), SC: O(1)
    public ListNode oddEvenList(ListNode head) {
        if (head == null) return null;
        ListNode odd = head, even = odd.next, evenHead = even;
        while (even != null && even.next != null) {
            odd.next = even.next;
            odd = odd.next;
            even.next = odd.next;
            even = even.next;
        }
        odd.next = evenHead;
        return head;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Add Two Numbers
## Intro
https://leetcode.com/problems/add-two-numbers/
![[Pasted image 20220321145450.png]]

![[Pasted image 20220321145459.png]]

## Thoughts
- N/A

## Solutions
### #1 Simple
```java
    // TC: O(max(m,n)), SC: O(max(m,n))
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1), p1 = l1, p2 = l2, cur = dummy;
        int carry = 0;
        while (p1 != null || p2 != null) {
            int x = (p1 != null) ? p1.val : 0;
            int y = (p2 != null) ? p2.val : 0;
            int sum = carry + x + y;
            carry = sum / 10;
            cur.next = new ListNode(sum % 10);
            cur = cur.next;
            if (p1 != null) p1 = p1.next;
            if (p2 != null) p2 = p2.next;
        }
        if (carry > 0) cur.next = new ListNode(carry);
        return dummy.next;
    }

    // TC: O(max(m,n)), SC: O(max(m,n))
    public ListNode addTwoNumbers_AnotherWay(ListNode l1, ListNode l2) {
        ListNode cur1 = l1, cur2 = l2;
        ListNode res = new ListNode(-1), iter = res;
        int carry = 0;
        while (cur1 != null && cur2 != null) {
            int sum = (cur1.val + cur2.val + carry);
            carry = sum >= 10 ? 1 : 0;
            iter.next = new ListNode(sum % 10);
            iter = iter.next;
            cur1 = cur1.next;
            cur2 = cur2.next;
        }
        while (cur1 != null) {
            int sum = carry + cur1.val;
            carry = sum >= 10 ? 1 : 0;
            iter.next = new ListNode(sum % 10);
            iter = iter.next;
            cur1 = cur1.next;
        }
        while (cur2 != null) {
            int sum = carry + cur2.val;
            carry = sum >= 10 ? 1 : 0;
            iter.next = new ListNode(sum % 10);
            iter = iter.next;
            cur2 = cur2.next;
        }
        if (carry == 1) iter.next = new ListNode(carry);
        return res.next;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Swap Nodes In Pairs
## Intro
https://leetcode.com/problems/swap-nodes-in-pairs/
![[Pasted image 20220321145555.png]]

## Thoughts
- N/A

## Solutions
### #1 Recursive LC Way

```java
    // TC: O(n), SC: O(n)
    public ListNode swapPairs_Recursive_LCWay(ListNode head) {
        if (head == null || head.next == null) return head;

        // nodes to be swapped
        ListNode second = head.next;

        head.next = swapPairs_Recursive_LCWay(second.next);
        second.next = head;

        return second;
    }
```

### #2 Iterative LC Way
```java
    // TC: O(n), SC: O(1)
    public ListNode swapPairs_Iterative_LCWay(ListNode head) {
        ListNode dummy = new ListNode(), prev = dummy;
        dummy.next = head;
        while (head != null && head.next != null) {
            ListNode first = head, second = head.next;

            // swapping
            prev.next = second;
            first.next = second.next;
            second.next = first;

            prev = first;
            head = first.next;
        }
        return dummy.next;
    }
```

### #3 Recursive Mine
```java
    // TC: O(n), SC: O(n)
    public ListNode swapPairs_Recursive_MyWay(ListNode cur) {
        if (cur == null || cur.next == null) return cur;

        ListNode prev = cur;
        cur = cur.next;
        ListNode next = cur.next;
        cur.next = prev;
        prev.next = swapPairs_Recursive_MyWay(next);
        return cur;
    }
```

### #4 Iterative My Way
```java
    // TC: O(n), SC: O(1)
    public ListNode swapPairs_Iterative_MyWay(ListNode head) {
        ListNode prev = null, cur = head;
        while (cur != null && cur.next != null) {
            ListNode before = cur;
            cur = cur.next;
            ListNode next = cur.next;
            cur.next = before;
            before.next = null;

            if (prev == null) head = cur;
            else prev.next = cur;

            prev = before;
            cur = next;
        }
        if (prev != null) prev.next = cur;
        return head;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Sort List
## Intro
https://leetcode.com/problems/sort-list/
![[Pasted image 20220321145735.png]]
![[Pasted image 20220321145741.png]]

## Thoughts
- N/A

## Solutions
### #1 BottomUP

```java
    ListNode tail = new ListNode();
    ListNode nextSubList = new ListNode();

    // TC: O(nlogn), SC: O(logn)
    public ListNode sortList_BottomUp(ListNode head) {
        if (head == null || head.next == null) return head;

        int n = getCount(head);
        ListNode start = head, dummy = new ListNode();
        for (int size = 1; size < n; size *= 2) {
            tail = dummy;
            while (start != null) {
                if (start.next == null) {
                    tail.next = start;
                    break;
                }
                ListNode mid = split(start, size);
                merge(start, mid);
                start = nextSubList;
            }
            start = dummy.next;
        }
        return dummy.next;
    }

    private void merge(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(), newTail = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                newTail.next = l1;
                l1 = l1.next;
            } else {
                newTail.next = l2;
                l2 = l2.next;
            }
            newTail = newTail.next;
        }
        newTail.next = l1 == null ? l2 : l1;
        while (newTail.next != null) newTail = newTail.next;
        tail.next = dummy.next;
        tail = newTail;
    }

    private ListNode split(ListNode start, int size) {
        ListNode prev = start, end = start.next;

        for (int i = 1; i < size && (prev.next != null || end.next != null); i++) {
            if (end.next != null) end = (end.next.next != null) ? end.next.next : end.next;
            if (prev.next != null) prev = prev.next;
        }
        ListNode mid = prev.next;
        prev.next = null;
        nextSubList = end.next;
        end.next = null;
        return mid;
    }

    private int getCount(ListNode head) {
        int count = 0;
        ListNode ptr = head;
        while (ptr != null) {
            ptr = ptr.next;
            count++;
        }
        return count;
    }
```

### #2 TopDown
```java
    // TC: O(nlogn), SC: O(logn)
    public ListNode sortList_TopDown(ListNode head) {
        if (head == null || head.next == null) return head;

        ListNode mid = getMid(head);
        ListNode left = sortList_TopDown(head);
        ListNode right = sortList_TopDown(mid);
        return merge_TopDown(left, right);
    }

    private ListNode merge_TopDown(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(), tail = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                tail.next = l1;
                l1 = l1.next;
            } else {
                tail.next = l2;
                l2 = l2.next;
            }
            tail = tail.next;
        }
        tail.next = l1 == null ? l2 : l1;
        return dummy.next;
    }

    private ListNode getMid(ListNode head) {
        ListNode prev = null;
        while (head != null && head.next != null) {
            prev = prev == null ? head : prev.next;
            head = head.next.next;
        }
        ListNode mid = prev.next;
        prev.next = null;
        return mid;
    }
```

## Common Pitfalls to Avoid
- N/A

---