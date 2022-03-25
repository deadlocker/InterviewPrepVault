# Matrix

# [Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)

## Intro

![Untitled](1_Blind75/Matrix%20418b5/Untitled.png)

![Untitled](1_Blind75/Matrix%20418b5/Untitled%201.png)

## Thoughts

- Pretty easy. have 4 handles r1,r2,c1,c2 and keep moving them to get the desired output.

## Solutions

### #1 MyCodeSchool Directions Approach

```java
// TC: O(m*n), SC: O(m*n)
public List<Integer> spiralOrder_directionApproach(int[][] matrix) {
    List<Integer> res = new ArrayList<>();
    int rows = matrix.length, cols = matrix[0].length, dir = 0;
    int rs = 0, re = rows - 1, cs = 0, ce = cols - 1;
    while (rs <= re && cs <= ce) {
        if (dir == 0) {
            for (int c = cs; c <= ce; c++) res.add(matrix[rs][c]);
            rs++;
        } else if (dir == 1) {
            for (int r = rs; r <= re; r++) res.add(matrix[r][ce]);
            ce--;
        } else if (dir == 2) {
            for (int c = ce; c >= cs; c--) res.add(matrix[re][c]);
            re--;
        } else {
            for (int r = re; r >= rs; r--) res.add(matrix[r][cs]);
            cs++;
        }
        dir = (dir + 1) % 4;
    }
    return res;
```

### #2 Non-Dir Approach

```java
// TC: O(m*n), SC: O(m*n)
public List<Integer> spiralOrder(int[][] matrix) {
    int rows = matrix.length, cols = matrix[0].length;
    int rs = 0, re = rows - 1, cs = 0, ce = cols - 1;
    List<Integer> res = new ArrayList<>();
    while (rs <= re && cs <= ce) {
        // top
        for (int c = cs; c <= ce; c++) res.add(matrix[rs][c]);
        // right
        for (int r = rs + 1; r <= re; r++) res.add(matrix[r][ce]);
        if (rs != re && cs != ce) {
            // bottom
            for (int c = ce - 1; c >= cs; c--) res.add(matrix[re][c]);
            // left
            for (int r = re - 1; r >= rs + 1; r--) res.add(matrix[r][cs]);
        }

        rs++;
        re--;
        cs++;
        ce--;
    }
    return res;
}
```

### #3 Recursive Approach(not needed)

```java
// TC: O(m*n), SC: O(m*n)
public List<Integer> spiralOrder_Recursive(int[][] matrix) {
    List<Integer> res = new ArrayList<>();
    int rs = 0, cs = 0, re = matrix.length - 1, ce = matrix[0].length - 1;
    spiralFill(matrix, rs, re, cs, ce, res);
    return res;
}

private static void spiralFill(int[][] arr, int rs, int re, int cs, int ce, List<Integer> res) {
    if (rs > re || cs > ce) return;

    // top
    for (int c = cs; c <= ce; c++) res.add(arr[rs][c]);
    // right
    for (int r = rs + 1; r <= re; r++) res.add(arr[r][ce]);
    if (rs < re && cs < ce) {
        // bottom
        for (int c = ce - 1; c > cs; c--) res.add(arr[re][c]);
        // left
        for (int r = re; r > rs; r--) res.add(arr[r][cs]);
    }

    // recurse
    spiralFill(arr, rs + 1, re - 1, cs + 1, ce - 1, res);
}
```

## Common Pitfalls to Avoid

- In Non-Dir approach, if you dont use the if condition, then it will fail for this input: `[[1,2,3,4],[5,6,7,8],[9,10,11,12]]. Expected: [1,2,3,4,8,12,11,10,9,5,6,7], but returned: [1,2,3,4,8,12,11,10,9,5,6,7,6]`

# [Rotate Image](https://leetcode.com/problems/rotate-image/)

## Intro

![Untitled](1_Blind75/Matrix%20418b5/Untitled%202.png)

![Untitled](1_Blind75/Matrix%20418b5/Untitled%203.png)

## Thoughts

- Extra space is pretty easy, transpose and mirror in single loop and then store it back in orig array.
- Other solutions might be a bit tricky.

## Solutions

### #1 Extra Space

```java
// TC: O(mn), SC: O(mn)
public void rotate_ExtraSpace(int[][] matrix) {
    int rows = matrix.length, cols = matrix[0].length;
    int res[][] = new int[rows][cols];

    // transpose and mirror in one loop
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            res[i][j] = matrix[cols - 1 - j][i];
        }
    }
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            matrix[i][j] = res[i][j];
        }
    }
```

### #2 No Space

```java
// TC: O(mn), SC: O(1)
public void rotate_NoSpace(int[][] matrix) {
    int n = matrix.length;

    // transpose
    for (int i = 0; i < n; i++) {
        for (int j = i; j < n; j++) {
            int temp = matrix[j][i];
            matrix[j][i] = matrix[i][j];
            matrix[i][j] = temp;
        }
    }

    // reverse each row
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n / 2; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[i][n - j - 1];
            matrix[i][n - j - 1] = temp;
        }
    }
}
```

### #3 More Advanced

```java
// TC: O(mn), SC: O(1)
public void rotate_SingleForLoop(int[][] matrix) {
    int n = matrix.length;
    for (int i = 0; i < (n + 1) / 2; i++) {
        for (int j = 0; j < n / 2; j++) {
            int temp = matrix[n - 1 - j][i];
            matrix[n - 1 - j][i] = matrix[n - 1 - i][n - j - 1];
            matrix[n - 1 - i][n - j - 1] = matrix[j][n - 1 - i];
            matrix[j][n - 1 - i] = matrix[i][j];
            matrix[i][j] = temp;
        }
    }
}

// TC: O(mn), SC: O(1)
public void rotate_NoSpace_RotateRectangles(int[][] matrix) {
    int n = matrix.length;

    for (int i = 0; i < n / 2 + (n % 2); i++) {
        for (int j = 0; j < n / 2; j++) {
            int[] temp = new int[4];
            int row = i, col = j;
            for (int k = 0; k < 4; k++) {
                temp[k] = matrix[row][col];
                int x = row;
                row = col;
                col = n - 1 - x;
            }
            for (int k = 0; k < 4; k++) {
                matrix[row][col] = temp[(k + 3) % 4];
                int x = row;
                row = col;
                col = n - 1 - x;
            }
        }
    }
}
```

## Common Pitfalls to Avoid

- N/A, redo this

# [Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)

## Intro

![Untitled](1_Blind75/Matrix%20418b5/Untitled%204.png)

## Thoughts

- Just mark all the rows and cols which need to made zero in two sets(rowSet and colSet) and then finally loop again and mark cells as zero based on rowSet and colSet.
- Followup: how to do without space? Use first row and first col itself as rowSet and colSet. Very clever, might need to memorize.

## Solutions

### #1 With Space

```java
// TC: O(m*n), SC: O(m+n)
public void setZeroes_ExtraSpace(int[][] matrix) {
    int rows = matrix.length, cols = matrix[0].length;
    Set<Integer> rowsSet = new HashSet<>(), colsSet = new HashSet<>();

    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (matrix[i][j] == 0) {
                rowsSet.add(i);
                colsSet.add(j);
            }
        }
    }

    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (rowsSet.contains(i) || colsSet.contains(j)) matrix[i][j] = 0;
        }
    }
}
```

### #2 Without Space

```java
// TC: O(mn), SC: O(1)
public void setZeroes_NoSpace(int[][] matrix) {
    int rows = matrix.length, cols = matrix[0].length;
    boolean isCol = false;

    for (int i = 0; i < rows; i++) {
        if (matrix[i][0] == 0) isCol = true;
        for (int j = 1; j < cols; j++) {
            if (matrix[i][j] == 0) {
                matrix[i][0] = 0;
                matrix[0][j] = 0;
            }
        }
    }

    for (int i = 1; i < rows; i++) {
        for (int j = 1; j < cols; j++) {
            if (matrix[i][0] == 0 || matrix[0][j] == 0) matrix[i][j] = 0;
        }
    }

    // see if first row needs to be set to zero as well
    if (matrix[0][0] == 0) {
        for (int j = 0; j < cols; j++) matrix[0][j] = 0;
    }

    // see if first col needs to be set to zero as well
    if (isCol) {
        for (int i = 0; i < rows; i++) matrix[i][0] = 0;
    }
}
```

## Common Pitfalls to Avoid

- N/A