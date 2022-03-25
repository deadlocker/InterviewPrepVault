
# Binary Tree Level Order Traversal II
## Intro
https://leetcode.com/problems/binary-tree-level-order-traversal-ii/
![[Pasted image 20220321170316.png]]

## Thoughts
- N/A

## Solutions
### #1 BFS

```java
    // TC: O(n), SC: O(n)
    public List<List<Integer>> levelOrderBottom_BFS(TreeNode root) {
        LinkedList<List<Integer>> res = new LinkedList<>();
        if (root == null) return res;
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int size = q.size();
            List<Integer> list = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                TreeNode cur = q.poll();
                list.add(cur.val);
                if (cur.left != null) q.add(cur.left);
                if (cur.right != null) q.add(cur.right);
            }
            res.addFirst(list);
        }
        return res;
    }
```

### #2 DFS
```java
    // TC: O(n), SC: O(n)
    public List<List<Integer>> levelOrderBottom_DFS(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        dfs(root, 0, res);
        Collections.reverse(res);
        return res;
    }

    private void dfs(TreeNode node, int i, List<List<Integer>> res) {
        if (node == null) return;

        if (i == res.size()) res.add(new ArrayList<>());
        res.get(i).add(node.val);
        dfs(node.left, i + 1, res);
        dfs(node.right, i + 1, res);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Binary Tree Zigzag Level Order Traversal
## Intro
https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/
![[Pasted image 20220321170411.png]]

## Thoughts
- N/A

## Solutions
### #1 BFS Reverse List

```java
    // TC: O(n), SC: O(n)
    public List<List<Integer>> zigzagLevelOrder_BFS_ReverseList(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int size = q.size();
            List<Integer> list = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                TreeNode cur = q.poll();
                list.add(cur.val);
                if (cur.left != null) q.offer(cur.left);
                if (cur.right != null) q.offer(cur.right);
            }
            if (res.size() % 2 == 1) Collections.reverse(list);
            res.add(list);
        }
        return res;
    }
```

### #2 BFS Use Linked List
```java
    // TC: O(n), SC: O(n)
    public List<List<Integer>> zigzagLevelOrder_BFS_Use_LinkedList(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int size = q.size();
            LinkedList<Integer> list = new LinkedList<>();
            for (int i = 0; i < size; i++) {
                TreeNode cur = q.poll();
                if (cur.left != null) q.offer(cur.left);
                if (cur.right != null) q.offer(cur.right);

                if (res.size() % 2 == 0) list.addLast(cur.val);
                else list.addFirst(cur.val);
            }
            res.add(list);
        }
        return res;
    }
```

### #3 BFS Sentinel
```java
    // TC: O(n), SC: O(n)
    public List<List<Integer>> zigzagLevelOrder_LC_BFS_Sentinel(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;

        LinkedList<TreeNode> q = new LinkedList<>();
        q.addLast(root);
        q.addLast(null);

        LinkedList<Integer> list = new LinkedList<>();

        while (!q.isEmpty()) {
            TreeNode cur = q.pollFirst();
            if (cur != null) {
                if (res.size() % 2 == 0) list.addLast(cur.val);
                else list.addFirst(cur.val);

                if (cur.left != null) q.addLast(cur.left);
                if (cur.right != null) q.addLast(cur.right);
            } else {
                res.add(list);
                list = new LinkedList<>();
                if (!q.isEmpty()) q.addLast(null);
            }
        }
        return res;
    }
```

### #4 DFS
```java
    // TC: O(n), SC: O(h), h is height of tree
    public List<List<Integer>> zigzagLevelOrder_DFS(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        dfs(root, 0, res);
        return res;
    }

    private void dfs(TreeNode cur, int level, List<List<Integer>> res) {
        if (level >= res.size()) res.add(new ArrayList<>(Collections.singletonList(cur.val)));
        else {
            if (level % 2 == 0) res.get(level).add(cur.val);
            else res.get(level).add(0, cur.val);
        }

        if (cur.left != null) dfs(cur.left, level + 1, res);
        if (cur.right != null) dfs(cur.right, level + 1, res);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Average of Levels in Binary Tree
## Intro
https://leetcode.com/problems/average-of-levels-in-binary-tree/
![[Pasted image 20220321170605.png]]
![[Pasted image 20220321170611.png]]

## Thoughts
- N/A

## Solutions
### #1 BFS

```java
    // TC: O(n), SC: O(n)
    public List<Double> averageOfLevels_BFS(TreeNode root) {
        List<Double> res = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>();
        q.add(root);
        while (!q.isEmpty()) {
            int size = q.size();
            double sum = 0;
            for (int i = 0; i < size; i++) {
                TreeNode cur = q.poll();
                sum += cur.val;
                if (cur.left != null) q.add(cur.left);
                if (cur.right != null) q.add(cur.right);
            }
            res.add(sum / size);
        }
        return res;
    }
```

### #2
```java
    // TC: O(n), SC: O(n)
    public List<Double> averageOfLevels_DFS(TreeNode root) {
        List<Double> res = new ArrayList<>();
        List<Integer> count = new ArrayList<>();
        average(root, 0, res, count);
        for (int i = 0; i < res.size(); i++) res.set(i, res.get(i) / count.get(i));
        return res;
    }

    private void average(TreeNode root, int i, List<Double> sum, List<Integer> count) {
        if (root == null) return;
        if (i < sum.size()) {
            sum.set(i, sum.get(i) + root.val);
            count.set(i, count.get(i) + 1);
        } else {
            sum.add(1.0 * root.val);
            count.add(1);
        }

        average(root.left, i + 1, sum, count);
        average(root.right, i + 1, sum, count);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Minimum Depth of Binary Tree
## Intro
https://leetcode.com/problems/minimum-depth-of-binary-tree/
![[Pasted image 20220321170735.png]]

## Thoughts
- N/A

## Solutions
### #1 DFS

```java
    // TC: O(n), SC: O(h)
    public int minDepth_DFS(TreeNode node) {
        if (node == null) return 0;
        if (node.left == null && node.right == null) return 1;

        int left = 1 + minDepth_DFS(node.left), right = 1 + minDepth_DFS(node.right);
        if (node.left == null) return right;
        if (node.right == null) return left;

        return min(left, right);
    }
```

### #2 DFS Iterative
```java
    // TC: O(n), SC: O(n)
    public int minDepth_DFS_Iterative(TreeNode node) {
        if (node == null) return 0;

        Stack<Pair<TreeNode, Integer>> stack = new Stack<>();
        stack.add(new Pair<>(node, 1));
        int ans = Integer.MAX_VALUE;
        while (!stack.isEmpty()) {
            Pair<TreeNode, Integer> pair = stack.pop();
            TreeNode cur = pair.getKey();
            int val = pair.getValue();

            Pair<TreeNode, Integer> left = new Pair<>(cur.left, 1 + val), right = new Pair<>(cur.right, 1 + val);

            if (cur.left == null && cur.right == null) ans = min(ans, val);
            else if (cur.left == null) stack.add(right);
            else if (cur.right == null) stack.add(left);
            else {
                stack.add(left);
                stack.add(right);
            }
        }
        return ans;
    }
```

### #3 BFS
```java
    // TC: O(n), SC: O(n)
    public int minDepth_BFS(TreeNode root) {
        if (root == null) return 0;

        Queue<Pair<TreeNode, Integer>> q = new LinkedList<>();
        q.add(new Pair<>(root, 1));
        while (!q.isEmpty()) {
            Pair<TreeNode, Integer> pair = q.poll();
            TreeNode cur = pair.getKey();
            int val = pair.getValue();
            Pair<TreeNode, Integer> left = new Pair<>(cur.left, 1 + val), right = new Pair<>(cur.right, 1 + val);
            if (cur.left == null && cur.right == null) return val;
            if (cur.left == null) q.add(right);
            else if (cur.right == null) q.add(left);
            else {
                q.add(left);
                q.add(right);
            }
        }
        return 0;
    }
```

### #4 BFS Simpler
```java
    // TC: O(n), SC: O(n)
    public int minDepth_BFS_Simpler(TreeNode root) {
        if (root == null) return 0;

        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        int level = 1;
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                TreeNode cur = q.poll();
                if (cur.left == null && cur.right == null) return level;
                if (cur.left != null) q.offer(cur.left);
                if (cur.right != null) q.offer(cur.right);
            }
            level++;
        }

        return level;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Level Order Successor(Grokking)
## Intro
![[Pasted image 20220321170945.png]]
![[Pasted image 20220321170953.png]]
## Thoughts
- N/A

## Solutions
### #1 BFS

```java
    // TC: O(n), SC: O(n)
    public static TreeNode findSuccessor(TreeNode root, int key) {
        if (root == null)
            return null;

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            TreeNode currentNode = queue.poll();
            // insert the children of current node in the queue
            if (currentNode.left != null)
                queue.offer(currentNode.left);
            if (currentNode.right != null)
                queue.offer(currentNode.right);

            // break if we have found the key
            if (currentNode.val == key)
                break;
        }

        return queue.peek();
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Populating Next Right Pointers in Each Node
## Intro
https://leetcode.com/problems/populating-next-right-pointers-in-each-node/
![[Pasted image 20220321171117.png]]
![[Pasted image 20220321171123.png]]

## Thoughts
- N/A

## Solutions
### #1 BFS

```java

    // TC: O(n), SC: O(n)
    public Node connect_BFS(Node root) {
        if (root == null) return null;

        Queue<Node> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int size = q.size();
            Node prev = new Node();
            for (int i = 0; i < size; i++) {
                Node node = q.poll();
                prev.next = node;
                prev = node;

                // or you can do the below instead of using prev variable also
//                if (i < size - 1) {
//                    node.next = Q.peek();
//                }

                if (node.left != null) q.offer(node.left);
                if (node.right != null) q.offer(node.right);
            }
        }
        return root;
    }

    @NoArgsConstructor
    static class Node {
        public int val;
        public Node left;
        public Node right;
        public Node next;
    }
```

### #2 Use Previously Established Pointers
```java
    // TC: O(n), SC: O(1)
    // TODO: understand this again. Very genius solution
    public Node connect_UsePreviouslyEstablishedPointers(Node root) {
        if (root == null) return null;

        Node leftMost = root;

        while (leftMost.left != null) {
            Node cur = leftMost;

            while (cur != null) {
                cur.left.next = cur.right;
                if (cur.next != null) cur.right.next = cur.next.left;

                cur = cur.next;
            }

            leftMost = leftMost.left;
        }
        return root;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Populating Next Right Pointers in Each Node II
## Intro
https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/
![[Pasted image 20220321171235.png]]
![[Pasted image 20220321171244.png]]

## Thoughts
- N/A

## Solutions
### #1 BFS

```java
    // TC: O(n), SC: O(n)
    public Node connect_BFS(Node root) {
        if (root == null) return null;

        Queue<Node> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int size = q.size();
            Node prev = new Node();
            for (int i = 0; i < size; i++) {
                Node cur = q.poll();
                prev.next = cur;
                prev = cur;
                if (cur.left != null) q.offer(cur.left);
                if (cur.right != null) q.offer(cur.right);
            }
        }

        return root;
    }

    static class Node {
        public int val;
        public Node left;
        public Node right;
        public Node next;

        public Node() {
        }

        public Node(int _val) {
            val = _val;
        }

        public Node(int _val, Node _left, Node _right, Node _next) {
            val = _val;
            left = _left;
            right = _right;
            next = _next;
        }
    }
```

### #2 DFS Clever
```java
    Node prev, leftMost;

    // TC: O(n), SC: O(1)
    // TODO: understand this again. Very genius solution
    public Node connect(Node root) {
        leftMost = root;
        while (leftMost != null) {
            Node cur = leftMost;
            prev = leftMost = null;
            while (cur != null) {
                connectToPrev(cur.left);
                connectToPrev(cur.right);
                cur = cur.next;
            }
        }
        return root;
    }

    private void connectToPrev(Node cur) {
        if (cur != null) {
            if (prev == null) leftMost = cur;
            else prev.next = cur;
            prev = cur;
        }
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Binary Tree Right Side View
## Intro
https://leetcode.com/problems/binary-tree-right-side-view/

## Thoughts
- N/A

## Solutions
### #1 BFS

```java
    // TC: O(n), SC: O(d), d is diameter of tree. max value could be N/2 for last level of complete binary tree
    public List<Integer> rightSideView_BFS(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;

        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                TreeNode cur = q.poll();
                if (i == size - 1) res.add(cur.val);
                if (cur.left != null) q.offer(cur.left);
                if (cur.right != null) q.offer(cur.right);
            }
        }
        return res;
    }
```

### #2
```java
    // TC: O(n), SC: O(h), h is height of tree and it can vary from logn to n
    public List<Integer> rightSideView_DFS(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        dfs(root, res, 0);
        return res;
    }

    private void dfs(TreeNode node, List<Integer> res, int level) {
        if (level == res.size()) res.add(node.val);

        if (node.right != null) dfs(node.right, res, level + 1);
        if (node.left != null) dfs(node.left, res, level + 1);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Path Sum
## Intro
https://leetcode.com/problems/path-sum/
![[Pasted image 20220321171503.png]]
![[Pasted image 20220321171509.png]]

## Thoughts
- N/A

## Solutions
### #1 DFS

```java
    // TC: O(n), SC: O(n)
    public boolean hasPathSum_DFS_Better(TreeNode root, int targetSum) {
        if (root == null) return false;

        targetSum -= root.val;
        if (root.left == null && root.right == null) return targetSum == 0;
        return hasPathSum_DFS_Better(root.left, targetSum) || hasPathSum_DFS_Better(root.right, targetSum);
    }

    // TC: O(n), SC: O(n)
    public boolean hasPathSum_DFS_MyWay(TreeNode root, int targetSum) {
        if (root == null) return targetSum == 0;
        return helper(root, targetSum);
    }

    private boolean helper(TreeNode node, int diffSum) {
        if (node.left == null && node.right == null) return diffSum == node.val;

        boolean left = false, right = false;
        if (node.left != null) left = helper(node.left, diffSum - node.val);
        if (node.right != null) right = helper(node.right, diffSum - node.val);
        return left || right;
    }
```

### #2 DFS Iterative
```java
    // TC: O(n), SC: O(n)
    public boolean hasPathSum_DFS_Iterative(TreeNode root, int targetSum) {
        if (root == null) return false;

        Stack<Pair<TreeNode, Integer>> stack = new Stack<>();
        stack.push(new Pair<>(root, targetSum - root.val));

        while (!stack.isEmpty()) {
            Pair<TreeNode, Integer> pair = stack.pop();
            TreeNode cur = pair.getKey();
            int diffSum = pair.getValue();

            if (cur.left == null && cur.right == null && diffSum == 0) return true;

            if (cur.left != null) stack.push(new Pair<>(cur.left, diffSum - cur.left.val));
            if (cur.right != null) stack.push(new Pair<>(cur.right, diffSum - cur.right.val));
        }
        return false;
    }

    // TC: O(n), SC: O(n)
    public boolean hasPathSum_DFS_Iterative_Another(TreeNode root, int target) {
        if (root == null) return false;

        Stack<Pair<TreeNode, Integer>> stack = new Stack<>();
        stack.push(new Pair<>(root, target));
        while (!stack.isEmpty()) {
            Pair<TreeNode, Integer> pair = stack.pop();
            TreeNode node = pair.getKey();
            int t = pair.getValue();
            if (node.left == null && node.right == null && node.val == t) return true;
            if (node.left != null) stack.push(new Pair<>(node.left, t - node.val));
            if (node.right != null) stack.push(new Pair<>(node.right, t - node.val));
        }
        return false;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Path Sum II
## Intro
https://leetcode.com/problems/path-sum-ii/
![[Pasted image 20220321171624.png]]
![[Pasted image 20220321171630.png]]

## Thoughts
- N/A

## Solutions
### #1 DFS LC

```java
    // TC: O(n^2), SC: O(n)
    public List<List<Integer>> pathSum_DFS_LC_Way(TreeNode root, int sum) {
        List<List<Integer>> res = new ArrayList<>();
        dfs_LC(root, sum, new ArrayList<>(), res);
        return res;
    }

    private void dfs_LC(TreeNode cur, int target, List<Integer> list, List<List<Integer>> res) {

        if (cur == null) return;

        // Add the current cur to the path's list
        list.add(cur.val);

        // Check if the current cur is a leaf and also, if it
        // equals our remaining sum. If it does, we add the path to
        // our list of paths
        if (cur.left == null && cur.right == null && cur.val == target) res.add(new ArrayList<>(list));
        else {
            // Else, we will recurse on the left and the right children
            this.dfs_LC(cur.left, target - cur.val, list, res);
            this.dfs_LC(cur.right, target - cur.val, list, res);
        }

        // We need to pop the cur once we are done processing ALL of it's subtrees.
        list.remove(list.size() - 1);
    }
```

### #2 DFS Mine
```java
    // TC: O(n^2), SC: O(n), TC of n^2 coz of copying and making newList from list
    public List<List<Integer>> pathSum_DFS_Mine(TreeNode root, int targetSum) {
        List<List<Integer>> res = new ArrayList<>();
        dfs(root, targetSum, res, new ArrayList<>());
        return res;
    }

    private void dfs(TreeNode node, int target, List<List<Integer>> res, List<Integer> list) {
        if (node == null) return;

        if (node.left == null && node.right == null && target == node.val) {
            List<Integer> newList = new ArrayList<>(list);
            newList.add(node.val);
            res.add(newList);
            return;
        }

        list.add(node.val);
        dfs(node.left, target - node.val, res, list);
        dfs(node.right, target - node.val, res, list);
        list.remove(list.size() - 1);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Sum Root to Leaf Numbers
## Intro
https://leetcode.com/problems/sum-root-to-leaf-numbers/
![[Pasted image 20220321171846.png]]
![[Pasted image 20220321171853.png]]

## Thoughts
- N/A

## Solutions
### #1 DFS Mine

```java
    // TC: O(n), SC: O(h)
    public int sumNumbers_DFS_Recursive_Mine(TreeNode root) {
        return dfs(root, 0);
    }

    private int dfs(TreeNode node, int prevNum) {
        if (node == null) return 0;

        prevNum = prevNum * 10 + node.val;
        if (node.left == null && node.right == null) return prevNum;

        return dfs(node.left, prevNum) + dfs(node.right, prevNum);
    }
```

### #2 DFS LC Way
```java
    // TC: O(n), SC: O(h)
    public int sumNumbers_DFS_Recursive_LC_Way(TreeNode root) {
        preorder(root, 0);
        return sum;
    }

    public void preorder(TreeNode cur, int currNumber) {
        if (cur != null) {
            currNumber = currNumber * 10 + cur.val;
            // if it's a leaf, update root-to-leaf sum
            if (cur.left == null && cur.right == null) sum += currNumber;
            preorder(cur.left, currNumber);
            preorder(cur.right, currNumber);
        }
    }
```

### #3 DFS Iterative
```java
    int sum = 0;

    // TC: O(n), SC: O(h)
    public int sumNumbers_DFS_Iterative_Mine(TreeNode root) {
        if (root == null) return 0;

        Stack<Pair<TreeNode, Integer>> stack = new Stack<>();
        stack.push(new Pair<>(root, 0));

        int sum = 0;
        while (!stack.isEmpty()) {
            Pair<TreeNode, Integer> pair = stack.pop();
            TreeNode node = pair.getKey();
            int prevNum = pair.getValue();
            prevNum = prevNum * 10 + node.val;
            if (node.left == null && node.right == null) sum += prevNum;

            if (node.left != null) stack.push(new Pair<>(node.left, prevNum));
            if (node.right != null) stack.push(new Pair<>(node.right, prevNum));
        }

        return sum;
    }
```

### #4 Morris Traversal
```java
    // TC: O(n), SC: O(1)
    // todo how the heck does this work?
    public int sumNumbers_MorrisTraversal(TreeNode root) {
        int sum = 0, currNumber = 0, steps;
        TreeNode predecessor;

        while (root != null) {
            // If there is a left child, then compute the predecessor.
            // If there is no link predecessor.right = root --> set it.
            // If there is a link predecessor.right = root --> break it.
            if (root.left != null) {
                // Predecessor node is one step to the left and then to the right till you can.
                predecessor = root.left;
                steps = 1;
                while (predecessor.right != null && predecessor.right != root) {
                    predecessor = predecessor.right;
                    ++steps;
                }

                // Set link predecessor.right = root
                // and go to explore the left subtree
                if (predecessor.right == null) {
                    currNumber = currNumber * 10 + root.val;
                    predecessor.right = root;
                    root = root.left;
                }
                // Break the link predecessor.right = root
                // Once the link is broken, it's time to change subtree and go to the right
                else {
                    // If you're on the leaf, update the sum
                    if (predecessor.left == null) sum += currNumber;
                    // This part of tree is explored, backtrack
                    for (int i = 0; i < steps; ++i) currNumber /= 10;
                    predecessor.right = null;
                    root = root.right;
                }
            }

            // If there is no left child then just go right.
            else {
                currNumber = currNumber * 10 + root.val;
                // if you're on the leaf, update the sum
                if (root.right == null) sum += currNumber;
                root = root.right;
            }
        }
        return sum;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Path With Given Sequence(Grokking)
## Intro
![[Pasted image 20220321171922.png]]
## Thoughts
- N/A

## Solutions
### #1 DFS

```java
    // TC: O(n), SC: O(h)
    public static boolean findPath(TreeNode root, int[] arr) {
        if (root == null) return arr.length == 0;
        return dfs(root, arr, 0);
    }

    private static boolean dfs(TreeNode node, int[] arr, int i) {
        if (node == null) return false;

        if (i >= arr.length || arr[i] != node.val) return false;

        if (node.left == null && node.right == null && i == arr.length - 1) return true;

        return dfs(node.left, arr, i + 1) || dfs(node.right, arr, i + 1);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Path Sum III
## Intro
https://leetcode.com/problems/path-sum-iii/
![[Pasted image 20220321172012.png]]
## Thoughts
- N/A

## Solutions
### #1 DFS Grokking Way

```java
    // TC: O(n^2), SC: O(n)
    public int pathSum_Grokking_Approach(TreeNode root, int sum) {
        return dfs(root, sum, new ArrayList<>());
    }

    private int dfs(TreeNode node, int sum, List<Integer> curPath) {
        if (node == null) return 0;

        curPath.add(node.val);

        int pathCount = 0, pathSum = 0;

        for (int i = curPath.size() - 1; i >= 0; i--) {
            pathSum += curPath.get(i);
            if (pathSum == sum) pathCount++;
        }

        pathCount += dfs(node.left, sum, curPath) + dfs(node.right, sum, curPath);

        curPath.remove(curPath.size() - 1);

        return pathCount;
    }
```

### #2 Brute Force
```java
    // TC: O(n^2), SC: O(n)
    public int pathSum_BruteForce(TreeNode node, int sum) {
        if (node == null) return 0;

        return pathSumFromNode(node, sum) + pathSum_BruteForce(node.left, sum) + pathSum_BruteForce(node.right, sum);
    }

    private int pathSumFromNode(TreeNode node, int sum) {
        if (node == null) return 0;

        return (node.val == sum ? 1 : 0)
                + pathSumFromNode(node.left, sum - node.val)
                + pathSumFromNode(node.right, sum - node.val);
    }
```

### #3 DFS Preorder
```java

    int count = 0, k = 0;
    Map<Integer, Integer> map = new HashMap<>();

    // todo understand how this works
    // TC: O(n), SC: O(n)
    public int pathSum_PrefixSum_Preorder_DFS(TreeNode root, int sum) {
        this.k = sum;
        preorder(root, 0);
        return count;
    }

    private void preorder(TreeNode node, int curSum) {
        if (node == null) return;

        // current prefix sum
        curSum += node.val;

        // here is the sum we are looking for
        if (curSum == k) count++;

        // no.of times the curSum - k has occured already, determines the no.of times
        // a path with sum k has occured upto the current node
        count += map.getOrDefault(curSum - k, 0);

        // add curSum into hashmap to use it during the child nodes processing
        map.put(curSum, map.getOrDefault(curSum, 0) + 1);

        preorder(node.left, curSum);
        preorder(node.right, curSum);

        // remove curSum from the map in order not to use it during the
        // parallel subtree processing
        map.put(curSum, map.get(curSum) - 1);
    }
```

### #4 DFS Preorder Pure
```java
    // TC: O(n), SC: O(n)
    public int pathSum_PrefixSum_Preorder_Pure(TreeNode node, int sum) {
        return dfs(node, sum, 0, new HashMap<>());
    }

    private int dfs(TreeNode node, int k, int curSum, Map<Integer, Integer> map) {
        if (node == null) return 0;

        int count = 0;
        curSum += node.val;

        if (curSum == k) count++;

        count += map.getOrDefault(curSum - k, 0);

        map.put(curSum, map.getOrDefault(curSum, 0) + 1);

        count += dfs(node.left, k, curSum, map);
        count += dfs(node.right, k, curSum, map);

        map.put(curSum, map.get(curSum) - 1);
        return count;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Diameter of binary tree
## Intro
https://leetcode.com/problems/diameter-of-binary-tree/
![[Pasted image 20220321172206.png]]
## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java
    // TC: O(n^2), SC: O(n^2)
    public int diameterOfBinaryTree_BruteForce(TreeNode node) {
        if (node == null) return 0;

        int left = height(node.left), right = height(node.right);

        int childDiameter = max(diameterOfBinaryTree(node.left), diameterOfBinaryTree(node.right));

        return max(left + right, childDiameter);

    }

    // TC: O(n), SC: O(n)
    private int height(TreeNode node) {
        if (node == null) return 0;

        return 1 + max(height(node.left), height(node.right));
    }
```

### #2 DFS Better
```java
    private int max = 0;

    // TC: O(n), SC: O(n)
    public int diameterOfBinaryTree(TreeNode root) {
        maxDepth(root);
        return max;
    }

    private int maxDepth(TreeNode node) {
        if (node == null) return 0;

        int left = maxDepth(node.left), right = maxDepth(node.right);

        // side effect
        max = max(max, left + right);

        return 1 + max(left, right);
    }
```

### #3 Iterative DFS
```java
    // TC: O(n), SC: O(n)
    public int diameterOfBinaryTree_Iterative(TreeNode root) {
        if (root == null) return 0;

        int max = 0;
        Stack<TreeNode> stack = new Stack<>();
        Map<TreeNode, Integer> nodePathCountMap = new HashMap<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.peek();
            if (node.left != null && !nodePathCountMap.containsKey(node.left)) stack.push(node.left);
            else if (node.right != null && !nodePathCountMap.containsKey(node.right)) stack.push(node.right);
            else {
                TreeNode rootNodeEndOfPostOrder = stack.pop();
                int leftMax = nodePathCountMap.getOrDefault(rootNodeEndOfPostOrder.left, 0);
                int rightMax = nodePathCountMap.getOrDefault(rootNodeEndOfPostOrder.right, 0);
                nodePathCountMap.put(rootNodeEndOfPostOrder, 1 + Math.max(leftMax, rightMax));
                max = Math.max(max, leftMax + rightMax);
            }
        }
        return max;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Serialize and Deserialize Binary Tree
## Intro
https://leetcode.com/problems/serialize-and-deserialize-binary-tree/
![[Pasted image 20220322111832.png]]
## Thoughts
- N/A

## Solutions
### #1 DFS
```java
    // Encodes a tree to a single string.
    // TC: O(n), SC: O(n)
    public String serialize_DFS(TreeNode root) {
        return rserialize(root, "");
    }

    private String rserialize(TreeNode root, String s) {
        if (root == null) s += "null,";
        else {
            s += root.val + ",";
            s = rserialize(root.left, s);
            s = rserialize(root.right, s);
        }
        return s;
    }

    // Decodes your encoded data to tree.
    // TC: O(n), SC: O(n)
    public TreeNode deserialize_DFS(String data) {
        String arr[] = data.split(",");
        List<String> list = new LinkedList<>(Arrays.asList(arr));
        return rdeserialize(list);
    }

    private TreeNode rdeserialize(List<String> l) {
        if (l.get(0).equals("null")) {
            l.remove(0);
            return null;
        }

        TreeNode root = new TreeNode(Integer.parseInt(l.get(0)));
        l.remove(0);
        root.left = rdeserialize(l);
        root.right = rdeserialize(l);
        return root;
    }
```

### #2 BFS
```java
    // Encodes a tree to a single string.
    // TC: O(n), SC: O(n)
    public String serialize_BFS(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);

        while (!q.isEmpty()) {
            TreeNode first = q.poll();
            sb.append(first != null ? first.val : "null").append(",");
            if (first != null) {
                q.add(first.left);
                q.add(first.right);
            }
        }

        return sb.toString();
    }

    // Decodes your encoded data to tree.
    // TC: O(n), SC: O(n)
    public TreeNode deserialize_BFS(String data) {
        String[] nodes = data.split(",");
        Queue<TreeNode> q = new LinkedList<>();
        TreeNode root = getTreeNode(nodes[0]); //it will always have one value atleast
        q.add(root);
        int i = 1;

        while (!q.isEmpty()) {
            TreeNode first = q.poll();
            if (first != null) {
                first.left = getTreeNode(nodes[i++]);
                first.right = getTreeNode(nodes[i++]);

                if (first.left != null) q.add(first.left);
                if (first.right != null) q.add(first.right);
            }
        }
        return root;
    }

    private TreeNode getTreeNode(String val) {
        if (val.equals("null")) return null;
        return new TreeNode(Integer.parseInt(val));
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Merge Two Binary Tree
## Intro
https://leetcode.com/problems/merge-two-binary-trees/
![[Pasted image 20220322111919.png]]

## Thoughts
- N/A

## Solutions
### #1 DFS Recursive
```java
    // TC: O(n), SC: O(n)
    public TreeNode mergeTrees_DFS_Recursive(TreeNode t1, TreeNode t2) {
        if (t1 == null) return t2;
        if (t2 == null) return t1;

        t1.val += t2.val;
        t1.left = mergeTrees_DFS_Recursive(t1.left, t2.left);
        t1.right = mergeTrees_DFS_Recursive(t1.right, t2.right);
        return t1;
    }
```

### #2 DFS Iterative
```java
    // TC: O(n), SC: O(n)
    public TreeNode mergeTrees_DFS_Iterative(TreeNode t1, TreeNode t2) {
        if (t1 == null) return t2;
        Stack<Pair<TreeNode, TreeNode>> stack = new Stack<>();
        stack.push(new Pair<>(t1, t2));
        while (!stack.isEmpty()) {
            Pair<TreeNode, TreeNode> pair = stack.pop();
            TreeNode c1 = pair.getKey(), c2 = pair.getValue();
            if (c1 == null || c2 == null) continue;
            c1.val += c2.val;
            if (c1.left == null) c1.left = c2.left;
            else stack.push(new Pair<>(c1.left, c2.left));

            if (c1.right == null) c1.right = c2.right;
            else stack.push(new Pair<>(c1.right, c2.right));
        }
        return t1;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Maximum Binary Tree
## Intro
https://leetcode.com/problems/maximum-binary-tree/
![[Pasted image 20220322112007.png]]
![[Pasted image 20220322112014.png]]
![[Pasted image 20220322112019.png]]

## Thoughts
- N/A

## Solutions
### #1 DFS LC Solution
```java
    // TC: O(n^2), SC: O(n)
    public TreeNode constructMaximumBinaryTree_DFS_LC_Solution(int[] nums) {
        return construct(nums, 0, nums.length);
    }

    private TreeNode construct(int[] nums, int l, int r) {
        if (l == r) return null;

        int maxIndex = max(nums, l, r);
        TreeNode node = new TreeNode(nums[maxIndex]);
        node.left = construct(nums, l, maxIndex);
        node.right = construct(nums, maxIndex + 1, r);
        return node;
    }

    private int max(int[] nums, int l, int r) {
        int maxIndex = l;
        for (int i = l; i < r; i++) {
            if (nums[maxIndex] < nums[i]) maxIndex = i;
        }
        return maxIndex;
    }
```

### #2 DFS Mine
```java
    // TC: O(n^2), SC: O(n)
    public TreeNode constructMaximumBinaryTree_DFS_MINE(int[] nums) {
        return helper(nums, 0, nums.length - 1);
    }

    private TreeNode helper(int[] nums, int start, int end) {
        TreeNode node = null;
        int maxIndex = -1, max = Integer.MIN_VALUE;
        for (int i = start; i <= end; i++) {
            if (nums[i] > max) {
                max = nums[i];
                maxIndex = i;
            }
        }
        if (max != Integer.MIN_VALUE) {
            node = new TreeNode(max);
            node.left = helper(nums, start, maxIndex - 1);
            node.right = helper(nums, maxIndex + 1, end);
        }
        return node;
    }
```

### #3 Stack Iterative
```java
    public TreeNode constructMaximumBinaryTree_Stack_Optimized(int[] nums) {
        Deque<TreeNode> stack = new LinkedList<>();
        for (int num : nums) {
            TreeNode cur = new TreeNode(num);
            while (!stack.isEmpty() && stack.peek().val < cur.val) cur.left = stack.pop();

            if (!stack.isEmpty()) stack.peek().right = cur;
            stack.push(cur);
        }
        return stack.isEmpty() ? null : stack.removeLast();
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Maximum Width Of Binary Tree
## Intro
https://leetcode.com/problems/maximum-width-of-binary-tree/
![[Pasted image 20220322112124.png]]
![[Pasted image 20220322112132.png]]
![[Pasted image 20220322112137.png]]
## Thoughts
- N/A

## Solutions
### #1 BFS
```java
    // TC: O(n), SC: O(n)
    public int widthOfBinaryTree_BFS(TreeNode root) {
        if (root == null) return 0;

        // [node, columnIndex]
        Queue<Pair<TreeNode, Integer>> q = new LinkedList<>();
        q.offer(new Pair<>(root, 0));
        int maxWidth = 0;
        while (!q.isEmpty()) {
            Pair<TreeNode, Integer> head = q.peek();

            int size = q.size();
            Pair<TreeNode, Integer> curPair = null;
            for (int i = 0; i < size; i++) {
                curPair = q.poll();
                TreeNode node = curPair.getKey();
                if (node.left != null) q.offer(new Pair<>(node.left, 2 * curPair.getValue()));
                if (node.right != null) q.offer(new Pair<>(node.right, 2 * curPair.getValue() + 1));
            }

            maxWidth = Math.max(maxWidth, curPair.getValue() - head.getValue() + 1);
        }
        return maxWidth;
    }
```

### #2 DFS
```java
    int maxWidth = 0;
    Map<Integer, Integer> firstColIndexTable = new HashMap<>();

    // todo understand how this works
    // TC: O(n), SC: O(n)
    public int widthOfBinaryTree_DFS(TreeNode root) {
        dfs(root, 0, 0);
        return this.maxWidth;
    }

    private void dfs(TreeNode node, int depth, int colIndex) {
        if (node == null) return;

        if (!firstColIndexTable.containsKey(depth)) firstColIndexTable.put(depth, colIndex);

        int firstColIndex = firstColIndexTable.get(depth);

        maxWidth = Math.max(maxWidth, colIndex - firstColIndex + 1);

        // preorder dfs, note: its important to put the priority on the left child
        dfs(node.left, depth + 1, 2 * colIndex);
        dfs(node.right, depth + 1, 2 * colIndex + 1);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# All Nodes Distance K In Binary Tree
## Intro
https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/
![[Pasted image 20220322112229.png]]

## Thoughts
- N/A

## Solutions
### #1 BFS LC
```java
    // todo understand how this works, especially why are we adding null to q
    // TC: O(n), SC: O(n)
    public List<Integer> distanceK_BFS_LC_Solution(TreeNode root, TreeNode target, int k) {
        Map<TreeNode, TreeNode> parent = new HashMap<>();
        dfs(root, null, parent);
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(null);
        q.offer(target);

        Set<TreeNode> seen = new HashSet<>();
        seen.add(null);
        seen.add(target);

        int dist = 0;
        while (!q.isEmpty()) {
            TreeNode cur = q.poll();
            if (cur == null) {
                if (dist == k) {
                    List<Integer> res = new ArrayList<>();
                    for (TreeNode n : q) res.add(n.val);
                    return res;
                }
                q.offer(null);
                dist++;
            } else {
                if (!seen.contains(cur.left)) {
                    seen.add(cur.left);
                    q.offer(cur.left);
                }
                if (!seen.contains(cur.right)) {
                    seen.add(cur.right);
                    q.offer(cur.right);
                }
                TreeNode par = parent.get(cur);
                if (!seen.contains(par)) {
                    seen.add(par);
                    q.offer(par);
                }
            }
        }
        return new ArrayList<>();
    }

    private void dfs(TreeNode node, TreeNode par, Map<TreeNode, TreeNode> parent) {
        if (node != null) {
            parent.put(node, par);
            dfs(node.left, node, parent);
            dfs(node.right, node, parent);
        }
    }
```

### #2 DFS LC
```java
    List<Integer> res = new ArrayList<>();
    TreeNode target;
    int k;

    // TC: O(n), SC: O(n)
    // todo understand how this works
    public List<Integer> distanceK_DFS_LC_Solution(TreeNode root, TreeNode target, int k) {
        this.target = target;
        this.k = k;
        dfs(root);
        return res;
    }

    // return vertex distance from node to target if exists, else -1
    // vertex distance: the number of vertices on the path from node to target
    private int dfs(TreeNode cur) {
        if (cur == null) return -1;
        else if (cur == target) {
            subtreeAdd(cur, 0);
            return 1;
        } else {
            int L = dfs(cur.left), R = dfs(cur.right);
            if (L != -1) {
                if (L == k) res.add(cur.val);
                subtreeAdd(cur.right, L + 1);
                return L + 1;
            } else if (R != -1) {
                if (R == k) res.add(cur.val);
                subtreeAdd(cur.left, R + 1);
                return R + 1;
            } else return -1;
        }
    }

    // add all nodes 'K - dist' from the target node to res
    // i.e., all nodes that are 'K - dist' away from target node will be added to res
    private void subtreeAdd(TreeNode cur, int dist) {
        if (cur == null) return;

        if (dist == k) res.add(cur.val);
        else {
            subtreeAdd(cur.left, dist + 1);
            subtreeAdd(cur.right, dist + 1);
        }
    }
```

### #3 BFS Mine
```java
    // TC: O(n), SC: O(n)
    public List<Integer> distanceK_Mine(TreeNode root, TreeNode target, int k) {
        if (k == 0) return List.of(target.val);
        Map<Integer, List<Integer>> graph = new HashMap<>();
        dfs_buildGraph(root, null, graph);

        Set<Integer> seen = new HashSet<>();
        int steps = 0;
        Queue<Integer> q = new LinkedList<>();
        q.offer(target.val);
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int v = q.poll();
                seen.add(v);
                for (int neighbor : graph.get(v)) if (!seen.contains(neighbor)) q.offer(neighbor);
            }
            if (++steps == k) return new ArrayList<>(q);
        }

        return Collections.emptyList();
    }

    private void dfs_buildGraph(TreeNode root, TreeNode parent, Map<Integer, List<Integer>> graph) {
        if (root == null) return;

        int v = root.val;
        graph.putIfAbsent(v, new ArrayList<>());
        if (root.left != null) graph.get(v).add(root.left.val);
        if (root.right != null) graph.get(v).add(root.right.val);
        if (parent != null) graph.get(v).add(parent.val);

        dfs_buildGraph(root.left, root, graph);
        dfs_buildGraph(root.right, root, graph);
    }
```

## Common Pitfalls to Avoid
- N/A

---