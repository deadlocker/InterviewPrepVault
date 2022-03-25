# Trie

# [Implement Trie(Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)

## Intro

![Untitled](1_Blind75/Trie%20c33b1/Untitled.png)

## Thoughts

- Textbook definition of Trie.

## Solutions

### #1 Trie Implementation

```java
public class P208_ImplementTrie {

    private TrieNode root;

    /**
     * Initialize your data structure here.
     */
    public P208_ImplementTrie() {
        root = new TrieNode();
    }

    /**
     * Inserts a word into the trie.
     */
    // TC: O(m), SC: O(m), where m is word.length()
    public void insert(String word) {
        TrieNode cur = root;
        for (char c : word.toCharArray()) cur = cur.children.computeIfAbsent(c, k -> new TrieNode());
        cur.isLeaf = true;
    }

    /**
     * Returns if the word is in the trie.
     */
    // TC: O(m), SC: O(1)
    public boolean search(String word) {
        TrieNode cur = root;
        for (char c : word.toCharArray()) {
            cur = cur.children.get(c);
            if (cur == null) return false;
        }
        return cur.isLeaf;
    }

    /**
     * Returns if there is any word in the trie that starts with the given prefix.
     */
    // TC: O(m), SC: O(1)
    public boolean startsWith(String prefix) {
        if (prefix == null) return false;

        TrieNode cur = root;
        for (char c : prefix.toCharArray()) {
            cur = cur.children.get(c);
            if (cur == null) return false;
        }
        return true;
    }

    static class TrieNode {
        boolean isLeaf;
        Map<Character, TrieNode> children = new HashMap<>();
    }

}
```

## Common Pitfalls to Avoid

- Look at the difference between `contains` and `startsWith` method. The only difference is that in the last line for `contains`we check if its **leaf**(`cur.isLeaf`). But for `startsWith`, we simply `return true` in last line.

# [Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/)

## Intro

![Untitled](1_Blind75/Trie%20c33b1/Untitled%201.png)

## Thoughts

- Pretty straightforward Trie based solution. Just be careful with the dot feature.

## Solutions

### #1 Trie Solution

```java
public class P211_DesignAddAndSearchWords {
    TrieNode root = new TrieNode();

    // TC: O(m), SC: O(m), m is word.length()
    public void addWord(String word) {
        TrieNode cur = root;
        for (char c : word.toCharArray()) cur = cur.children.computeIfAbsent(c, k -> new TrieNode());
        cur.isWord = true;
    }

    // TC: O(m), SC: O(1) for well defined words which don't have '.' in them.
    // TC: O(n. 26^m), SC: O(1) for undefined words, n is number of keys.
    public boolean search(String word) {
        return searchInNode(word, root);
    }

    private boolean searchInNode(String word, TrieNode node) {
        for (int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            if (!node.children.containsKey(c)) {
                if (c == '.') {
                    for (char x : node.children.keySet()) {
                        TrieNode child = node.children.get(x);
                        if (searchInNode(word.substring(i + 1), child)) return true;
                    }
                }
                return false;
            } else node = node.children.get(c);
        }
        return node.isWord;
    }

    static class TrieNode {
        boolean isWord;
        Map<Character, TrieNode> children = new HashMap<>();
    }
}
```

## Common Pitfalls to Avoid

- Very tricky to implement the dot operator. But if you think carefully it should become obvious.

# [Word Break](https://leetcode.com/problems/word-break/)

## Intro

![Untitled](1_Blind75/Trie%20c33b1/Untitled%202.png)

## Thoughts

- Dp Solution is pretty straight forward. But Trie is also possible.

## Solutions

### #1 BruteForce Recursive

```java
// TC: O(2^n), SC: O(n)
public boolean wordBreakRecursiveBruteForce(String s, List<String> wordDict) {
    return helper(s, new HashSet<>(wordDict), 0);
}

private boolean helper(String s, Set<String> wordDict, int start) {
    if (start == s.length()) return true;

    for (int end = start + 1; end <= s.length(); end++) {
        if (wordDict.contains(s.substring(start, end)) && helper(s, wordDict, end)) return true;
    }

    return false;
}
```

### #2 TopDown Memo

```java
// TC: O(n^3), SC: O(n), n^3 TC coz n^2 recursion depth and n substring method
public boolean wordBreakTopDownDP(String s, List<String> wordDict) {
    return helper(s, new HashSet<>(wordDict), 0, new Boolean[s.length()]);
}

private boolean helper(String s, Set<String> wordDict, int start, Boolean[] memo) {
    if (start == s.length()) return true;

    if (memo[start] != null) return memo[start];

    for (int end = start + 1; end <= s.length(); end++) {
        if (wordDict.contains(s.substring(start, end)) && helper(s, wordDict, end, memo)) return memo[start] = true;
    }

    return memo[start] = false;
}
```

### #3 DP BottomUP

```java
// TC: O(n^3), SC: O(n)
public boolean wordBreakBottomUpDP(String s, List<String> wordDict) {
    Set<String> set = new HashSet<>(wordDict);
    boolean[] dp = new boolean[s.length() + 1];
    dp[0] = true;
    for (int i = 1; i < dp.length; i++) {
        for (int j = 0; j < i; j++) {
            if (dp[j] && set.contains(s.substring(j, i))) {
                dp[i] = true;
                break;
            }
        }
    }
    return dp[s.length()];
}
```

### #4 BFS(not needed)

```java
// TODO relearn how this works, also analyse how this time complexity is derived.
// TC: O(n^3), SC: O(n)
public boolean wordBreak_BFS(String s, List<String> wordDict) {
    Set<String> set = new HashSet<>(wordDict);
    Queue<Integer> q = new LinkedList<>();
    boolean[] visited = new boolean[s.length()];
    q.add(0);
    while (!q.isEmpty()) {
        int start = q.poll();
        if (!visited[start]) {
            for (int end = start + 1; end <= s.length(); end++) {
                if (set.contains(s.substring(start, end))) {
                    q.add(end);
                    if (end == s.length()) return true;
                }
            }
            visited[start] = true;
        }
    }
    return false;
}
```

### #5 Trie

```java
// this is the best solution amongst all.
// TC: O(n^2 + t), SC: O(n + t), n = s.length(), t is total chars in wordDict
public boolean wordBreak_Best(String s, List<String> wordDict) {
    wordDict.forEach(this::insert);
    return dfs(s, 0, new Boolean[s.length()]);
}

private boolean dfs(String s, int start, Boolean[] memo) {
    if (start == s.length()) return true;
    if (memo[start] != null) return memo[start];

    TrieNode cur = root;
    for (int end = start + 1; end <= s.length(); end++) {
        char c = s.charAt(end - 1);
        cur = cur.children.get(c);
        if (cur == null) break;
        if (cur.isLeaf && dfs(s, end, memo)) return memo[start] = true;
    }
    return memo[start] = false;
}
```

## Common Pitfalls to Avoid

- In Trie solution be careful with what TrieNode you make computations on.

---

# Word Search II
## Intro
https://leetcode.com/problems/word-search-ii/
![[Pasted image 20220321051911.png]]
![[Pasted image 20220321051919.png]]

## Thoughts
- list

## Solutions
### #1 BruteForce Using Word Search

```java
    // TC: O(K*M* 3^L), SC: O(L), M is no.of cells on board, L is length of the word, k is no.of words
    public List<String> findWords_BruteForce(char[][] board, String[] words) {
        List<String> result = new ArrayList<>();
        for (String word : words) {
            if (exist(board, word)) result.add(word);
        }
        return result;
    }

    public boolean exist(char[][] board, String word) {
        int rows = board.length, cols = board[0].length;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (exists(board, word, 0, i, j)) return true;
            }
        }
        return false;
    }

    private boolean exists(char[][] board, String s, int i, int row, int col) {
        if (i >= s.length()) return true;

        int rows = board.length, cols = board[0].length;
        if (row < 0 || row >= rows || col < 0 || col >= cols || board[row][col] != s.charAt(i)) return false;

        board[row][col] = '#';

        int offsets[][] = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        boolean ret = false;
        for (int[] offset : offsets) {
            ret = exists(board, s, i + 1, row + offset[0], col + offset[1]);
            if (ret) break;
        }

        board[row][col] = s.charAt(i);
        return ret;
    }
```

### #2 Trie Approach
```java
    char[][] board;
    List<String> res = new ArrayList<>();

    // TC: O(M(4.3^(L - 1))), SC: O(N)
    public List<String> findWords(char[][] board, String[] words) {
        this.board = board;

        // add words to trie
        TrieNode root = new TrieNode();
        for (String word : words) {
            TrieNode cur = root;
            for (char c : word.toCharArray()) cur = cur.children.computeIfAbsent(c, k -> new TrieNode());
            cur.word = word;
        }

        // backtrack starting for each cell in the board
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[i].length; j++) {
                if (root.children.containsKey(board[i][j])) backtrack(i, j, root);
            }
        }

        return res;
    }

    private void backtrack(int row, int col, TrieNode node) {
        char c = board[row][col];
        TrieNode cur = node.children.get(c);

        if (cur.word != null) {
            res.add(cur.word);
            cur.word = null;
        }

        board[row][col] = '#';

        int offsets[][] = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
        for (int[] offset : offsets) {
            int newRow = row + offset[0], newCol = col + offset[1];
            if (newRow < 0 || newRow >= board.length || newCol < 0 || newCol >= board[0].length) continue;
            if (cur.children.containsKey(board[newRow][newCol])) backtrack(newRow, newCol, cur);
        }

        board[row][col] = c;

        // Optimization: incrementally remove the leaf nodes
        if (cur.children.isEmpty()) node.children.remove(c);
    }

    static class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        String word = null;
    }
```

## Common Pitfalls to Avoid
- N/A

---