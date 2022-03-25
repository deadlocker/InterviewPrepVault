# Backtracking

# [Word Search](https://leetcode.com/problems/word-search/)

## Intro

![Untitled](1_Blind75/Backtracki%20d585d/Untitled.png)

![Untitled](1_Blind75/Backtracki%20d585d/Untitled%201.png)

![Untitled](1_Blind75/Backtracki%20d585d/Untitled%202.png)

## Thoughts

- Pretty straightforward backtracking algorithm.

## Solutions

### #1 Backtracking

```java
// TC: O(N*3^L), SC: O(L), N is no.of cells on board, L is length of the word
public boolean exist(char[][] board, String word) {
    int rows = board.length, cols = board[0].length;

    for (int row = 0; row < rows; row++) {
        for (int col = 0; col < cols; col++) {
            if (backtrack(board, row, col, word, 0)) return true;
        }
    }
    return false;
}

private boolean backtrack(char[][] board, int row, int col, String word, int i) {
    if (i >= word.length()) return true;

    int rows = board.length, cols = board[0].length;
    if (row < 0 || row == rows || col < 0 || col == cols || board[row][col] != word.charAt(i)) return false;

    // mark the path before the next exploration
    board[row][col] = '#';
    boolean ret = false;
    int[] rowOffsets = {0, 0, 1, -1};
    int[] colOffsets = {1, -1, 0, 0};
    for (int d = 0; d < 4; d++) {
        ret = backtrack(board, row + rowOffsets[d], col + colOffsets[d], word, i + 1);
        if (ret) break;
    }

    // clean up and return the result
    board[row][col] = word.charAt(i);
    return ret;
}
```

## Common Pitfalls to Avoid

- One important edgecase is where you don’t set `A[r][c] = ‘#’` before starting the dfs process. If you dont set this, then there is a chance for you to come back to same node and consider the same letter twice even though this cell is only present once in the board. So set it to ‘#’, this serves as a visited set.
- The second important thing(which applies to all backtracking solutions) is to handle the backtracking case, that is reset the character back to what it was. You might have to set it to ‘#’ for your dfs process, but before leaving the function, you need to reset it back to whatever its previous value was like so: `A[r][c] = ch`.

# [Combination Sum](https://leetcode.com/problems/combination-sum/)

## Intro

![Untitled](1_Blind75/Backtracki%20d585d/Untitled%203.png)

## Thoughts

- This is fairly simple backtracking solution. Just be careful with basecases.

## Solutions

### #1 Two Choice Backtracking

```java
// TC: O(2^(t+n)), SC: O(t+n), t is target, n = A.length
public List<List<Integer>> combinationSum_My_Knapsack_Way(int[] A, int target) {
    List<List<Integer>> res = new ArrayList<>();
    backtrack(res, A, target, 0, new ArrayList<>());
    return res;
}

private void backtrack(List<List<Integer>> res, int[] A, int target, int i, List<Integer> list) {
    if (target == 0) {
        res.add(new ArrayList<>(list));
        return;
    }

    if (i >= A.length || target < 0) return;

    list.add(A[i]);
    backtrack(res, A, target - A[i], i, list);
    list.remove(list.size() - 1);

    backtrack(res, A, target, i + 1, list);
}
```

### #2 LC Way

![Untitled](1_Blind75/Backtracki%20d585d/Untitled%204.png)

```java
// TC: O(n^(t/m + 1)), SC: O(t/m), n = candidates.length, t = target, m = min value among the candidates
// Note that the above TC/SC is an upper bound and its not a true bound so to speak at least according to LC solutions
public List<List<Integer>> combinationSum_backtrackingApproach(int[] candidates, int target) {
    List<List<Integer>> res = new ArrayList<>();
    backtrack(res, new ArrayList<>(), candidates, target, 0);
    return res;
}

private void backtrack(
        List<List<Integer>> res,
        List<Integer> comb,
        int[] candidates,
        int target,
        int i
) {
    if (target == 0) {
        res.add(new ArrayList<>(comb));
        return;
    } else if (target < 0) return;

    for (int j = i; j < candidates.length; j++) {
        comb.add(candidates[j]);
        backtrack(res, comb, candidates, target - candidates[j], j);
        comb.remove(comb.size() - 1);
    }
```

## Common Pitfalls to Avoid
- If `target == 0`, we can add the current list to answer and simply return. There is no need to further explore even if we have not considered all elements in the array. This is because, each `candidates[i]` is given as `>= 1`