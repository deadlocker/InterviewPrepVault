# LinkedList

# [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

## Intro

![Untitled](1_Blind75/LinkedList%206c48a/Untitled.png)

## Thoughts

- Notice that these lists are already sorted. So while looping you can create a dummy node and keep building the linked list.

## Solutions

### Iterative using &&

```java
// TC: O(m + n), SC: O(1)
public ListNode mergeTwoLists_Iterative_And(ListNode l1, ListNode l2) {
    ListNode dummy = new ListNode(-1), cur = dummy;
    while (l1 != null && l2 != null) {
        if (l1.val <= l2.val) {
            cur.next = l1;
            l1 = l1.next;
        } else {
            cur.next = l2;
            l2 = l2.next;
        }
        cur = cur.next;
    }
    cur.next = l1 == null ? l2 : l1;
    return dummy.next;
```

### Iterative using ||

```java
// TC: O(m + n), SC: O(1)
public ListNode mergeTwoLists_Or(ListNode l1, ListNode l2) {
    ListNode p1 = l1, p2 = l2, prev = new ListNode(-1), backup = prev;
    while (p1 != null && p2 != null) {
        if (p1.val <= p2.val) {
            prev.next = p1;
            p1 = p1.next;
        } else {
            prev.next = p2;
            p2 = p2.next;
        }
        prev = prev.next;
    }
    prev.next = p1 != null ? p1 : p2;
    return backup.next;
}
```

### Recursion

```java
// TC: O(m + n), SC: O(m + n)
public ListNode mergeTwoLists_Recursive(ListNode l1, ListNode l2) {
    if (l1 == null) return l2;
    if (l2 == null) return l1;

    if (l1.val <= l2.val) {
        l1.next = mergeTwoLists_Recursive(l1.next, l2);
        return l1;
    } else {
        l2.next = mergeTwoLists_Recursive(l1, l2.next);
        return l2;
    }
}
```

## Common Pitfalls to Avoid

- Be careful with terminating condition in while loop. If you use `&&`, then it will be an early termination which means that you will have to handle the remaining list outside the while loop. On the other if you use `||`, then you need to handle null pointers within the while loop.

# [Reverse LinkedList](https://leetcode.com/problems/reverse-linked-list/)

## Intro

![Untitled](1_Blind75/LinkedList%206c48a/Untitled%201.png)

![Untitled](1_Blind75/LinkedList%206c48a/Untitled%202.png)

## Thoughts

- Iterative Solution is very simple. 4 steps.
- For recursion, pass head, null to init the solution. The helper takes cur,prev. This depends on faith trick. [cur.next](http://cur.next) = prev and then we call helper to recursively reverse the next.

## Solutions

### #1 Iterative

```java
// TC: O(n), SC: O(1)
public static LinkedList iterativeApproach(LinkedList head) {
    LinkedList prev = null, cur = head;
    while (cur != null) {
        LinkedList next = cur.next;
        cur.next = prev;
        prev = cur;
        cur = next;
    }
    return prev;
}

static class LinkedList {
    int value;
    LinkedList next = null;

    public LinkedList(int value) {
        this.value = value;
    }
}
```

### #2 Recursion

```java
//TC: O(n), SC: O(n)
public static LinkedList recursiveApproach(LinkedList head) {
    return helper(head, null);
}

public static LinkedList helper(LinkedList cur, LinkedList prev) {
    if (cur == null) return prev;
    LinkedList next = cur.next;
    cur.next = prev;
    return helper(next, cur);
}
```

## Common Pitfalls to Avoid

- Remember to return prev in iterative solution. Not cur.

# [LinkedList Cycle](https://leetcode.com/problems/linked-list-cycle/)

## Intro

![Untitled](1_Blind75/LinkedList%206c48a/Untitled%203.png)

![Untitled](1_Blind75/LinkedList%206c48a/Untitled%204.png)

## Thoughts

- The first and immediate thought would be to use HashSet.
- For the follow up, the floyd cycle detection makes sense(however you would need to know this). I don’t see how one could come up with this on their own.
- Even if you know this, deriving and proving it might be tricky especially the true proof that involves mod operator.
- Image

![Untitled](1_Blind75/LinkedList%206c48a/Untitled%205.png)

- The above proof is not 100% correct but gives the correct intuition. Check the next image which is extension of the above proof.
- [https://cs.stackexchange.com/questions/10360/floyds-cycle-detection-algorithm-determining-the-starting-point-of-cycle](https://cs.stackexchange.com/questions/10360/floyds-cycle-detection-algorithm-determining-the-starting-point-of-cycle)

![Untitled](1_Blind75/LinkedList%206c48a/Untitled%206.png)

## Solutions

### #1 HashSet

```java
// TC: O(n), SC: O(n)
public boolean hasCycle_SetApproach(ListNode head) {
    Set<ListNode> set = new HashSet<>();
    while (head != null) {
        if (set.contains(head)) return true;
        set.add(head);
        head = head.next;
    }
    return false;
}
```

### #2 Floyd Cycle

```java
// TC: O(n + k), SC: O(1)
public boolean hasCycle_Better(ListNode head) {
    if (head == null) return false;

    ListNode slow = head, fast = head.next;
    while (fast != null && fast.next != null) {
        if (slow == fast) return true;

        slow = slow.next;
        fast = fast.next.next;
    }
    return false;
}

// TC: O(n + k), SC: O(1), where k is the cycle length(or point at which they meet).
// Check LC for explanation about Time complexity and also intuition behind proof for this algorithm
public boolean hasCycle_Floyd_Cycle_Algorithm(ListNode head) {
    if (head == null) return false;

    ListNode slow = head, fast = head;
    while (slow != null && fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) return true;
    }
    return false;
```

## Common Pitfalls to Avoid

- N/A

# [Remove Nth Node From End of the List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

## Intro

![Untitled](1_Blind75/LinkedList%206c48a/Untitled%207.png)

## Thoughts

- Using two traversal(first find n and then remove n - k) is easy
- A better approach is to go until k, and then from there use two pointers(one at k and one at head) and keep going until the end of list. At which point the slow pointers(one started at head) will point to the node to be deleted.

## Solutions

### #1 Two Traversals

```java
// TC: O(2n - k), SC: O(1)
public ListNode removeNthFromEnd_TwoTraversalsTillEnd(ListNode head, int k) {
    int i = 0, n = getSize(head);
    ListNode cur = head, prev = null;
    while (cur != null && i != (n - k)) {
        prev = cur;
        cur = cur.next;
        i++;
    }

    if (prev == null) return head.next;

    prev.next = prev.next.next;
    return head;
}

private int getSize(ListNode cur) {
    int n = 0;
    while (cur != null) {
        n++;
        cur = cur.next;
    }
    return n;
}
```

### #2 Better Approach

```java
// TC: O(n), SC: O(1)
// Dummy Node trick to avoid if(fast == null) check like above;
public ListNode removeNthFromEnd_DummyNodeTrick(ListNode head, int k) {
    ListNode dummy = new ListNode(0), slow = dummy, fast = dummy;
    dummy.next = head;
    // Use while loop like this
    // while(k >= 0) {
    //     fast = fast.next;
    //     k--;
    // }
    for (int i = 0; i <= k; i++) fast = fast.next;

    while (fast != null) {
        fast = fast.next;
        slow = slow.next;
    }
    slow.next = slow.next.next;
    return dummy.next;
}
```

## Common Pitfalls to Avoid

- Be careful with ≤k etc.

# [Reorder List](https://leetcode.com/problems/reorder-list/)

## Intro

![Untitled](1_Blind75/LinkedList%206c48a/Untitled%208.png)

![Untitled](1_Blind75/LinkedList%206c48a/Untitled%209.png)

## Thoughts

- Reverse from middle and then merge two lists. Looks simple but very easy to make off by one errors. Code very slowly if this shows up on interviews.

## Solutions

### #1 My Way(broken into methods)

```java
// TC: O(n), SC: O(1)
public void reorderList_AnotherWayMine(ListNode head) {
    if (head == null) return;

    ListNode middle = middle(head), reversed = reverse(middle.next);
    middle.next = null;
    merge_Mine(head, reversed);
}

private void merge_Mine(ListNode l1, ListNode l2) {
    ListNode p1 = l1, p2 = l2, dummy = new ListNode(-1);
    while (p1 != null && p2 != null) {
        ListNode p1Next = p1.next;
        dummy.next = p1;
        dummy.next.next = p2;
        dummy = dummy.next.next;

        p1 = p1Next;
        p2 = p2.next;
    }
    if (p2 == null) dummy.next = p1;
}

private ListNode middle(ListNode head) {
    ListNode slow = head, fast = head;
    while (fast.next != null && fast.next.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow;
}

public void reorderList_MyWay(ListNode head) {
    if (head == null || head.next == null) return;
    ListNode mid = findMid(head);
    ListNode reversed = reverse(mid);
    merge(head, reversed);
}

private void merge(ListNode first, ListNode second) {
    ListNode dummy = new ListNode(-1), p1 = first, p2 = second;
    while (p1 != null && p2 != null) {
        ListNode p1Next = p1.next;
        dummy.next = p1;
        dummy.next.next = p2;
        dummy = dummy.next.next;

        p1 = p1Next;
        p2 = p2.next;
    }
}

private ListNode findMid(ListNode head) {
    ListNode slow = head, fast = head, prev = null;
    while (slow != null && fast != null && fast.next != null) {
        prev = slow;
        slow = slow.next;
        fast = fast.next.next;
    }
    if (prev != null) prev.next = null;
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

### #2 LC Way

```java
// TC: O(n), SC: O(1)
public void reorderList_WithoutSomeExtraChecks(ListNode head) {
    if (head == null) return;

    // find the mid spot
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    ListNode reversed = reverse(slow), cur = head;

    while (reversed.next != null) {
        ListNode p1Next = cur.next;
        ListNode reversedNext = reversed.next;

        cur.next = reversed;
        reversed.next = p1Next;

        reversed = reversedNext;
        cur = p1Next;
    }
}
```

### #3 Recursive

```java
// TC: O(n), SC: O(n)
public void reorderList_Recursive(ListNode head) {
    if (head == null) return;
    reorder(head, head.next);
}

public ListNode reorder(ListNode root, ListNode cur) {
    if (cur == null) return root;

    // keep on passing root to the end of recursive call chain
    root = reorder(root, cur.next);

    if (root == null) return root;

    ListNode temp = null;
    if (root == cur || root.next == cur) cur.next = null;
    else {
        temp = root.next;
        root.next = cur;
        cur.next = temp;
    }
    return temp;
}
```

### #4 Stack

```java
// TC: O(?), SC: O(?)
public void reorderList_StackApproach(ListNode head) {
    Stack<ListNode> stack = new Stack<>();
    ListNode node = head;
    while (node != null) {
        stack.push(node);
        node = node.next;
    }
    ListNode cur = head;
    int size = stack.size();
    for (int i = 0; i < size / 2; i++) {
        ListNode next = cur.next;
        cur.next = stack.pop();
        if (stack.size() > size / 2) {
            cur.next.next = next;
        } else {
            cur.next.next = null;
        }
        cur = next;
    }
    if (size % 2 == 1) cur.next = null;
}
```

## Common Pitfalls to Avoid

- Careful with null pointers.
- Make sure to not have a cycle in final answer.