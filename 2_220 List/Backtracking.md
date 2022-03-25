# Combinations
## Intro
https://leetcode.com/problems/combinations/
![[Pasted image 20220321141654.png]]

## Thoughts
- N/A

## Solutions
### #1 Backtracking Approach

```java
    // TC: O(k C(n, k)), SC: O(C(n, k)) where C(n, k) means nCk formula
    // For TC, extra k multiplier at start is coz of line res.add(new LinkedList<>(cur));
    // adding to linkedlist happens nCk times. And for each time we are adding k elements
    // hence the above mentioned tc
    public List<List<Integer>> combine_backtrackApproach(int n, int k) {
        List<List<Integer>> res = new LinkedList<>();
        backtrack(n, k, 1, new LinkedList<>(), res);
        return res;
    }

    private void backtrack(int n, int k, int first, LinkedList<Integer> cur, List<List<Integer>> res) {
        if (cur.size() == k) res.add(new LinkedList<>(cur));

        for (int i = first; i <= n; i++) {
            cur.add(i);
            backtrack(n, k, i + 1, cur, res);
            cur.removeLast();
        }
    }
```

### #2 Binary Sorted Approach
```java
    // TC: O(k C(n, k)), SC: O(C(n, k)) where C(n, k) means nCk formula
    // TODO relearn how this works
    public List<List<Integer>> combine_binarySortedApproach(int n, int k) {
        List<Integer> nums = new ArrayList<>();
        for (int i = 1; i <= k; i++) nums.add(i);
        nums.add(n + 1);

        List<List<Integer>> res = new ArrayList<>();
        int i = 0;
        while (i < k) {
            res.add(new ArrayList<>(nums.subList(0, k)));
            i = 0;
            while ((i < k) && (nums.get(i + 1) == nums.get(i) + 1)) nums.set(i, i++ + 1);
            nums.set(i, nums.get(i) + 1);
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Permutations II
## Intro
https://leetcode.com/problems/permutations-ii/
![[Pasted image 20220321141824.png]]

## Thoughts
- N/A

## Solutions
### #1 Backtracking

```java
    // TC: O(sigma(P(n,k), where k goes from 1 to n)), SC: O(n)
    // P(n,k) is permutation notation, check leetcode for explanation
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();

        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums) map.put(num, map.getOrDefault(num, 0) + 1);

        LinkedList<Integer> comb = new LinkedList<>();
        backtrack(comb, nums.length, map, res);
        return res;
    }

    private void backtrack(LinkedList<Integer> comb, int n, Map<Integer, Integer> map, List<List<Integer>> res) {
        if (comb.size() == n) {
            res.add(new ArrayList<>(comb));
            return;
        }

        map.forEach((num, count) -> {
            if (count == 0) return;
            comb.addLast(num);
            map.put(num, count - 1);

            backtrack(comb, n, map, res);

            comb.removeLast();
            map.put(num, count);
        });
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Combination Sum II
## Intro
https://leetcode.com/problems/combination-sum-ii/
![[Pasted image 20220321141940.png]]

## Thoughts
- N/A

## Solutions
### #1 Backtracking Counter Approach

```java
    // TC: O(2^n), SC: O(n)
    public List<List<Integer>> combinationSum2_backtracking_Counter(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : candidates) map.put(num, map.getOrDefault(num, 0) + 1);

        // convert map to a list of(num, count) tuples
        List<int[]> counterList = new ArrayList<>();
        map.forEach((key, value) -> counterList.add(new int[]{key, value}));

        backtrack(res, new ArrayList<>(), counterList, target, 0);
        return res;
    }

    private void backtrack(
            List<List<Integer>> res,
            List<Integer> comb,
            List<int[]> counterList,
            int target,
            int start
    ) {
        if (target == 0) {
            res.add(new ArrayList<>(comb));
            return;
        } else if (target < 0) return; // if you dont add this else if, you will get TLE on LC

        for (int i = start; i < counterList.size(); i++) {
            int[] entry = counterList.get(i);
            int candidate = entry[0], freq = entry[1];

            if (freq <= 0) continue;

            comb.add(candidate);
            counterList.set(i, new int[]{candidate, freq - 1});

            backtrack(res, comb, counterList, target - candidate, i);

            comb.remove(comb.size() - 1);
            counterList.set(i, new int[]{candidate, freq});
        }
    }
```

### #2 Backtracking Sort Approach
```java
    // TC: O(2^n), SC: O(n)
    public List<List<Integer>> combinationSum2_SortApproach(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        Arrays.sort(candidates);

        backtrack(res, new ArrayList<>(), candidates, target, 0);
        return res;
    }

    private void backtrack(
            List<List<Integer>> res,
            List<Integer> comb,
            int[] candidates,
            int target,
            int start
    ) {
        if (target == 0) {
            res.add(new ArrayList<>(comb));
            return;
        } else if (target < 0) return;

        for (int i = start; i < candidates.length; i++) {
            if (i > start && candidates[i] == candidates[i - 1]) continue;

            int candidate = candidates[i];
            comb.add(candidate);
            backtrack(res, comb, candidates, target - candidate, i + 1);
            comb.remove(comb.size() - 1);
        }
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Combination Sum III
## Intro
https://leetcode.com/problems/combination-sum-iii/
![[Pasted image 20220321142051.png]]
![[Pasted image 20220321142058.png]]

## Thoughts
- N/A

## Solutions
### #1 Backtracking

```java
    // TC: O(k * 9Pk), SC: O(k), 9Pk is permutation formula
    // Although in comments people did mention that it should be 9Ck instead of k*9Pk. Recheck that yourself next time
    public List<List<Integer>> combinationSum3_backtrackingApproach(int k, int n) {
        List<List<Integer>> res = new ArrayList<>();
        backtrack(res, new ArrayList<>(), n, k, 1);
        return res;
    }

    private void backtrack(
            List<List<Integer>> res,
            List<Integer> comb,
            int target,
            int k,
            int start
    ) {
        if (target == 0 && comb.size() == k) {
            res.add(new ArrayList<>(comb));
            return;
        } else if (target < 0 || comb.size() == k) return;

        for (int i = start; i <= 9; i++) {
            comb.add(i);
            backtrack(res, comb, target - i, k, i + 1);
            comb.remove(comb.size() - 1);
        }
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Letter Combinations of a Phone Number
## Intro
https://leetcode.com/problems/letter-combinations-of-a-phone-number/
![[Pasted image 20220321142203.png]]

## Thoughts
- N/A

## Solutions
### #1 Backtracking

```java
    Map<Character, String> map = Map.of(
            '2', "abc",
            '3', "def",
            '4', "ghi",
            '5', "jkl",
            '6', "mno",
            '7', "pqrs",
            '8', "tuv",
            '9', "wxyz"
    );


    // TC: O(3^n * 4^m), SC: O(3^n * 4^m)
    // where digits.length() = n + m, where n is no.of digits that map to three chars(like 2, 3 etc) while
    // m is no.of digits that map to four chars(like 7 or 9).
    // TC: O(4^n * n), SC: O(n) --> LC analysis
    public List<String> letterCombinations_Backtracking(String digits) {
        List<String> res = new ArrayList<>();
        if (digits.length() == 0) return res;
        backtrack(res, digits, map, new StringBuilder(), 0);
        return res;
    }

    private void backtrack(List<String> res, String digits, Map<Character, String> map, StringBuilder cur, int i) {
        if (i == digits.length()) {
            res.add(cur.toString());
            return;
        }

        String lettersList = map.get(digits.charAt(i));
        for (char c : lettersList.toCharArray()) {
            cur.append(c);
            backtrack(res, digits, map, cur, i + 1);
            cur.deleteCharAt(cur.length() - 1);
        }
    }
```

### #2 Stack Approach
```java
    Map<Character, String> map = Map.of(
            '2', "abc",
            '3', "def",
            '4', "ghi",
            '5', "jkl",
            '6', "mno",
            '7', "pqrs",
            '8', "tuv",
            '9', "wxyz"
    );

    // TC: O(?), SC: O(?)
    public List<String> letterCombinations_StackApproach(String s) {
        List<String> res = new ArrayList<>();
        if (s.isEmpty()) return res;
        Stack<String> stack = new Stack<>();
        stack.push("");
        int n = s.length();
        while (!stack.isEmpty()) {
            String cur = stack.pop();
            if (cur.length() == n) res.add(cur);
            else {
                for (char c : map.get(s.charAt(cur.length())).toCharArray()) {
                    stack.push(cur + c);
                }
            }
        }
        return res;
    }
```

### #4 BFS
```java
    Map<Character, String> map = Map.of(
            '2', "abc",
            '3', "def",
            '4', "ghi",
            '5', "jkl",
            '6', "mno",
            '7', "pqrs",
            '8', "tuv",
            '9', "wxyz"
    );

    // TC: O(?), SC: O()
    public List<String> letterCombinations_BFS(String s) {
        List<String> res = new ArrayList<>();
        if (s.isEmpty()) return res;
        Queue<Pair<String, Integer>> q = new LinkedList<>();
        q.offer(new Pair<>("", 0));
        while (!q.isEmpty()) {
            Pair<String, Integer> pair = q.poll();
            String cur = pair.getKey();
            int i = pair.getValue();
            if (i == s.length()) {
                res.add(cur);
                continue;
            }
            for (char c : map.get(s.charAt(i)).toCharArray()) {
                q.offer(new Pair<>(cur + c, i + 1));
            }
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# N Queens
## Intro
https://leetcode.com/problems/n-queens/
![[Pasted image 20220321142440.png]]

## Thoughts
- N/A

## Solutions
### #1 Way 1

```java
    int[] cols, dales, hills, queens;
    int n;
    List<List<String>> res = new ArrayList<>();

    // TC: O(n!), SC: O(n^2)
    public List<List<String>> solveNQueens(int n) {
        this.n = n;
        cols = new int[n];
        dales = new int[2 * n - 1];
        hills = new int[2 * n - 1];
        queens = new int[n];

        backtrack(0);
        return res;
    }

    private void backtrack(int row) {
        for (int col = 0; col < n; col++) {
            if (isNotUnderAttack(row, col)) {
                placeQueen(row, col);

                // if n queens are already place
                if (row == n - 1) addSolution();
                    // if not proceed to place the rest
                else backtrack(row + 1);
                removeQueen(row, col);
            }
        }
    }

    private boolean isNotUnderAttack(int row, int col) {
        return (cols[col] + dales[row - col + n - 1] + hills[row + col]) == 0;
    }

    private void placeQueen(int row, int col) {
        queens[row] = col;
        cols[col] = 1;
        dales[row - col + n - 1] = 1;
        hills[row + col] = 1;
    }

    private void removeQueen(int row, int col) {
        queens[row] = 0;
        cols[col] = 0;
        dales[row - col + n - 1] = 0;
        hills[row + col] = 0;
    }

    private void addSolution() {
        List<String> list = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            StringBuilder sb = new StringBuilder();
            for (int j = 0; j < n; j++) sb.append(queens[i] == j ? "Q" : ".");
            list.add(sb.toString());
        }
        res.add(list);
    }
```

### #2 Way 2
```java
    int[] cols, dales, hills, queens;
    int n;
    List<List<String>> res = new ArrayList<>();

    // TC: O(n! * 4n), SC: O(n^2), times 4n coz of 4 loops while checking is Valid
    public List<List<String>> solveNQueens_MyWay(int n) {
        List<List<String>> res = new ArrayList<>();
        boolean[][] board = new boolean[n][n];
        backtrack(res, board, 0);
        return res;
    }

    private void backtrack(List<List<String>> res, boolean[][] board, int pos) {
        int n = board.length;
        if (pos == n) {
            List<String> list = new ArrayList<>();
            for (boolean[] row : board) {
                StringBuilder sb = new StringBuilder();
                for (int j = 0; j < n; j++) {
                    if (row[j]) sb.append('Q');
                    else sb.append('.');
                }
                list.add(sb.toString());
            }
            res.add(list);
            return;
        }

        for (int j = 0; j < n; j++) {
            if (isValid(board, pos, j)) {
                board[pos][j] = true;
                backtrack(res, board, pos + 1);
                board[pos][j] = false;
            }
        }
    }

    private boolean isValid(boolean[][] board, int r, int c) {
        int n = board.length;
        for (int j = 0; j < n; j++) if (board[r][j]) return false;

        for (int i = 0; i < n; i++) if (board[i][c]) return false;

        int x = r, y = c;
        while (x >= 0 && y >= 0) if (board[x--][y--]) return false;

        x = r;
        y = c;
        while (x < n && y < n) if (board[x++][y++]) return false;

        x = r;
        y = c;
        while (x >= 0 && y < n) if (board[x--][y++]) return false;

        x = r;
        y = c;
        while (x < n && y >= 0) if (board[x++][y--]) return false;

        return true;
    }
```

### #3 Short Code
```java
    boolean[] rows, cols, hills, dales, board[];
    List<List<String>> res = new ArrayList<>();

    // TC: O(n!), SC: O(n^2)
    public List<List<String>> solveNQueens(int n) {
        board = new boolean[n][n];
        rows = new boolean[n];
        cols = new boolean[n];
        hills = new boolean[n + n - 1];
        dales = new boolean[n + n - 1];
        dfs(n);
        return res;
    }

    private void dfs(int n) {
        if (n == 0) {
            List<String> list = new ArrayList<>();
            for (int i = 0; i < board.length; i++) {
                StringBuilder sb = new StringBuilder();
                for (int j = 0; j < board.length; j++) sb.append(board[i][j] ? 'Q' : '.');
                list.add(sb.toString());
            }
            res.add(list);
            return;
        }

        int row = n - 1;
        for (int j = 0; j < board.length; j++) {
            if (rows[row] || cols[j] || hills[row + j] || dales[row - j + board.length - 1]) continue;

            updatePos(row, j, true);
            dfs(n - 1);
            updatePos(row, j, false);
        }
    }

    private void updatePos(int r, int c, boolean status) {
        int n = board.length;
        board[r][c] = status;
        rows[r] = status;
        cols[c] = status;
        hills[r + c] = status;
        dales[r - c + n - 1] = status;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Sudoku Solver
## Intro
https://leetcode.com/problems/sudoku-solver/
![[Pasted image 20220321142738.png]]
![[Pasted image 20220321142810.png]]
![[Pasted image 20220321142816.png]]
## Thoughts
- N/A

## Solutions
### #1 LC Solution

```java
// TC: O(9^(m*n)), SC: O(m*n)
public class P37_SudokuSolver_LC_Solution {
    char[][] board;
    // n is box size, N is row or col size
    int n = 3, N = n * n;
    int[][] rows = new int[N][N + 1];
    int[][] cols = new int[N][N + 1];
    int[][] boxes = new int[N][N + 1];
    boolean sudokuSolved = false;

    public void solveSudoku(char[][] board) {
        this.board = board;

        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                char num = board[i][j];
                if (num != '.') {
                    int d = Character.getNumericValue(num);
                    placeNumber(d, i, j);
                }
            }
        }

        backtrack(0, 0);
    }

    private void placeNumber(int d, int row, int col) {
        int id = getBoxId(row, col);

        rows[row][d]++;
        cols[col][d]++;
        boxes[id][d]++;
        board[row][col] = (char) (d + '0');
    }

    public void backtrack(int row, int col) {
        if (board[row][col] == '.') {
            for (int d = 1; d <= 9; d++) {
                if (couldPlace(d, row, col)) {
                    placeNumber(d, row, col);
                    placeNextNumbers(row, col);

                    // if its solved, there is no need to backtrack, since the single
                    // unique solution is promised
                    if (!sudokuSolved) removeNumber(d, row, col);
                }
            }
        } else placeNextNumbers(row, col);
    }

    private boolean couldPlace(int d, int row, int col) {
        int id = getBoxId(row, col);
        return rows[row][d] + cols[col][d] + boxes[id][d] == 0;
    }

    private void placeNextNumbers(int row, int col) {
        if (col == N - 1 && row == N - 1) sudokuSolved = true;
        else {
            if (col == N - 1) backtrack(row + 1, 0);
            else backtrack(row, col + 1);
        }
    }

    private void removeNumber(int d, int row, int col) {
        int id = getBoxId(row, col);
        rows[row][d]--;
        cols[col][d]--;
        boxes[id][d]--;
        board[row][col] = '.';
    }

    private int getBoxId(int row, int col) {
        return (row / n) * n + col / n;
    }
}
```

### #2 LC Shorter
```java
// TC: O(9^(m*n)), SC: O(m*n)
public class P37_SudokuSolver_Shorter_1 {
    int rows, cols;

    public void solveSudoku(char[][] board) {
        this.rows = board.length;
        this.cols = board[0].length;
        solve(board);
    }

    private boolean solve(char[][] board) {
        for (int row = 0; row < rows; row++) {
            for (int col = 0; col < cols; col++) if (board[row][col] == '.') return canBeSolved(board, row, col);
        }
        return true;
    }

    private boolean canBeSolved(char[][] board, int row, int col) {
        for (char c = '1'; c <= '9'; c++) {
            if (isValid(board, row, col, c)) {
                board[row][col] = c;
                if (solve(board)) return true;
                board[row][col] = '.';
            }
        }
        return false;
    }

    private boolean isValid(char[][] board, int row, int col, char c) {
        if (row > rows - 1 || col > cols - 1) return false;

        // if already present in row, col or subgrid, return false
        for (int i = 0; i < 9; i++) if (board[row][i] == c) return false;
        for (int i = 0; i < 9; i++) if (board[i][col] == c) return false;

        // row,col = 7,4, numOfGrid = 3, rowSubGridId = 2, colSubGridId = 1
        int boxes = rows / 3, rowSubGridId = row / boxes, colSubGridId = col / boxes;
        for (int i = rowSubGridId * boxes; i < rowSubGridId * boxes + boxes; i++) {
            for (int j = colSubGridId * boxes; j < colSubGridId * boxes + boxes; j++) {
                if (board[i][j] == c) return false;
            }
        }
        return true;
    }
}
```

### #3 Shorter Mine
```java
// TC: O(9^(m*n)), SC: O(m*n)
public class P37_SudokuSolver_Shorter_Better {
    char[][] board;
    boolean[][] rows, cols, boxes;

    public void solveSudoku(char[][] board) {
        this.board = board;
        rows = new boolean[9][9];
        cols = new boolean[9][9];
        boxes = new boolean[9][9];

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) if (board[i][j] != '.') place(i, j, board[i][j]);
        }

        solve();
    }

    private boolean solve() {
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) if (board[i][j] == '.') return canBeSolved(i, j);
        }
        return true;
    }

    private boolean canBeSolved(int x, int y) {
        for (char c = '1'; c <= '9'; c++) {
            if (isValid(x, y, c)) {
                place(x, y, c);
                if (solve()) return true;
                remove(x, y, c);
            }
        }
        return false;
    }

    private void place(int r, int c, char ch) {
        int index = ch - '1', boxId = getBoxId(r, c);
        rows[r][index] = true;
        cols[c][index] = true;
        boxes[boxId][index] = true;
        board[r][c] = ch;
    }

    private void remove(int r, int c, char ch) {
        int index = ch - '1', boxId = getBoxId(r, c);
        rows[r][index] = false;
        cols[c][index] = false;
        boxes[boxId][index] = false;
        board[r][c] = '.';
    }

    private boolean isValid(int r, int c, char ch) {
        int index = ch - '1', boxId = getBoxId(r, c);
        return !rows[r][index] && !cols[c][index] && !boxes[boxId][index];
    }

    private int getBoxId(int r, int c) {
        return (r / 3) * 3 + c / 3;
    }
}
```

## Common Pitfalls to Avoid
- N/A

---