# Graphs

# [Number of Islands](https://leetcode.com/problems/number-of-islands/)

## Intro

![Untitled](1_Blind75/Graphs%20a5753/Untitled.png)

## Thoughts

- Pretty easy dfs or bfs or union find
- The key trick for UF approach is that whenever we unionize two components, the instance variable noOfComponents is decremented. Initially this variable is set to number of 1’s we have in our matrix.

## Solutions

### #1 DFS

```java
// TC: O(mn), SC: O(mn)
public int numIslands_DFS(char[][] grid) {
    int rows = grid.length, cols = grid[0].length, ans = 0;
    boolean[][] visited = new boolean[rows][cols];
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (!visited[r][c] && grid[r][c] == '1') {
                ans++;
                dfs(grid, visited, r, c);
            }
        }
    }
    return ans;
}

private void dfs(char[][] grid, boolean[][] visited, int r, int c) {
    int rows = grid.length, cols = grid[0].length;
    if (r < 0 || r >= rows || c < 0 || c >= cols || grid[r][c] == '0' || visited[r][c]) return;

    // Instead of using boolean array, you could also use grid itself and reset its value to '0' here
    // just like in leetcode solution
    visited[r][c] = true;
    dfs(grid, visited, r - 1, c);
    dfs(grid, visited, r + 1, c);
    dfs(grid, visited, r, c - 1);
    dfs(grid, visited, r, c + 1);

    /*
    // Another way of going to neighbors
    int[][] offsets = {{0, -1}, {0, 1}, {-1, 0}, {1, 0}};
    for(int[] offset : offsets) dfs(grid, row + offset[0], col + offset[1], visited);
     */
```

### #2 BFS

```java
// TC: O(mn), SC: O(min(m,n))
public int numIslands_BFS_AnotherWay(char[][] grid) {
    int count = 0, rows = grid.length, cols = grid[0].length;
    boolean[][] visited = new boolean[rows][cols];
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (!visited[i][j] && grid[i][j] == '1') {
                count++;
                bfs(grid, i, j, visited);
            }
        }
    }
    return count;
}

private void bfs(char[][] grid, int r, int c, boolean[][] visited) {
    int rows = grid.length, cols = grid[0].length;
    Queue<int[]> q = new LinkedList<>();
    q.offer(new int[]{r, c});
    while (!q.isEmpty()) {
        int[] arr = q.poll();
        int i = arr[0], j = arr[1];
        if (i < 0 || i >= rows || j < 0 || j >= cols || visited[i][j] || grid[i][j] == '0') continue;

        visited[i][j] = true;
        q.offer(new int[]{i - 1, j});
        q.offer(new int[]{i + 1, j});
        q.offer(new int[]{i, j - 1});
        q.offer(new int[]{i, j + 1});
    }
}
```

### # Union Find

```java
// TC: O(mn), SC: O(mn)
public int numIslands_UnionFindApproach(char[][] grid) {
    int rows = grid.length, cols = grid[0].length;

    UnionFind uf = new UnionFind(grid);
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (grid[r][c] == '1') {
                grid[r][c] = '0';
                if (r - 1 >= 0 && grid[r - 1][c] == '1') uf.union(r * cols + c, (r - 1) * cols + c);
                if (r + 1 < rows && grid[r + 1][c] == '1') uf.union(r * cols + c, (r + 1) * cols + c);
                if (c - 1 >= 0 && grid[r][c - 1] == '1') uf.union(r * cols + c, r * cols + c - 1);
                if (c + 1 < cols && grid[r][c + 1] == '1') uf.union(r * cols + c, r * cols + c + 1);
            }
        }
    }
    return uf.numberOfConnectedComponents;
}

static class UnionFind {
    int numberOfConnectedComponents; // no.of connected components
    int[] parent, rank;

    public UnionFind(char[][] grid) {
        int rows = grid.length, cols = grid[0].length;
        parent = new int[rows * cols];
        rank = new int[rows * cols];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == '1') {
                    parent[i * cols + j] = i * cols + j;
                    numberOfConnectedComponents++;
                }
            }
        }
    }

    // note this is not path compression
    public int find(int i) {
        if (parent[i] != i) parent[i] = find(parent[i]);
        return parent[i];
    }

    // Iterative find with path compression
//        private int find(int x) {
//            int rx = x;
//            while(rx != parent[rx]) {
//                rx = parent[rx];
//            }
//
//            while(x != parent[x]) {
//                int temp = parent[x];
//                parent[x] = rx;
//                x = temp;
//            }
//
//            return rx;
//        }

    // union with rank
    public void union(int x, int y) {
        int rootx = find(x), rooty = find(y);
        if (rootx != rooty) {
            if (rank[rootx] > rank[rooty]) parent[rooty] = rootx;
            else if (rank[rootx] < rank[rooty]) parent[rootx] = rooty;
            else {
                parent[rooty] = rootx;
                rank[rootx] += 1;
            }
            numberOfConnectedComponents--;
        }
    }
}
```

## Common Pitfalls to Avoid

- Don’t forget to check for bounds
- Also don’t forget to use some sort of mechanism for not visiting already visited cells. You can use an external visited boolean array or Set. Or simply make grid[r][c] = ‘0’ upon visiting it. Of course this means that you will be mutating the input, so check with interviewer if its ok to mutate the input.
- Also byheart the iterative version of find operation in Union Find Data structure

# [Course Schedule](https://leetcode.com/problems/course-schedule/)

## Intro

![Untitled](1_Blind75/Graphs%20a5753/Untitled%201.png)

## Thoughts

- Textbook definition of top sort.

## Solutions

### #1 Kahn’s TopSort Algorithm

```java
// TC: O(V + E), SC: O(V + E), v is numCourses, E is prerequisites.length
public boolean canFinish_TopSortApproach(int numCourses, int[][] prerequisites) {
    Map<Integer, List<Integer>> graph = new HashMap<>();
    Map<Integer, Integer> indegreeMap = new HashMap<>();
    int removedNodes = 0;
    for (int i = 0; i < numCourses; i++) {
        graph.put(i, new ArrayList<>());
        indegreeMap.put(i, 0);
    }
    for (int[] pre : prerequisites) {
        int source = pre[1], dest = pre[0];
        graph.get(source).add(dest);
        indegreeMap.put(dest, indegreeMap.get(dest) + 1);
    }

    Queue<Integer> q = new LinkedList<>();
    indegreeMap.forEach((v, indegreeCount) -> {
        if (indegreeCount == 0) q.add(v);
    });

    while (!q.isEmpty()) {
        int vertex = q.poll();
        removedNodes++;

        for (int neighbor : graph.get(vertex)) {
            indegreeMap.put(neighbor, indegreeMap.get(neighbor) - 1);
            if (indegreeMap.get(neighbor) == 0) q.add(neighbor);
        }
    }
    return removedNodes == numCourses;
```

### #2 DFS isCyclic approach

```java
// TC: O(V + E), SC: O(V + E)
public boolean canFinish_My_DFS(int n, int[][] edges) {
    Map<Integer, List<Integer>> graph = new HashMap<>();
    for (int i = 0; i < n; i++) graph.put(i, new ArrayList<>());
    for (int[] edge : edges) {
        int source = edge[1], dest = edge[0];
        graph.get(source).add(dest);
    }

    // 0 -> unvisited, 1 -> visiting, 2 -> visited
    int[] visited = new int[n];
    for (int i = 0; i < n; i++) {
        if (isCyclic(graph, i, visited)) return false;
    }

    return true;
}

private boolean isCyclic(Map<Integer, List<Integer>> graph, int i, int[] visited) {
    if (visited[i] == 2) return false;

    if (visited[i] == 1) return true;

    visited[i] = 1;

    boolean ret = false;
    for (int neighbor : graph.get(i)) {
        ret = isCyclic(graph, neighbor, visited);
        if (ret) break;
    }

    visited[i] = 2;
    return ret;
}
```

## Common Pitfalls to Avoid

- In Kahn’s algo, while returning make sure to check `removed == numCourses`.This happens when you have some nodes which are interdependent(has cycles) and therefore topsort might not apply.
- In isCyclic DFS approach, be careful with visited boolean array. It has three states. `visited[i] = 0` means its **unvisited**, `visited[i] = 1` means its **being visited**(and if in doing so, you encounter the same i node, then it means we have found a cycle), and `visited[i] = 2` means we have already **visited** i node.

# [No.of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)

## Intro

![Untitled](1_Blind75/Graphs%20a5753/Untitled%202.png)

![Untitled](1_Blind75/Graphs%20a5753/Untitled%203.png)

## Thoughts

- Ignite a fire and burn all nodes in current component. Do this until entire graph is burnt. The no.of times you have to ignite is essentially the answer.
- You can use DFS, BFS, or UF.

## Solutions

### #1 DFS Recursive

```java
// TC: O(V + E), SC: O(V + E)
public int countComponents_DFS(int n, int[][] edges) {
    Map<Integer, List<Integer>> graph = new HashMap<>();
    boolean[] visited = new boolean[n];
    for (int i = 0; i < n; i++) graph.put(i, new ArrayList<>());

    for (int[] edge : edges) {
        int source = edge[0], dest = edge[1];
        graph.get(source).add(dest);
        graph.get(dest).add(source);
    }

    int count = 0;
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            dfs(i, graph, visited);
            count++;
        }
    }
    return count;
}

private void dfs(int vertex, Map<Integer, List<Integer>> graph, boolean[] visited) {
    visited[vertex] = true;

    for (int neighbor : graph.get(vertex)) {
        if (!visited[neighbor]) dfs(neighbor, graph, visited);
    }
}
```

### #2 DFS Iterative(Stack)

```java
// TC: O(V + E), SC: O(V + E)
public int countComponents_DFS(int n, int[][] edges) {
    Map<Integer, List<Integer>> graph = new HashMap<>();
    boolean[] visited = new boolean[n];
    for (int i = 0; i < n; i++) graph.put(i, new ArrayList<>());

    for (int[] edge : edges) {
        int source = edge[0], dest = edge[1];
        graph.get(source).add(dest);
        graph.get(dest).add(source);
    }

    int count = 0;
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            dfs(i, graph, visited);
            count++;
        }
    }
    return count;
}

private void dfsIterative(Map<Integer, List<Integer>> graph, int i, boolean[] visited) {
    Stack<Integer> stack = new Stack<>();
    stack.push(i);
    while (!stack.isEmpty()) {
        int v = stack.pop();
        visited[v] = true;
        for (int neighbor : graph.get(v)) {
            if (!visited[neighbor]) stack.push(neighbor);
        }
    }
}
```

### #3 BFS

```java
// TC: O(V + E), SC: O(V + E)
public int countComponents_BFS(int n, int[][] edges) {
    Map<Integer, List<Integer>> graph = new HashMap<>();
    boolean[] visited = new boolean[n];
    for (int i = 0; i < n; i++) graph.put(i, new ArrayList<>());

    for (int[] edge : edges) {
        int source = edge[0], dest = edge[1];
        graph.get(source).add(dest);
        graph.get(dest).add(source);
    }

    int count = 0;

    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            bfs(graph, visited, i);
            count++;
        }
    }

    return count;
}

private void bfs(Map<Integer, List<Integer>> graph, boolean[] visited, int i) {
    Queue<Integer> q = new LinkedList<>();
    q.offer(i);
    while (!q.isEmpty()) {
        int vertex = q.poll();
        visited[vertex] = true;
        for (int neighbor : graph.get(vertex)) {
            if (!visited[neighbor]) q.offer(neighbor);
        }
    }
```

### #4 Union Find

```java
// TC: O(V + E), SC: O(V + E) --> assuming you implement path compression and weight union find
public int countComponents_MyUnionFindApproach(int n, int[][] edges) {
    UnionFind uf = new UnionFind(n);
    for (int[] edge : edges) {
        int source = edge[0], dest = edge[1];
        uf.union(source, dest);
    }
    return uf.count;
}

private static class UnionFind {
    int parent[], count;

    public UnionFind(int n) {
        parent = new int[n];
        count = n;
        for (int i = 0; i < n; i++) parent[i] = i;
    }

    private int find(int i) {
        if (i == parent[i]) return i;
        return find(parent[i]);
    }

    private void union(int x, int y) {
        int rootX = find(x), rootY = find(y);
        if (rootX == rootY) return;

        parent[rootY] = rootX;
        count--;
    }
}
```

## Common Pitfalls to Avoid

- In UnionFind, we initially set count to n and whenever we unionize, this count is reduced. And finally this count should give us the no.of connected components in this graph.

# [Clone Graph](https://leetcode.com/problems/clone-graph/)

## Intro

![Untitled](1_Blind75/Graphs%20a5753/Untitled%204.png)

![Untitled](1_Blind75/Graphs%20a5753/Untitled%205.png)

![Untitled](1_Blind75/Graphs%20a5753/Untitled%206.png)

## Thoughts

- Definitely need some sort of dfs or bfs. Initial thought would be to use HashSet, but then natural thought process would be that for oldNode > newNode mapping is required. Therefore this should tell us that we might need HashMap.
- But implementation is very tricky(at least with stack/bfs solutions)

## Solutions

### #1 DFS

```java
// TC: O(V + E), SC: O(V)
public Node cloneGraph_DFS(Node node) {
    return helper(node, new HashMap<>());
}

private Node helper(Node node, Map<Node, Node> visited) {
    if (node == null) return null;

    if (visited.containsKey(node)) return visited.get(node);

    Node newNode = new Node(node.val);
    visited.put(node, newNode);

    for (Node neighbor : node.neighbors) newNode.neighbors.add(helper(neighbor, visited));

    return newNode;
}

static class Node {
    public int val;
    public List<Node> neighbors = new ArrayList<>();

    public Node(int val) {
        this.val = val;
    }
}
```

### #2 DFS Stack

```java
// TC: O(V + E), SC: O(V + E)
public Node cloneGraph_UsingStack(Node node) {
    if (node == null) return null;

    Map<Node, Node> visited = new HashMap<>();
    Stack<Node> stack = new Stack<>();
    stack.push(node);
    visited.put(node, new Node(node.val));
    while (!stack.isEmpty()) {
        Node cur = stack.pop();
        for (Node neighbor : cur.neighbors) {
            if (!visited.containsKey(neighbor)) {
                visited.put(neighbor, new Node(neighbor.val));
                stack.push(neighbor);
            }
            visited.get(cur).neighbors.add(visited.get(neighbor));
        }
    }
    return visited.get(node);
```

### #3 BFS

```java
// TC: O(V + E), SC: O(V)
public Node cloneGraph_BFS(Node node) {
    if (node == null) return null;

    Map<Node, Node> visited = new HashMap<>();

    Queue<Node> q = new LinkedList<>();
    q.offer(node);
    visited.put(node, new Node(node.val));

    while (!q.isEmpty()) {
        Node n = q.poll();
        for (Node neighbor : n.neighbors) {
            if (!visited.containsKey(neighbor)) {
                visited.put(neighbor, new Node(neighbor.val));
                q.add(neighbor);
            }
            visited.get(n).neighbors.add(visited.get(neighbor));
        }
    }
    return visited.get(node);
}
```

## Common Pitfalls to Avoid

- N/A

# [Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/)

## Intro

![Untitled](1_Blind75/Graphs%20a5753/Untitled%207.png)

![Untitled](1_Blind75/Graphs%20a5753/Untitled%208.png)

## Thoughts

- list

## Solutions

### #1 DFS Recursive

```java
// TC: O(V + E), SC: O(V + E)
public boolean validTree_DFS_Recursive(int n, int[][] edges) {
    // a tree will always have n - 1 edges
    if (edges.length != n - 1) return false;

    Map<Integer, List<Integer>> graph = new HashMap<>();
    Set<Integer> seen = new HashSet<>();
    for (int i = 0; i < n; i++) graph.put(i, new ArrayList<>());

    for (int[] edge : edges) {
        int source = edge[0], dest = edge[1];
        graph.get(source).add(dest);
        graph.get(dest).add(source);
    }

    // return true iff no cycles were detected
    // and the entire graph has been reached
    return isNotCyclic(0, -1, graph, seen) && seen.size() == n;
}

private boolean isNotCyclic(int node, int parent, Map<Integer, List<Integer>> graph, Set<Integer> seen) {
    // cycle found if we have already seen this node
    if (seen.contains(node)) return false;

    seen.add(node);
    for (int neighbor : graph.get(node)) {
        // to avoid going back to node where we came from, we do this below if check
        if (parent != neighbor) {
            boolean ret = isNotCyclic(neighbor, node, graph, seen);
            if (!ret) return false;
        }
    }
    return true;
}
```

### #2 DFS Iterative (Stack) - not needed

```java
// TC: O(V + E), SC: O(V + E)
public boolean validTree_DFS_Iterative(int n, int[][] edges) {
    if (edges.length != n - 1) return false;

    Map<Integer, List<Integer>> graph = new HashMap<>();
    for (int i = 0; i < n; i++) graph.put(i, new ArrayList<>());
    for (int[] edge : edges) {
        int source = edge[0], dest = edge[1];
        graph.get(source).add(dest);
        graph.get(dest).add(source);
    }

    Map<Integer, Integer> parent = new HashMap<>();
    parent.put(0, -1);
    Stack<Integer> stack = new Stack<>();
    stack.push(0);

    while (!stack.isEmpty()) {
        int node = stack.pop();
        for (int neighbor : graph.get(node)) {
            // avoid going back to the node we came from
            if (parent.get(node) == neighbor) continue;
            // if we have already visited
            if (parent.containsKey(neighbor)) return false;
            stack.push(neighbor);
            parent.put(neighbor, node);
        }
    }

    // return false if dfs did not discover all nodes(i.e., if there are any unconnected components)
    return parent.size() == n;
```

### #3 BFS(not needed)

```java
// TC: O(V + E), SC: O(V + E)
public boolean validTree_BFS(int n, int[][] edges) {
    if (edges.length != n - 1) return false;

    Map<Integer, List<Integer>> graph = new HashMap<>();
    for (int i = 0; i < n; i++) graph.put(i, new ArrayList<>());
    for (int[] edge : edges) {
        int source = edge[0], dest = edge[1];
        graph.get(source).add(dest);
        graph.get(dest).add(source);
    }

    Map<Integer, Integer> parent = new HashMap<>();
    parent.put(0, -1);
    Queue<Integer> q = new LinkedList<>();
    q.offer(0);

    while (!q.isEmpty()) {
        int node = q.poll();
        for (int neighbor : graph.get(node)) {
            // avoid going back to the node we came from
            if (parent.get(node) == neighbor) continue;
            // if we have already visited
            if (parent.containsKey(neighbor)) return false;
            q.offer(neighbor);
            parent.put(neighbor, node);
        }
    }

    // return false if dfs did not discover all nodes(i.e., if there are any unconnected components)
    return parent.size() == n;
}
```

### #4 UnionFind

```java
// TC: O(N.alpha(N)), SC: O(N)
// Alpha(N) is inverse ackerman function. Todo more analysis on disjoint set data structure
public boolean validTree_DisjointSetApproach(int n, int[][] edges) {
    if (edges.length != n - 1) return false;

    // graph must contain a single connected component
    UnionFind uf = new UnionFind(n);

    for (int[] edge : edges) {
        int source = edge[0], dest = edge[1];
        // found cycle so return false
        if (!uf.union(source, dest)) return false;
    }

    return true;
}

static class UnionFind {
    private int[] parent, rank;

    public UnionFind(int n) {
        parent = new int[n];
        rank = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            rank[i] = 1;
        }
    }

    public int find(int A) {
        // find the root
        int root = A;
        while (parent[root] != root) root = parent[root];

        // do a second traversal, this time setting each node to point directly at A as we go
        // path compression
        while (A != root) {
            int oldRoot = parent[A];
            parent[A] = root;
            A = oldRoot;
        }
        return root;
    }

    public boolean union(int A, int B) {
        int rootA = find(A), rootB = find(B);

        if (rootA == rootB) return false;

        if (rank[rootA] < rank[rootB]) {
            parent[rootA] = rootB;
            rank[rootB] += rank[rootA];
        } else {
            parent[rootB] = rootA;
            rank[rootA] += rank[rootB];
        }
        return true;
    }
}
```

### #5 Kahn’s TopSort Algorithm

```java
// TC: O(n^2), SC: O(n)
public boolean validTree_KahnsAlgorithm_TopSort(int n, int[][] edges) {
    if (n == 1 && edges.length == 0) return true;
    if (edges.length != n - 1) return false;

    Map<Integer, List<Integer>> graph = new HashMap<>();
    Map<Integer, Integer> indegreeMap = new HashMap<>();
    for (int i = 0; i < n; i++) {
        graph.putIfAbsent(i, new ArrayList<>());
        indegreeMap.put(i, 0);
    }
    for (int[] edge : edges) {
        int src = edge[0], dest = edge[1];
        graph.get(src).add(dest);
        graph.get(dest).add(src);
        indegreeMap.put(src, indegreeMap.get(src) + 1);
        indegreeMap.put(dest, indegreeMap.get(dest) + 1);
    }
    Queue<Integer> q = new LinkedList<>();
    indegreeMap.forEach((v, indegree) -> {
        if (indegree == 1) q.offer(v);
    });
    int count = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        count++;
        for (int child : graph.get(u)) {
            indegreeMap.put(child, indegreeMap.get(child) - 1);
            if (indegreeMap.get(child) == 1) q.offer(child);
        }
    }
    return count == n;
}
```

## Common Pitfalls to Avoid

- For the graph to be a valid tree, there are 3 conditions it should satisfy.
- edges.length == n - 1. (Any less, it wont be fully connected, any more it will have cycles)
- No cycles
- No disconnected components

# [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)

## Intro

![Untitled](1_Blind75/Graphs%20a5753/Untitled%209.png)

![Untitled](1_Blind75/Graphs%20a5753/Untitled%2010.png)

![Untitled](1_Blind75/Graphs%20a5753/Untitled%2011.png)

## Thoughts

- Very hard question to understand itself. Just need to memorize.
- Also sometimes think of reversed approach. For example, instead of following problem’s description of water going from cells to oceans, what if we flood the cells from the oceans instead?

## Solutions

### #1 DFS

```java
// TC: O(mn), SC: O(mn)
public List<List<Integer>> pacificAtlantic_DFS(int[][] matrix) {
    List<List<Integer>> res = new ArrayList<>();
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return res;

    int rows = matrix.length, cols = matrix[0].length;
    boolean[][] visitedFromPacific = new boolean[rows][cols];
    boolean[][] visitedFromAtlantic = new boolean[rows][cols];
    for (int i = 0; i < rows; i++) {
        dfs(matrix, visitedFromPacific, Integer.MIN_VALUE, i, 0);
        dfs(matrix, visitedFromAtlantic, Integer.MIN_VALUE, i, cols - 1);
    }
    for (int i = 0; i < cols; i++) {
        dfs(matrix, visitedFromPacific, Integer.MIN_VALUE, 0, i);
        dfs(matrix, visitedFromAtlantic, Integer.MIN_VALUE, rows - 1, i);
    }
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (visitedFromPacific[i][j] && visitedFromAtlantic[i][j]) res.add(List.of(i, j));
        }
    }
    return res;
}

private void dfs(int[][] matrix, boolean[][] visited, int height, int r, int c) {
    int rows = matrix.length, cols = matrix[0].length;
    // be careful with matrix[r][c] < height condition
    // this is because we are flooding from pacific and atlantic as opposed to going from cells to these oceans
    if (r < 0 || r >= rows || c < 0 || c >= cols || visited[r][c] || matrix[r][c] < height) return;

    visited[r][c] = true;
    for (int[] d : dir) dfs(matrix, visited, matrix[r][c], r + d[0], c + d[1]);
}
```

### #2 BFS

```java
private int[][] dir = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

// TC: O(mn), SC: O(mn)
public List<List<Integer>> pacificAtlantic_BFS(int[][] matrix) {
    List<List<Integer>> res = new ArrayList<>();
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return res;

    int rows = matrix.length, cols = matrix[0].length;
    boolean[][] visitedFromPacific = new boolean[rows][cols];
    boolean[][] visitedFromAtlantic = new boolean[rows][cols];
    Queue<int[]> pQ = new LinkedList<>();
    Queue<int[]> aQ = new LinkedList<>();
    for (int i = 0; i < rows; i++) {
        pQ.offer(new int[]{i, 0});
        aQ.offer(new int[]{i, cols - 1});
        visitedFromPacific[i][0] = true;
        visitedFromAtlantic[i][cols - 1] = true;
    }
    for (int i = 0; i < cols; i++) {
        pQ.offer(new int[]{0, i});
        aQ.offer(new int[]{rows - 1, i});
        visitedFromPacific[0][i] = true;
        visitedFromAtlantic[rows - 1][i] = true;
    }
    bfs(matrix, pQ, visitedFromPacific);
    bfs(matrix, aQ, visitedFromAtlantic);
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (visitedFromPacific[i][j] && visitedFromAtlantic[i][j]) res.add(List.of(i, j));
        }
    }
    return res;
}

private void bfs(int[][] matrix, Queue<int[]> q, boolean[][] visited) {
    int rows = matrix.length, cols = matrix[0].length;
    while (!q.isEmpty()) {
        int[] node = q.poll();
        for (int[] d : dir) {
            int r = node[0] + d[0], c = node[1] + d[1];
            if (r < 0 || r >= rows || c < 0 || c >= cols || visited[r][c] || matrix[r][c] < matrix[node[0]][node[1]])
                continue;

            visited[r][c] = true;
            q.offer(new int[]{r, c});
        }
    }
}
```

## Common Pitfalls to Avoid

- N/A

---

# Longest Consecutive Sequence
## Intro
https://leetcode.com/problems/longest-consecutive-sequence/
![[Pasted image 20220321045301.png]]

## Thoughts
- list

## Solutions
### #1 BruteForce

```java
    // TC: O(n^3), SC: O(1)
    public int longestConsecutive_BruteForce(int[] nums) {
        int longestStreak = 0;
        for (int num : nums) {
            int currentNum = num;
            int currentStreak = 1;

            while (arrayContains(nums, currentNum + 1)) {
                currentStreak++;
                currentNum++;
            }

            longestStreak = max(longestStreak, currentStreak);
        }
        return longestStreak;
    }

    private boolean arrayContains(int[] arr, int a) {
        for (int num : arr) {
            if (a == num) return true;
        }
        return false;
    }
```

### #2 BruteForce using HashSet
```java
    // TC: O(n^2), SC: O(n)
    public int longestConsecutive_BruteForce_Set(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) set.add(num);

        int longestStreak = 0;
        for (int num : nums) {
            int val = num, currentStreak = 0;
            while (set.contains(val)) {
                currentStreak++;
                val--;
            }
            val = num + 1;
            while (set.contains(val)) {
                currentStreak++;
                val++;
            }
            longestStreak = max(longestStreak, currentStreak);
        }
        return longestStreak;
    }
```

### #3 Sorting
```java
    // TC: O(nlogn), SC: O(n)
    public int longestConsecutive_Sort_MyWay(int[] nums) {
        Arrays.sort(nums);
        int longestStreak = 0, currentStreak = 0;
        for (int i = 0; i < nums.length; i++) {
            if (i == 0 || nums[i] == nums[i - 1] + 1) {
                currentStreak++;
                longestStreak = max(longestStreak, currentStreak);
            } else if (nums[i] != nums[i - 1]) currentStreak = 1;
        }
        return longestStreak;
    }

    // TC: O(nlogn), SC: O(1)
    public int longestConsecutive_SortApproach(int[] nums) {
        if (nums.length == 0) return 0;

        Arrays.sort(nums);

        int longestStreak = 1, currentStreak = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != nums[i - 1]) {
                if (nums[i] == nums[i - 1] + 1) currentStreak++;
                else {
                    longestStreak = max(longestStreak, currentStreak);
                    currentStreak = 1;
                }
            }
        }
        return max(longestStreak, currentStreak);
    }
```

### #4 HashSet O(n)
```java
    // TC: O(n), SC: O(n)
    public int longestConsecutive_HashSetApproach(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) set.add(num);

        int longestStreak = 0;
        for (int num : set) {
            if (!set.contains(num - 1)) {
                int currentNum = num + 1, currentStreak = 1;

                while (set.contains(currentNum++)) currentStreak++;

                longestStreak = Math.max(longestStreak, currentStreak);
            }
        }
        return longestStreak;
    }
```

### #5 HashMap O(n)
```java
    // TC: O(n), SC: O(n)
    public int longestConsecutive_HashMapApproach(int[] nums) {
        int longest = 0;
        Map<Integer, Integer> map = new HashMap<>();
        for (int x : nums) {
            // if there is no duplicates, these two lines can be commented
            if (map.containsKey(x)) continue;
            map.put(x, 1);

            int end = x, begin = x;
            if (map.containsKey(x + 1)) end = x + map.get(x + 1);
            if (map.containsKey(x - 1)) begin = x - map.get(x - 1);
            longest = max(longest, end - begin + 1);
            map.put(end, end - begin + 1);
            map.put(begin, end - begin + 1);
        }
        return longest;
    }
```

### #6 HashMap Faster O(n)
```java
    // TC: O(n), SC: O(n)
    public int longestConsecutive_HashMap_Faster(int[] nums) {
        int longest = 0;
        Map<Integer, Boolean> map = new HashMap<>();
        for (int num : nums) map.put(num, false);

        for (int num : nums) {
            if (map.containsKey(num - 1) || map.get(num)) continue;
            map.put(num, true);
            int currentStreak = 0, x = num;
            while (map.containsKey(x)) {
                currentStreak++;
                x++;
            }
            if (longest < currentStreak) longest = currentStreak;

        }
        return longest;
    }
```

### #7 Union Find
```java
    // TC: O(n), SC: O(n), UF has amortized TC of O(n) since we implemented both path compression and weighted union find
    public int longestConsecutive_UnionFind(int[] A) {
        Map<Integer, Integer> valToIndex = new HashMap<>();
        int n = A.length;
        UF uf = new UF(n);
        for (int i = 0; i < n; i++) {
            int num = A[i];
            if (valToIndex.containsKey(num)) continue;

            if (valToIndex.containsKey(num + 1)) uf.union(i, valToIndex.get(num + 1));
            if (valToIndex.containsKey(num - 1)) uf.union(i, valToIndex.get(num - 1));

            valToIndex.put(num, i);
        }
        return uf.max;
    }

    static class UF {
        int parent[], rank[], max;

        public UF(int n) {
            parent = new int[n];
            rank = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                rank[i] = 1;
                max = 1;
            }
        }

        private int find(int i) {
            if (i != parent[i]) parent[i] = find(parent[i]);
            return parent[i];
        }

        private void union(int x, int y) {
            int rx = find(x), ry = find(y);
            if (rx == ry) return;
            if (rank[rx] <= rank[ry]) {
                parent[rx] = ry;
                rank[ry] += rank[rx];
                max = Math.max(max, rank[ry]);
            } else {
                parent[ry] = rx;
                rank[rx] += rank[ry];
                max = Math.max(max, rank[rx]);
            }
        }
    }
```

## Common Pitfalls to Avoid
- N/A

---


# Alien Dictionary
## Intro
https://leetcode.com/problems/alien-dictionary/
![[Pasted image 20220321051705.png]]

## Thoughts
- list

## Solutions
### #1 DFS

```java
    // TC: O(C), SC: O(1) or O(U + min(U^2, N))
    // where N = words.length, C = total length of all words, U = total no.of unique letters in alien alphabet(max U can be 26 according to question)
    public String alienOrder_DFS(String[] words) {
        Map<Character, List<Character>> graph = new HashMap<>();
        for (String word : words) {
            for (char c : word.toCharArray()) graph.putIfAbsent(c, new ArrayList<>());
        }

        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i], w2 = words[i + 1];
            if (w1.length() > w2.length() && w1.startsWith(w2)) return "";

            for (int j = 0; j < Math.min(w1.length(), w2.length()); j++) {
                char c1 = w1.charAt(j), c2 = w2.charAt(j);
                if (c1 != c2) {
                    graph.get(c1).add(c2);
                    break;
                }
            }
        }

        StringBuilder sb = new StringBuilder();
        // 0: unvisited, 1: visiting, 2: visited
        int[] visited = new int[26];
        for (char u : graph.keySet()) if (isCyclic(graph, u, visited, sb)) return "";
        return sb.length() == graph.size() ? sb.reverse().toString() : "";
    }

    private boolean isCyclic(Map<Character, List<Character>> graph, char u, int[] visited, StringBuilder sb) {
        int index = u - 'a';
        if (visited[index] == 1) return true;
        if (visited[index] == 2) return false;

        visited[index] = 1;

        boolean ret = false;
        for (char v : graph.get(u)) {
            ret = isCyclic(graph, v, visited, sb);
            if (ret) break;
        }
        visited[index] = 2;
        sb.append(u);
        return ret;
    }
```

### #2 BFS Kahns Algorithm
```java
    // TC: O(C), SC: O(1) or O(U + min(U^2, N))
    // where N = words.length, C = total length of all words, U = total no.of unique letters in alien alphabet(max U can be 26 according to question)
    public String alienOrder_Kahns(String[] words) {
        Map<Character, List<Character>> graph = new HashMap<>();
        Map<Character, Integer> indegreeMap = new HashMap<>();
        for (String word : words) {
            for (char c : word.toCharArray()) {
                graph.put(c, new ArrayList<>());
                indegreeMap.put(c, 0);
            }
        }

        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i], w2 = words[i + 1];
            // check that w2 is not a prefix of w1
            if (w1.length() > w2.length() && w1.startsWith(w2)) return "";

            for (int j = 0; j < Math.min(w1.length(), w2.length()); j++) {
                if (w1.charAt(j) != w2.charAt(j)) {
                    graph.get(w1.charAt(j)).add(w2.charAt(j));
                    indegreeMap.put(w2.charAt(j), indegreeMap.get(w2.charAt(j)) + 1);
                    break;
                }
            }
        }

        StringBuilder sb = new StringBuilder();
        Queue<Character> q = new LinkedList<>();
        indegreeMap.forEach((k, v) -> {
            if (v == 0) q.offer(k);
        });
        while (!q.isEmpty()) {
            char c = q.poll();
            sb.append(c);
            for (char neighbor : graph.get(c)) {
                indegreeMap.put(neighbor, indegreeMap.get(neighbor) - 1);
                if (indegreeMap.get(neighbor) == 0) q.offer(neighbor);
            }
        }

        return sb.length() == graph.size() ? sb.toString() : "";
    }
```

## Common Pitfalls to Avoid
- N/A

---