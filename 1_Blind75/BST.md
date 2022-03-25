# Binary Search Tree

# [Lowest Common Ancestor of a BST](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

## Intro

![Untitled](1_Blind75/Binary%20Sea%201a438/Untitled.png)

![Untitled](1_Blind75/Binary%20Sea%201a438/Untitled%201.png)

## Thoughts

- Notice that all vals are unique, also notice that p and q will always be present in BST. So we dont have to handle any edge cases related to this.
- Therefore this is pretty straightforward. Also notice that root will never be null.

## Solutions

### #1 Recursive DFS

```java
// TC: O(n), SC: O(n)
public TreeNode lowestCommonAncestor_Recursive(TreeNode root, TreeNode p, TreeNode q) {
    if (p.val > root.val && q.val > root.val) return lowestCommonAncestor_Recursive(root.right, p, q);
    if (p.val < root.val && q.val < root.val) return lowestCommonAncestor_Recursive(root.left, p, q);

    return root;
}
```

### #1 Iterative

```java
// TC: O(n), SC: O(n)
public TreeNode lowestCommonAncestor_Iterative(TreeNode root, TreeNode p, TreeNode q) {
    TreeNode node = root;
    while (node != null) {
        if (p.val > node.val && q.val > node.val) node = node.right;
        else if (p.val < node.val && q.val < node.val) node = node.left;
        else return node;
    }
    return null;
}
```

## Common Pitfalls to Avoid

- N/A

---

# Kth Smallest Element in BST
## Intro
https://leetcode.com/problems/kth-smallest-element-in-a-bst/
![[Pasted image 20220320225247.png]]
![[Pasted image 20220320225255.png]]

## Thoughts
- Immediate solution is to dump inorder into list and get kth element from there.
- The next idea is to have an early break in the above brute force solution.

## Solutions
### #1 BruteForce Dump Inorder into List

```java
    // TC: O(n), SC: O(n)
    public int kthSmallest_BruteForce(TreeNode root, int k) {
        List<Integer> res = new ArrayList<>();
        inOrder(root, res);
        return res.get(k - 1);
    }

    private void inOrder(TreeNode node, List<Integer> res) {
        if (node != null) {
            inOrder(node.left, res);
            res.add(node.val);
            inOrder(node.right, res);
        }
    }
```

### #2 BruteForce Dump inorder into List(early Break)
```java
    // TC: O(n), SC: O(n)
    public int kthSmallest_EarlyBreakBasedOnK(TreeNode root, int k) {
        List<Integer> res = new ArrayList<>();
        inorder(root, res, k);
        return res.get(k - 1);
    }

    private void inorder(TreeNode root, List<Integer> res, int k) {
        if (root != null && res.size() <= k) {
            inorder(root.left, res, k);
            res.add(root.val);
            inorder(root.right, res, k);
        }
    }
```

### #3 Iterative using stack(relearn this)
```java
    // TC: O(h + k), SC: O(h), h is height of BST which can go from logn to n
    // todo how does this work?
    public int kthSmallest_Iterative(TreeNode root, int k) {
        Stack<TreeNode> stack = new Stack<>();
        while (!stack.isEmpty() || root != null) {
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            root = stack.pop();
            if (--k == 0) return root.val;
            root = root.right;
        }
        return -1;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# [Validate BST](https://leetcode.com/problems/validate-binary-search-tree/)

## Intro

![Untitled](1_Blind75/Binary%20Sea%201a438/Untitled%202.png)

![Untitled](1_Blind75/Binary%20Sea%201a438/Untitled%203.png)

## Thoughts

- Recursive is pretty easy. Follow from there.

## Solutions

### #1 Recursive

```java
// TC: O(n), SC: O(h)
public boolean isValidBST_AnotherWayUsingIntegerNull(TreeNode root) {
    return valid(root, null, null);
}

private boolean valid(TreeNode root, Integer min, Integer max) {
    if (root == null) return true;
    if ((min != null && root.val <= min) || (max != null && root.val >= max)) return false;
    return valid(root.left, min, root.val) && valid(root.right, root.val, max);
}

// TC: O(n), SC: O(h), h can go from logn to n
public boolean isValidBST_Recursive(TreeNode root) {
    return helper(root, Long.MIN_VALUE, Long.MAX_VALUE);
}

private boolean helper(TreeNode root, long min, long max) {
    return root == null
            || root.val > min && root.val < max
            && helper(root.left, min, root.val)
            && helper(root.right, root.val, max);
}
```

### #2 Stack Iterative

```java
// TC: O(n), SC: O(h)
public boolean isValidBST_Iterative_ValidRange(TreeNode root) {
    if (root == null) return true;
    Stack<Item> stack = new Stack<>();
    stack.add(new Item(root, Long.MIN_VALUE, Long.MAX_VALUE));
    while (!stack.isEmpty()) {
        Item item = stack.pop();
        TreeNode cur = item.node;
        if (cur != null) {
            long min = item.min, max = item.max;
            if (cur.val <= min || cur.val >= max) return false;
            stack.push(new Item(cur.left, min, cur.val));
            stack.push(new Item(cur.right, cur.val, max));
        }
    }
    return true;
}

@AllArgsConstructor
static class Item {
    TreeNode node;
    long min;
    long max;
}
```

### #3 Recursive Inorder(not needed)

```java
Integer prev;

// TC: O(n), SC: O(h)
// Note: We need to have prev as an instance variable. If we try to make helper method pure, then it will fail on [1,1] input.
//  This is because we depend on values that are returned from recursion but our return value for helper is a boolean.
//  So we need prev as an instance variable to use it as a side effect.
public boolean isValidBST_Recursive_InOrder(TreeNode root) {
    return helper(root);
}

private boolean helper(TreeNode root) {
    if (root == null) return true;

    if (!helper(root.left)) return false;

    // compare
    if (prev != null && root.val <= prev) return false;

    prev = root.val;
    return helper(root.right);
}
```

### #4 Iterative InOrder(not needed)

```java
// TC: O(n), SC: O(h)
// Note: Very Clever
public boolean isValidBST_Iterative_InOrder(TreeNode root) {
    Stack<TreeNode> stack = new Stack<>();
    Integer prev = null;
    while (!stack.isEmpty() || root != null) {
        // add all left subtree elements on to stack.
        while (root != null) {
            stack.push(root);
            root = root.left;
        }

        root = stack.pop();
        if (prev != null && root.val <= prev) return false;

        prev = root.val;
        root = root.right;
    }
    return true;
}
```

## Common Pitfalls to Avoid
- N/A

---

# Serialize and Deserialize BST
## Intro
https://leetcode.com/problems/serialize-and-deserialize-bst/
![[Pasted image 20220320225320.png]]

## Thoughts
- DFS and BFS is straightforward and should work for normal BT also.

## Solutions
### #1 BFS

```java
    // Encodes a tree to a single string.
    // TC: O(n), SC: O(n)
    public String serialize_BFS(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            TreeNode cur = q.poll();
            sb.append(cur == null ? "null," : cur.val + ",");
            if (cur != null) {
                q.offer(cur.left);
                q.offer(cur.right);
            }
        }
        return sb.toString();
    }

    // Decodes your encoded data to tree.
    // TC: O(n), SC: O(n)
    public TreeNode deserialize_BFS(String data) {
        String[] arr = data.split(",");
        int i = 0;
        TreeNode root = getTreeNode(arr[i++]);
        if (root == null) return null;
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            TreeNode cur = q.poll();
            cur.left = getTreeNode(arr[i++]);
            cur.right = getTreeNode(arr[i++]);

            if (cur.left != null) q.offer(cur.left);
            if (cur.right != null) q.offer(cur.right);
        }
        return root;
    }

    private TreeNode getTreeNode(String s) {
        if (s.equals("null")) return null;
        return new TreeNode(Integer.parseInt(s));
    }
```

### #2 DFS
```java
    // Encodes a tree to a single string.
    // TC: O(n), SC: O(n)
    public String serialize_DFS(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serialize(root, sb);
        return sb.toString();
    }

    private void serialize(TreeNode root, StringBuilder sb) {
        if (root == null) {
            sb.append("null,");
            return;
        }
        sb.append(root.val).append(",");
        serialize(root.left, sb);
        serialize(root.right, sb);
    }

    // Decodes your encoded data to tree.
    // TC: O(n), SC: O(n)
    public TreeNode deserialize_DFS(String data) {
        List<String> list = new ArrayList<>(Arrays.asList(data.split(",")));
        return deserialize(list);
    }

    private TreeNode deserialize(List<String> list) {
        if (list.get(0).equals("null")) {
            list.remove(0);
            return null;
        }
        TreeNode root = new TreeNode(Integer.parseInt(list.remove(0)));
        root.left = deserialize(list);
        root.right = deserialize(list);
        return root;
    }
```

### #3 Better BST PostOrder Approach
```java
    // Encodes a tree to a single string.
    // TC: O(n), SC: O(n)
    public String serialize_PostOrder(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        postorder_simpler(root, sb);
        if (sb.length() > 0) sb.deleteCharAt(sb.length() - 1);
        return sb.toString();
    }

    private void postorder_simpler(TreeNode root, StringBuilder sb) {
        if (root == null) return;

        postorder_simpler(root.left, sb);
        postorder_simpler(root.right, sb);
        sb.append(root.val).append(' ');
    }

    // Decodes your encoded data to tree.
    // TC: O(n), SC: O(n)
    public TreeNode deserialize_PostOrder(String data) {
        if (data == null || data.isEmpty()) return null;
        Deque<Integer> dq = new ArrayDeque<>();
        for (String s : data.split("\\s+")) dq.add(Integer.valueOf(s));
        return helper_simpler(Integer.MIN_VALUE, Integer.MAX_VALUE, dq);
    }

    private TreeNode helper_simpler(Integer lower, Integer upper, Deque<Integer> dq) {
        if (dq == null || dq.isEmpty()) return null;
        int val = dq.peekLast();
        if (val < lower || val > upper) return null;

        dq.pollLast();
        TreeNode root = new TreeNode(val);
        // make sure to call right first
        root.right = helper_simpler(val, upper, dq);
        root.left = helper_simpler(lower, val, dq);
        return root;
    }
```

### #4  Better BST bytes delimiter Approach(not needed)
```java
    // Encodes a tree to a single string.
    public String serialize_BytesDelimiter(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        postorder(root, sb);
        return sb.toString();
    }

    // Encodes a tree to a list.
    private void postorder(TreeNode root, StringBuilder sb) {
        if (root == null) return;
        postorder(root.left, sb);
        postorder(root.right, sb);
        sb.append(intToString(root.val));
    }

    // Encodes integer to bytes string
    private String intToString(int x) {
        char[] bytes = new char[4];
        for (int i = 3; i > -1; --i) bytes[3 - i] = (char) (x >> (i * 8) & 0xff);
        return new String(bytes);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize_BytesDelimiter(String data) {
        Deque<Integer> dq = new ArrayDeque<>();
        int n = data.length();
        for (int i = 0; i < (n / 4); ++i) dq.add(stringToInt(data.substring(4 * i, 4 * i + 4)));

        return helper_simpler(Integer.MIN_VALUE, Integer.MAX_VALUE, dq);
    }

    // Decodes list to tree.
    private TreeNode helper(Integer lower, Integer upper, Deque<Integer> dq) {
        if (dq.isEmpty()) return null;
        int val = dq.getLast();
        if (val < lower || val > upper) return null;

        dq.removeLast();
        TreeNode root = new TreeNode(val);
        root.right = helper(val, upper, dq);
        root.left = helper(lower, val, dq);
        return root;
    }

    // Decodes bytes string to integer
    private int stringToInt(String bytesStr) {
        int result = 0;
        for (char b : bytesStr.toCharArray()) result = (result << 8) + b;
        return result;
    }
```

## Common Pitfalls to Avoid
- pitfall1

---