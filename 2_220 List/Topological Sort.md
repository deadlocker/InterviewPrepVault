
# Topological Sort(Grokking)
## Intro
![[Pasted image 20220322110759.png]]
![[Pasted image 20220322110808.png]]
![[Pasted image 20220322110815.png]]

## Thoughts
- N/A

## Solutions
### #1 Kahn's Algo
```java
    // TC: O(V + E), SC: O(V + E)
    public static List<Integer> sort_Mine_Kahns_Algorithm(int n, int[][] edges) {
        List<Integer> res = new ArrayList<>();
        Map<Integer, List<Integer>> graph = new HashMap<>();
        Map<Integer, Integer> indegreeMap = new HashMap<>();
        for (int i = 0; i < n; i++) {
            graph.put(i, new ArrayList<>());
            indegreeMap.put(i, 0);
        }
        for (int[] edge : edges) {
            int source = edge[0], dest = edge[1];
            graph.get(source).add(dest);
            indegreeMap.put(dest, indegreeMap.get(dest) + 1);
        }

        Queue<Integer> q = new LinkedList<>();
        indegreeMap.forEach((k, v) -> {
            if (v == 0) q.offer(k);
        });

        while (!q.isEmpty()) {
            int vertex = q.poll();
            res.add(vertex);
            for (int neighbor : graph.get(vertex)) {
                indegreeMap.put(neighbor, indegreeMap.get(neighbor) - 1);
                if (indegreeMap.get(neighbor) == 0) q.offer(neighbor);
            }
        }
        // Always I forget this edge case
        if (res.size() != n) return new ArrayList<>();

        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# All Tasks Scheduling Orders(Grokking)
## Intro
![[Pasted image 20220322110929.png]]
![[Pasted image 20220322110935.png]]

## Thoughts
- N/A

## Solutions
### #1 Backtracking with Kahns Algo
```java
    // TC: O(V! * E), SC: O(V! * E)
    public static void printOrders_Grokking_Backtracking(int tasks, int[][] prerequisites) {
        List<Integer> sortedOrder = new ArrayList<>();
        if (tasks <= 0) return;

        // a. Initialize the graph
        Map<Integer, Integer> inDegree = new HashMap<>(); // count of incoming edges for every vertex
        Map<Integer, List<Integer>> graph = new HashMap<>(); // adjacency list graph
        for (int i = 0; i < tasks; i++) {
            inDegree.put(i, 0);
            graph.put(i, new ArrayList<>());
        }

        // b. Build the graph
        for (int[] prerequisite : prerequisites) {
            int parent = prerequisite[0], child = prerequisite[1];
            graph.get(parent).add(child); // put the child into it's parent's list
            inDegree.put(child, inDegree.get(child) + 1); // increment child's inDegree
        }

        // c. Find all q i.e., all vertices with 0 in-degrees
        Queue<Integer> q = new LinkedList<>();
        inDegree.forEach((k, v) -> {
            if (v == 0) q.offer(k);
        });

        printAllTopologicalSorts(graph, inDegree, q, sortedOrder);
    }

    private static void printAllTopologicalSorts(Map<Integer, List<Integer>> graph,
                                                 Map<Integer, Integer> inDegree,
                                                 Queue<Integer> q,
                                                 List<Integer> sortedOrder) {
        if (!q.isEmpty()) {
            for (Integer vertex : q) {
                sortedOrder.add(vertex);
                // clone the current q
                Queue<Integer> sourcesForNextCall = new LinkedList<>(q);
                // only remove the current source, all other q should remain in the queue for the next call
                sourcesForNextCall.remove(vertex);
                List<Integer> children = graph.get(vertex); // get the node's children to decrement their in-degrees
                for (int child : children) {
                    inDegree.put(child, inDegree.get(child) - 1);
                    // save the new source for the next call
                    if (inDegree.get(child) == 0) sourcesForNextCall.offer(child);
                }

                // recursive call to print other orderings from the remaining (and new) q
                printAllTopologicalSorts(graph, inDegree, sourcesForNextCall, sortedOrder);

                // backtrack, remove the vertex from the sorted order and put all of its children back to consider
                // the next source instead of the current vertex
                sortedOrder.remove(vertex);
                for (int child : children) inDegree.put(child, inDegree.get(child) + 1);
            }
        }

        // if sortedOrder doesn't contain all tasks, either we've a cyclic dependency between tasks, or
        // we have not processed all the tasks in this recursive call
        if (sortedOrder.size() == inDegree.size()) System.out.println(sortedOrder);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Course Schedule II
## Intro
https://leetcode.com/problems/course-schedule-ii/
![[Pasted image 20220322111055.png]]

## Thoughts
- N/A

## Solutions
### #1 DFS
```java
    // TC: O(V + E), SC: O(V + E)
    public int[] findOrder_UsingDFS(int numCourses, int[][] prerequisites) {
        Map<Integer, List<Integer>> graph = new HashMap<>();
        for (int i = 0; i < numCourses; i++) graph.put(i, new ArrayList<>());

        for (int[] edge : prerequisites) {
            int source = edge[1], dest = edge[0];
            graph.get(source).add(dest);
        }

        // 0 -> unvisited, 1 -> visiting, 2 -> visited
        int[] visitedStatus = new int[numCourses];

        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) {
            if (isCyclic(res, graph, visitedStatus, i)) return new int[0];
        }
        Collections.reverse(res);
        return res.stream().mapToInt(i -> i).toArray();
    }

    private boolean isCyclic(List<Integer> res, Map<Integer, List<Integer>> graph, int[] visitedStatus, int i) {
        int visit = visitedStatus[i];

        // if already visited, then return false, no cycle should exist
        if (visit == 2) return false;
        // if we came back to same node and it has a visit == 1, meaning we started from this node and came back to this node again,
        // => it implies that there is a cycle
        if (visit == 1) return true;

        // we are starting the visit, so put 1
        visitedStatus[i] = 1;
        boolean ret = false;
        for (int neighbor : graph.get(i)) {
            ret = isCyclic(res, graph, visitedStatus, neighbor);
            if (ret) break;
        }
        // we finished visiting, so put 2
        visitedStatus[i] = 2;

        // add to ans, note that res will be in reverse, ACN will already be in res before we add CN.(CN -> computer networks in our arbitrary made example)
        res.add(i);
        return ret;
    }
```

### #2 Kahns Using Map
```java
    // TC: O(V + E), SC: O(V + E)
    public int[] findOrder_Kahns_UsingHashMap(int numCourses, int[][] prerequisites) {
        Map<Integer, List<Integer>> graph = new HashMap<>();
        Map<Integer, Integer> indegreeMap = new HashMap<>();
        for (int i = 0; i < numCourses; i++) {
            graph.put(i, new ArrayList<>());
            indegreeMap.put(i, 0);
        }
        for (int[] edge : prerequisites) {
            int source = edge[1], dest = edge[0];
            graph.get(source).add(dest);
            indegreeMap.put(dest, indegreeMap.get(dest) + 1);
        }

        Queue<Integer> q = new LinkedList<>();
        indegreeMap.forEach((k, v) -> {
            if (v == 0) q.offer(k);
        });

        List<Integer> res = new ArrayList<>();
        while (!q.isEmpty()) {
            int vertex = q.poll();
            res.add(vertex);
            for (int neighbor : graph.get(vertex)) {
                indegreeMap.put(neighbor, indegreeMap.get(neighbor) - 1);
                if (indegreeMap.get(neighbor) == 0) q.offer(neighbor);
            }
        }
        if (res.size() != numCourses) return new int[0];
        return res.stream().mapToInt(i -> i).toArray();
    }
```

### #3 Kahns Using Array
```java
    // TC: O(V + E), SC: O(V + E)
    public int[] findOrder_Kahns_UsingArray(int numCourses, int[][] prerequisites) {
        Map<Integer, List<Integer>> graph = new HashMap<>();
        // use array instead of map to avoid one extra for loop
        int[] indegree = new int[numCourses];

        for (int[] edge : prerequisites) {
            int source = edge[1], dest = edge[0];
            if (!graph.containsKey(source)) graph.put(source, new ArrayList<>());
            graph.get(source).add(dest);
            indegree[dest]++;
        }

        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < indegree.length; i++) if (indegree[i] == 0) q.offer(i);

        int res[] = new int[numCourses], i = 0;
        while (!q.isEmpty()) {
            int vertex = q.poll();
            res[i++] = vertex;
            if (graph.containsKey(vertex)) {
                for (int neighbor : graph.get(vertex)) {
                    indegree[neighbor]--;
                    if (indegree[neighbor] == 0) q.offer(neighbor);
                }
            }
        }

        return (i == numCourses) ? res : new int[0];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Sequence Reconstruction
## Intro
https://leetcode.com/problems/sequence-reconstruction/
![[Pasted image 20220322111207.png]]
![[Pasted image 20220322111214.png]]

## Thoughts
- N/A

## Solutions
### #1 Kahns Mine
```java
    // TC: O(V + N), SC: O(V + N)
    // V = no.of distinct numbers, N = count of numbers in all seqs
    public boolean sequenceReconstruction_Mine(int[] nums, List<List<Integer>> seqs) {
        List<Integer> res = new ArrayList<>();
        Map<Integer, List<Integer>> graph = new HashMap<>();
        Map<Integer, Integer> indegreeMap = new HashMap<>();
        for (List<Integer> seq : seqs) {
            for (Integer integer : seq) {
                graph.putIfAbsent(integer, new ArrayList<>());
                indegreeMap.putIfAbsent(integer, 0);
            }
        }

        for (List<Integer> seq : seqs) {
            for (int i = 1; i < seq.size(); i++) {
                int source = seq.get(i - 1), dest = seq.get(i);
                graph.get(source).add(dest);
                indegreeMap.put(dest, indegreeMap.get(dest) + 1);
            }
        }

        if (nums.length != graph.size()) return false;

        Queue<Integer> q = new LinkedList<>();
        indegreeMap.forEach((k, v) -> {
            if (v == 0) q.offer(k);
        });

        while (!q.isEmpty()) {
            if (q.size() > 1) return false;
            if (nums[res.size()] != q.peek()) return false;
            int vertex = q.poll();
            res.add(vertex);
            for (int neighbor : graph.get(vertex)) {
                indegreeMap.put(neighbor, indegreeMap.get(neighbor) - 1);
                if (indegreeMap.get(neighbor) == 0) q.offer(neighbor);
            }
        }
        return res.size() == nums.length;
    }
```

### #2 Other solution
```java
    // todo how the hell does this work?
    // https://leetcode.com/problems/sequence-reconstruction/discuss/92574/Very-short-solution-with-explanation
    public boolean sequenceReconstruction(int[] org, List<List<Integer>> seqs) {
        // sanity check
        if (seqs == null || seqs.isEmpty())
            return false;

        final int N = org.length;
        int[] orders = new int[N + 1];
        for (int i = 0; i < N; ++i) {
            if (org[i] <= 0 || org[i] > N)
                return false;

            orders[org[i]] = i;
        }

        boolean[] pairs = new boolean[N];
        for (List<Integer> seq : seqs) {
            final int SIZE = seq.size();
            for (int i = 0; i < SIZE; ++i) {
                int cur = seq.get(i);
                if (cur <= 0 || cur > N)
                    return false;

                if (i == 0 && cur == org[0]) pairs[0] = true;
                if (i > 0) {
                    int prev = seq.get(i - 1);
                    if (orders[prev] >= orders[cur]) return false;
                    if (orders[prev] + 1 == orders[cur])
                        pairs[orders[cur]] = true;
                }
            }
        }

        for (int i = 0; i < N; ++i)
            if (!pairs[i])
                return false;

        return true;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Minimum Height Trees
## Intro
https://leetcode.com/problems/minimum-height-trees/
![[Pasted image 20220322111444.png]]
![[Pasted image 20220322111502.png]]

## Thoughts
- N/A

## Solutions
### #1 LC Way
```java
    // TC: O(V), SC: O(V), because no.of edges would be V-1
    public List<Integer> findMinHeightTrees_LCWay(int n, int[][] edges) {
        // base cases
        if (n < 2) {
            List<Integer> centroids = new ArrayList<>();
            for (int i = 0; i < n; i++) centroids.add(i);
            return centroids;
        }

        // Using Set instead of List coz we need to remove set.remove(vertex)
        Map<Integer, Set<Integer>> graph = new HashMap<>();
        for (int i = 0; i < n; i++) graph.put(i, new HashSet<>());

        for (int[] edge : edges) {
            int source = edge[0], dest = edge[1];
            graph.get(source).add(dest);
            graph.get(dest).add(source);
        }

        // initialize the first layer of the leaves
        List<Integer> leaves = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (graph.get(i).size() == 1) leaves.add(i);
        }

        // trim the leaves until reaching the centroids
        int remainingNodes = n;
        while (remainingNodes > 2) {
            remainingNodes -= leaves.size();

            List<Integer> newLeaves = new ArrayList<>();

            // remove the current leaves along the edges
            for (int leaf : leaves) {
                // the only neighbor left for the leaf node
                int leafNeighbor = graph.get(leaf).iterator().next();

                graph.get(leafNeighbor).remove(leaf);
                if (graph.get(leafNeighbor).size() == 1) newLeaves.add(leafNeighbor);
            }

            leaves = newLeaves;
        }

        // the remainingNodes are the centroids of the graph
        return leaves;
    }
```

### #2 My Way
```java
    // TC: O(V), SC: O(V), because no.of edges would be V-1
    public List<Integer> findMinHeightTrees_MyWay(int n, int[][] edges) {
        List<Integer> res = new ArrayList<>();

        if (n == 1) {
            res.add(0);
            return res;
        }

        Map<Integer, List<Integer>> graph = new HashMap<>();
        Map<Integer, Integer> degreeMap = new HashMap<>();
        for (int i = 0; i < n; i++) {
            graph.put(i, new ArrayList<>());
            degreeMap.put(i, 0);
        }

        for (int[] edge : edges) {
            int source = edge[0], dest = edge[1];
            graph.get(source).add(dest);
            graph.get(dest).add(source);
            degreeMap.put(source, degreeMap.get(source) + 1);
            degreeMap.put(dest, degreeMap.get(dest) + 1);
        }

        Queue<Integer> q = new LinkedList<>();
        degreeMap.forEach((k, v) -> {
            if (v == 1) q.offer(k);
        });

        int remainingNodes = n;
        while (remainingNodes > 2) {
            int size = q.size();
            remainingNodes -= size;
            for (int i = 0; i < size; i++) {
                int vertex = q.poll();
                for (int neighbor : graph.get(vertex)) {
                    degreeMap.put(neighbor, degreeMap.get(neighbor) - 1);
                    if (degreeMap.get(neighbor) == 1) q.offer(neighbor);
                }
            }
        }

        res.addAll(q);
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Sort Items By Groups Respecting Dependencies
## Intro
https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/
![[Pasted image 20220322111600.png]]
![[Pasted image 20220322111606.png]]

## Thoughts
- N/A

## Solutions
### #1 Two Level Top Sort
```java
    Map<Integer, List<Integer>> groupGraph = new HashMap<>();
    Map<Integer, List<Integer>> itemGraph = new HashMap<>();

    int[] groupsIndegree, itemsIndegree;

    // https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/discuss/389805/JAVA-Topological-sort.-Detailed-Explanation
    public int[] sortItems_TwoLevelTopSort(int n, int m, int[] group, List<List<Integer>> beforeItems) {
        for (int i = 0; i < group.length; i++) if (group[i] == -1) group[i] = m++;

        // Each item belongs to a group. If an item doesn't have a group it forms it's own isolated group.
        for (int i = 0; i < m; i++) groupGraph.put(i, new ArrayList<>());
        for (int i = 0; i < n; i++) itemGraph.put(i, new ArrayList<>());

        groupsIndegree = new int[m];
        itemsIndegree = new int[n];

        // form graph structure across different groups and different items and also calculate indegree.
        buildGraphOfGroups(group, beforeItems);
        buildGraphOfItems(beforeItems, n);

        // Topological ordering of the groups and items
        List<Integer> groupsList = topsort(groupGraph, groupsIndegree, m);
        List<Integer> itemsList = topsort(itemGraph, itemsIndegree, n);

        // Detect if there are any cycles.
        if (groupsList.isEmpty() || itemsList.isEmpty()) return new int[0];

        // This Map holds relative order of items in the same group computed in the loop below.
        Map<Integer, List<Integer>> groupsToItems = new HashMap<>();
        for (int item : itemsList) groupsToItems.computeIfAbsent(group[item], x -> new ArrayList<>()).add(item);

        int res[] = new int[n], i = 0;
        for (int grp : groupsList) {
            for (int item : groupsToItems.getOrDefault(grp, new ArrayList<>())) res[i++] = item;
        }
        return res;
    }

    private void buildGraphOfGroups(int[] group, List<List<Integer>> beforeItems) {
        for (int i = 0; i < group.length; i++) {
            int toGroup = group[i];
            List<Integer> fromItems = beforeItems.get(i);
            for (int fromItem : fromItems) {
                int fromGroup = group[fromItem];
                if (fromGroup != toGroup) {
                    groupGraph.computeIfAbsent(fromGroup, x -> new ArrayList<>()).add(toGroup);
                    groupsIndegree[toGroup]++;
                }
            }
        }
    }

    private void buildGraphOfItems(List<List<Integer>> beforeItems, int n) {
        for (int i = 0; i < n; i++) {
            List<Integer> items = beforeItems.get(i);
            for (Integer item : items) {
                itemGraph.computeIfAbsent(item, x -> new ArrayList<>()).add(i);
                itemsIndegree[i]++;
            }
        }
    }

    private List<Integer> topsort(Map<Integer, List<Integer>> graph, int[] indegree, int n) {
        List<Integer> list = new ArrayList<>();
        Queue<Integer> q = new LinkedList<>();
        for (int key : graph.keySet()) if (indegree[key] == 0) q.offer(key);

        while (!q.isEmpty()) {
            int node = q.poll();
            n--;
            list.add(node);
            for (int neighbor : graph.get(node)) {
                indegree[neighbor]--;
                if (indegree[neighbor] == 0) q.offer(neighbor);
            }
        }
        return n == 0 ? list : new ArrayList<>();
    }
```

### #2 Better Short Code
```java
    // https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/discuss/402945/C%2B%2B-with-picture-generic-topological-sort
    public int[] sortItems_Better(int n, int m, int[] group, List<List<Integer>> beforeItems) {
        List<Integer> res = new ArrayList<>();
        List<Integer>[] graph = new List[n + m];
        int[] indegree = new int[n + m];

        for (int i = 0; i < n + m; i++) graph[i] = new ArrayList<>();

        for (int i = 0; i < group.length; i++) {
            if (group[i] == -1) continue;
            graph[n + group[i]].add(i);
            indegree[i]++;
        }

        for (int i = 0; i < beforeItems.size(); i++) {
            for (int item : beforeItems.get(i)) {
                int repBeforeItem = group[item] == -1 ? item : n + group[item];
                int repCurrentItem = group[i] == -1 ? i : n + group[i];

                if (repBeforeItem == repCurrentItem) {
                    graph[item].add(i);
                    indegree[i]++;
                } else {
                    graph[repBeforeItem].add(repCurrentItem);
                    indegree[repCurrentItem]++;
                }
            }
        }

        for (int i = 0; i < n + m; i++) if (indegree[i] == 0) dfs(graph, indegree, i, n, res);

        return (res.size() == n) ? res.stream().mapToInt(i -> i).toArray() : new int[]{};
    }

    private void dfs(List<Integer>[] graph, int[] indegree, int cur, int n, List<Integer> res) {
        if (cur < n) res.add(cur);
        indegree[cur]--;

        for (int child : graph[cur]) {
            if (--indegree[child] == 0) dfs(graph, indegree, child, n, res);
        }
    }
```

## Common Pitfalls to Avoid
- N/A

---