# Tree

# [Same Tree](https://leetcode.com/problems/same-tree/)

## Intro

![Untitled](1_Blind75/Tree%204691a/Untitled.png)

![Untitled](1_Blind75/Tree%204691a/Untitled%201.png)

## Thoughts

- Pretty straightforward recursive solution.

## Solutions

### #1 DFS Recursion

```java
// TC: O(n), SC: O(h)
public boolean isSameTree_Recursive(TreeNode p, TreeNode q) {
    if (p == null && q == null) return true;
    if (p == null || q == null) return false;

    boolean left = isSameTree_Recursive(p.left, q.left);
    boolean right = isSameTree_Recursive(p.right, q.right);

    return p.val == q.val && left && right;
}
```

### #2 Stack Iterative DFS

```java
// TC: O(n), SC: O(h)
public boolean isSameTree_Iterative_DFS(TreeNode p, TreeNode q) {
    Stack<TreeNode[]> stack = new Stack<>();
    stack.push(new TreeNode[]{p, q});

    while (!stack.isEmpty()) {
        TreeNode[] cur = stack.pop();
        TreeNode x = cur[0], y = cur[1];
        if (x != null && y != null && x.val == y.val) {
            stack.push(new TreeNode[]{x.left, y.left});
            stack.push(new TreeNode[]{x.right, y.right});
        } else if (x != null || y != null) return false;

    }
    return true;
}
```

## Common Pitfalls to Avoid

- N/A

# Maximum Depth of Binary Tree

## Intro

![Untitled](1_Blind75/Tree%204691a/Untitled%202.png)

## Thoughts

- Pretty straight forward dfs

## Solutions

### #1 DFS Recursion

```java
// TC: O(n), SC: O(h), h can go from logn to n in worst case depending on how well the tree is balanced.
public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    int leftDepth = maxDepth(root.left);
    int rightDepth = maxDepth(root.right);
    return 1 + Math.max(leftDepth, rightDepth);
}
```

### #2 Iterative DFS(Stack)

```java
// TC: O(n), SC: O(h)
public int maxDepth_Iterative_DFS(TreeNode root) {
    if (root == null) return 0;
    Stack<TreeNode> stack = new Stack<>();
    Stack<Integer> depthStack = new Stack<>();
    depthStack.add(1);

    stack.add(root);
    int depth = 0, currentDepth = 0;
    while (!stack.isEmpty()) {
        root = stack.pop();
        currentDepth = depthStack.pop();
        if (root != null) {
            depth = Math.max(depth, currentDepth);
            stack.add(root.left);
            stack.add(root.right);
            depthStack.add(currentDepth + 1);
            depthStack.add(currentDepth + 1);
        }
    }
    return depth;
}

// TC: O(n), SC: O(h), h can go from logn to n in worst case depending on how well the tree is balanced.
public int maxDepth_Iterative_DFS_AnotherWay(TreeNode root) {
    if(root == null) return 0;
    int max = 0;
    Stack<Pair<TreeNode, Integer>> stack = new Stack<>();
    stack.push(new Pair<>(root, 1));
    while(!stack.isEmpty()) {
        Pair<TreeNode, Integer> pair = stack.pop();
        TreeNode cur = pair.getKey();
        int curDepth = pair.getValue();
        max = Math.max(max, curDepth);
        if(cur.left != null) stack.push(new Pair<>(cur.left, curDepth + 1));
        if(cur.right != null) stack.push(new Pair<>(cur.right, curDepth + 1));
    }
    return max;
}
```

### #3 BFS(Queue)

```java
// TC: O(n), SC: O(h)
public int maxDepth_Iterative_BFS(TreeNode root) {
    if (root == null) return 0;
    Queue<TreeNode> q = new ArrayDeque<>();
    int depth = 0, next = 0;
    q.offer(root);
    while (!q.isEmpty()) {
        depth++;
        next = q.size();
        for (int i = 0; i < next; i++) {
            TreeNode cur = q.poll();
            if (cur.left != null) q.offer(cur.left);
            if (cur.right != null) q.offer(cur.right);
        }
    }
    return depth;
}
```

## Common Pitfalls to Avoid

- Be careful with iterative stack solution. You need to propagate two things whenever you are pushing onto stack. One is the treenode itself(because later we need to evaluate node.left and node.right) and the other is currentDepth.

# [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)

## Intro

![Untitled](1_Blind75/Tree%204691a/Untitled%203.png)

## Thoughts

- Pretty straightforward recursive solution based on faith.

## Solutions

### #1 DFS Recursive

```java
// TC: O(n), SC: O(h)
public TreeNode invertTree_Recursive(TreeNode root) {
    if (root == null) return null;
    TreeNode left = invertTree_Recursive(root.left);
    root.left = invertTree_Recursive(root.right);
    root.right = left;
    return root;
}
```

### #2 DFS Iterative(Stack)

```java
// TC: O(n), SC: O(h)
public TreeNode invertTree_Iterative_DFS(TreeNode root) {
    if (root == null) return null;
    Stack<TreeNode> stack = new Stack<>();
    stack.add(root);
    while (!stack.isEmpty()) {
        TreeNode cur = stack.pop();
        if (cur != null) {
            TreeNode left = cur.left;
            cur.left = cur.right;
            cur.right = left;
            stack.add(cur.left);
            stack.add(cur.right);
        }
    }
    return root;
```

### #3 BFS Iterative(Queue)

```java
// TC: O(n), SC: O(h)
public TreeNode invertTree_Iterative_BFS(TreeNode root) {
    if (root == null) return null;
    // Can use ArrayDeque also, but ArrayDeque doesnt allow null values in it, so on below line in while loop,
    // if we add null values, then we get NPR. That is on line q.add(cur.left) if cur.left is null.
    // So its better to use LinkedList as a queue instead of ArrayDeque.
    Queue<TreeNode> q = new LinkedList<>();
    q.add(root);
    while (!q.isEmpty()) {
        TreeNode cur = q.poll();
        if (cur != null) {
            TreeNode left = cur.left;
            cur.left = cur.right;
            cur.right = left;
            q.add(cur.left);
            q.add(cur.right);
        }
    }
    return root;
}
```

## Common Pitfalls to Avoid

- In Queue BFS solution, remember that if you use ArrayDeque, you cannot add null values in it. Therefore use linked list.

# [Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/)

## Intro

![Untitled](1_Blind75/Tree%204691a/Untitled%204.png)

![Untitled](1_Blind75/Tree%204691a/Untitled%205.png)

## Thoughts

- Recursive solution is pretty straightforward.

## Solutions

### #1 Recursive DFS

```java
// TC: O(m * n), SC: O(n), n nodes in s, m nodes in t
public boolean isSubtree_Naive(TreeNode s, TreeNode t) {
    return s != null
            && (equals(s, t) || isSubtree_Naive(s.left, t) || isSubtree_Naive(s.right, t));
}

private boolean equals(TreeNode x, TreeNode y) {
    if (x == null && y == null) return true;
    if (x == null || y == null) return false;

    return x.val == y.val
            && equals(x.left, y.left)
            && equals(x.right, y.right);
```

### #2 KMP

```java
// TC: O(m + n), SC: O(m + n)
public boolean isSubtreea_KMP(TreeNode s, TreeNode t) {
    return kmp(serialize(s), serialize(t));
}

private boolean kmp(String s, String p) { // Check if s contains p?
    int[] lps = getLPS(p);
    int n = s.length(), m = p.length();
    for (int i = 0, j = 0; i < n; ++i) {
        while (s.charAt(i) != p.charAt(j) && j > 0) j = lps[j - 1];
        if (s.charAt(i) == p.charAt(j)) j++;
        if (j == m) return true;
    }
    return false;
}

private int[] getLPS(String p) {
    int m = p.length();
    int[] lps = new int[m];
    for (int i = 1, j = 0; i < m; ++i) {
        while (p.charAt(i) != p.charAt(j) && j > 0) j = lps[j - 1];
        if (p.charAt(i) == p.charAt(j)) lps[i] = ++j;
    }
    return lps;
}

private String serialize(TreeNode root) {
    StringBuilder sb = new StringBuilder();
    serialize(root, sb);
    return sb.toString();
}

private void serialize(TreeNode root, StringBuilder sb) {
    if (root == null) sb.append(",#");
    else {
        sb.append("," + root.val);
        serialize(root.left, sb);
        serialize(root.right, sb);
    }
```

## Common Pitfalls to Avoid

- N/A

# [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)

## Intro

![Untitled](1_Blind75/Tree%204691a/Untitled%206.png)

![Untitled](1_Blind75/Tree%204691a/Untitled%207.png)

## Thoughts

- Very easy, can be implemented using dfs or bfs.

## Solutions

### #1 DFS Recursive

```java
// TC: O(n), SC: O(n), SC is O(n) instead of O(h) coz of List<List<Integer>> res which is output that stores all n values
public List<List<Integer>> levelOrder_DFS(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();
    dfs(root, res, 0);
    return res;
}

private void dfs(TreeNode root, List<List<Integer>> res, int level) {
    if (root == null) return;

    if (res.size() < level + 1) res.add(new ArrayList<>());
    res.get(level).add(root.val);

    dfs(root.left, res, level + 1);
    dfs(root.right, res, level + 1);
}
```

### #2 DFS Iterative(Stack)

```java
// TC: O(n), SC: O(n), SC is O(n) instead of O(h) coz of List<List<Integer>> res which is output that stores all n values
public List<List<Integer>> levelOrder_DFS_Iterative(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();
    if (root == null) return res;
    dfs(root, res);
    return res;
}

private void dfs(TreeNode root, List<List<Integer>> res) {
    Stack<Pair<TreeNode, Integer>> stack = new Stack<>();
    stack.push(new Pair<>(root, 0));
    while (!stack.isEmpty()) {
        Pair<TreeNode, Integer> pair = stack.pop();
        TreeNode cur = pair.getKey();
        int level = pair.getValue();
        if (level == res.size()) res.add(new ArrayList<>());
        res.get(level).add(cur.val);
        // note right should be added to stack first before left
        if (cur.right != null) stack.push(new Pair<>(cur.right, level + 1));
        if (cur.left != null) stack.push(new Pair<>(cur.left, level + 1));
    }
}
```

### #3 BFS

```java
// TC: O(n), SC: O(n), SC is O(n) instead of O(h) coz of List<List<Integer>> res which is output that stores all n values
public List<List<Integer>> levelOrder_Iterative_BFS(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();
    if (root == null) return res;

    Queue<TreeNode> q = new LinkedList<>();
    q.add(root);
    while (!q.isEmpty()) {
        List<Integer> levelList = new ArrayList<>();
        int size = q.size();
        for (int i = 0; i < size; i++) {
            TreeNode cur = q.poll();
            levelList.add(cur.val);
            if (cur.left != null) q.add(cur.left);
            if (cur.right != null) q.add(cur.right);
        }
        res.add(levelList);
    }
    return res;
}
```

## Common Pitfalls to Avoid

- In Stack DFS, make sure to add `node.right` before `node.left` onto stack.

---
# Lowest Common Ancestor of a Binary Tree
## Intro
https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/
![[Pasted image 20220320225015.png]]
![[Pasted image 20220320225024.png]]

## Thoughts
- Find LCA on left, right and mid. If there are at least 2 then this node is the answer.

## Solutions
### #1 LC Way

```java
    // TC: O(n), SC: O(h)
    // Unnecessarily complex solution given by LC
    // On second thought, this is not that hard to understand and it also works for lca2 problem
    public TreeNode lowestCommonAncestor_LC_TooComplex_Solution(TreeNode root, TreeNode p, TreeNode q) {
        TreeNode[] ans = {null};
        helper(root, p, q, ans);
        return ans[0];
    }

    private boolean helper(TreeNode cur, TreeNode p, TreeNode q, TreeNode[] ans) {
        // if reached the end of a branch, return false
        if (cur == null) return false;

        // left recursion. If left recursion returns true, set left = 1 else 0
        int left = helper(cur.left, p, q, ans) ? 1 : 0;
        // right recursion. If left recursion returns true, set left = 1 else 0
        int right = helper(cur.right, p, q, ans) ? 1 : 0;

        int mid = (cur == p || cur == q) ? 1 : 0;

        // if any two of the flags become true
        if (mid + left + right >= 2) ans[0] = cur;

        // return true if any one of the 3 bools values is true;
        return (mid + left + right > 0);
```

### #2 My Way using booleans
```java
    TreeNode p, q, ans;

    // this uses boolean instead of numbers and stuff
    // TC: O(n), SC: O(n)
    public TreeNode lowestCommonAncestor_LC_optimized_Mine(TreeNode root, TreeNode p, TreeNode q) {
        this.p = p;
        this.q = q;
        helper(root);
        return ans;
    }

    private boolean helper(TreeNode node) {
        if (node == null) return false;

        boolean l = helper(node.left), r = helper(node.right);
        boolean m = (node == p || node == q);

        boolean[] arr = {l, r, m};
        int trueCount = 0;
        for (boolean result : arr) if (result) trueCount++;
        if (trueCount >= 2) ans = node;

        return l || r || m;
    }
```

### #3 Iterative Parent Pointers
```java
    // TC: O(n), SC: O(n)
    public TreeNode lowestCommonAncestor_Iterative_Using_Parent_Pointers(TreeNode root, TreeNode p, TreeNode q) {
        Stack<TreeNode> stack = new Stack<>();

        Map<TreeNode, TreeNode> parent = new HashMap<>();

        parent.put(root, null);
        stack.push(root);

        // iterate until we find both the nodes p and q
        while (!parent.containsKey(p) || !parent.containsKey(q)) {
            TreeNode node = stack.pop();
            if (node.left != null) {
                parent.put(node.left, node);
                stack.push(node.left);
            }
            if (node.right != null) {
                parent.put(node.right, node);
                stack.push(node.right);
            }
        }

        // ancestors set for node p
        Set<TreeNode> ancestors = new HashSet<>();

        // process all ancestors for node p using parent pointers.
        while (p != null) {
            ancestors.add(p);
            p = parent.get(p);
        }

        // The first ancestor of q which appears in p's ancestor set is their lca
        while (!ancestors.contains(q)) q = parent.get(q);

        return q;
    }
```

### #4 Recursive
```java
    // TC: O(n), SC: O(h)
    // note that the below solution wont work for lcaii problem
    public TreeNode lowestCommonAncestor_RecursiveSimple(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) return root;
        TreeNode left = lowestCommonAncestor_RecursiveSimple(root.left, p, q);
        TreeNode right = lowestCommonAncestor_RecursiveSimple(root.right, p, q);
        if (root == p || root == q) return root;
        return left == null ? right : right == null ? left : root;
    }
```

## Common Pitfalls to Avoid
- N/A

---


# Construct Binary Tree From Preorder and Inorder Traversal
## Intro
https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/
![[Pasted image 20220320225104.png]]
![[Pasted image 20220320225110.png]]

## Thoughts
- list

## Solutions
### #1 Iterative With HashMap

```java
    // TC: O(n), SC: O(n)
    public TreeNode buildTree_IterativeWithMap(int[] preorder, int[] inorder) {
        // deal with edge case(s)
        if (preorder.length == 0) return null;

        // build a map of the indices of the values as they appear in the inorder array
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) map.put(inorder[i], i);

        // initialize the stack of tree nodes
        Stack<TreeNode> stack = new Stack<>();
        TreeNode root = new TreeNode(preorder[0]);
        stack.push(root);

        // for all remaining values...
        for (int i = 1; i < preorder.length; i++) {
            // create a node
            int value = preorder[i];
            TreeNode node = new TreeNode(value);

            // the new node is on the left of the last node,
            // so it must be its left child (that's the way preorder works)
            if (map.get(value) < map.get(stack.peek().val)) stack.peek().left = node;
            else {
                // the new node is on the right of the last node,
                // so it must be the right child of either the last node
                // or one of the last node's ancestors.
                // pop the stack until we either run out of ancestors
                // or the node at the top of the stack is to the right of the new node
                TreeNode parent = null;
                while (!stack.isEmpty() && map.get(value) > map.get(stack.peek().val)) parent = stack.pop();
                parent.right = node;
            }
            stack.push(node);
        }

        return root;
    }
```

### #2 Iterative Without Map
```java
    // TC: O(n), SC: O(n)
    public TreeNode buildTree_IterativeWithoutMap(int[] preorder, int[] inorder) {
        if (preorder.length == 0) return null;
        Stack<TreeNode> s = new Stack<>();
        TreeNode root = new TreeNode(preorder[0]), cur = root;
        for (int i = 1, j = 0; i < preorder.length; i++) {
            if (cur.val != inorder[j]) {
                cur.left = new TreeNode(preorder[i]);
                s.push(cur);
                cur = cur.left;
            } else {
                j++;
                while (!s.empty() && s.peek().val == inorder[j]) {
                    cur = s.pop();
                    j++;
                }
                cur = cur.right = new TreeNode(preorder[i]);
            }
        }
        return root;
    }
```

### #3 Recursive
```java
    int preId = 0, preorder[];
    Map<Integer, Integer> inorderMap = new HashMap<>();

    // TC: O(n), SC: O(n)
    public TreeNode buildTree_Recursive(int[] preorder, int[] inorder) {
        this.preorder = preorder;

        int i = 0;
        for (int val : inorder) inorderMap.put(val, i++);
        return helper(0, inorder.length);
    }

    private TreeNode helper(int left, int right) {
        if (left == right) return null;

        int rootVal = preorder[preId++];
        TreeNode root = new TreeNode(rootVal);

        int inorderIndex = inorderMap.get(rootVal);

        root.left = helper(left, inorderIndex);
        root.right = helper(inorderIndex + 1, right);
        return root;
    }
```

### #4 Recursive Best and Short
```java
    int in, pre;

    // TC: O(n), SC: O(n)
    // SC coz of recursion stack, but no need to use HashMap like the other approach
    // todo understand how this works and how to come up with this solution
    public TreeNode buildTree_Best(int[] preorder, int[] inorder) {
        return build(preorder, inorder, Integer.MIN_VALUE);
    }

    private TreeNode build(int[] preorder, int[] inorder, int stop) {
        if (pre >= preorder.length) return null;
        if (inorder[in] == stop) {
            in++;
            return null;
        }
        TreeNode root = new TreeNode(preorder[pre++]);
        root.left = build(preorder, inorder, root.val);
        root.right = build(preorder, inorder, stop);
        return root;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Binary Tree Max Path Sum
## Intro
https://leetcode.com/problems/binary-tree-maximum-path-sum/
![[Pasted image 20220321051021.png]]
![[Pasted image 20220321051031.png]]
## Thoughts
- list

## Solutions
### #1 Brute Force

```java
    // TC: O(n^2), SC: O(n)
    public int maxPathSum_BruteForce(TreeNode node) {
        return dfs(node, new int[]{Integer.MIN_VALUE});
    }

    private int dfs(TreeNode node, int[] res) {
        if (node == null) return res[0];

        // clamp left and right values by zero, i.e., dont include them if they are less than 0
        int left = max(maxGain(node.left), 0), right = max(maxGain(node.right), 0);

        int curNodeGain = left + right + node.val;
        int leftNodeGain = dfs(node.left, res), rightNodeGain = dfs(node.right, res);
        res[0] = Collections.max(Arrays.asList(res[0], curNodeGain, leftNodeGain, rightNodeGain));

        return res[0];
    }

    private int maxGain(TreeNode node) {
        if (node == null) return 0;

        // clamp left and right values by zero, i.e., dont include them if they are less than 0
        int left = max(maxGain(node.left), 0), right = max(maxGain(node.right), 0);

        return node.val + max(left, right);
    }
```

### #2 Side Effect Using Instance Variable
```java
    int max = Integer.MIN_VALUE;

    // TC: O(n), SC: O(h)
    // TC coz we visit each node not more than 2 times.??? how?
    // https://leetcode.com/problems/binary-tree-maximum-path-sum/discuss/603423/Python-Recursion-stack-thinking-process-diagram
    // ^^ for better explanation
    public int maxPathSum(TreeNode root) {
        maxGain_Better(root);
        return max;
    }

    private int maxGain_Better(TreeNode node) {
        if (node == null) return 0;

        int leftGain = max(0, maxGain_Better(node.left));
        int rightGain = max(0, maxGain_Better(node.right));

        // have a side effect on max instance field
        max = max(max, node.val + leftGain + rightGain);


        return node.val + max(leftGain, rightGain);
    }
```

### #3 Side effect using pure method with no instance fields
```java
    // TC: O(n), SC: O(n)
    public int maxPathSum_Pure(TreeNode node) {
        return maxGain(node, new int[]{Integer.MIN_VALUE})[1];
    }

    // arr[0] -> actual maxGain, arr[1] -> answer or side effect
    private int[] maxGain(TreeNode node, int[] max) {
        if (node == null) return new int[]{0, max[0]};

        int left = max(maxGain(node.left, max)[0], 0), right = max(maxGain(node.right, max)[0], 0);

        // side effect start
        int curNodeGain = left + right + node.val;
        max[0] = Collections.max(Arrays.asList(max[0], curNodeGain));
        // side effect end

        return new int[]{node.val + max(left, right), max[0]};
    }
```

## Common Pitfalls to Avoid
- N/A

---