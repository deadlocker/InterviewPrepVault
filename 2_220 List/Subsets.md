
# Problem Name
## Intro
https://leetcode.com/problems/subsets/
![[Pasted image 20220322093926.png]]

## Thoughts
- N/A

## Solutions
### #1 Subsets Cascading

```java
    // TC: O(2^n * n), SC: O(2^n * n)
    public List<List<Integer>> subsets_Cascading(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        res.add(new ArrayList<>());
        for (int num : nums) {
            int size = res.size();
            for (int j = 0; j < size; j++) {
                List<Integer> temp = new ArrayList<>(res.get(j));
                temp.add(num);
                res.add(temp);
            }
        }
        return res;
    }
```

### #2 Bit Manipulation
```java
    // TC: O(2^n * n), SC: O(2^n * n)
    public List<List<Integer>> subsets_BitManipulation(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        // instead of math.pow also u can use 1 << nums.length
        for (int i = 0; i < Math.pow(2, nums.length); i++) {
            List<Integer> list = new ArrayList<>();
            for (int n = i, j = 0; n != 0; n = n >> 1, j++) if ((n & 1) == 1) list.add(nums[j]);
            res.add(list);
        }
        return res;
    }
```

### #3 Backtracking Approach
```java
    // TC: O(2^n * n), SC: O(2^n * n)
    public List<List<Integer>> subsets_BacktrackingApproach(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        backtrack_extraSpace(res, new ArrayList<>(), nums, 0);
        return res;
    }

    private void backtrack_extraSpace(List<List<Integer>> res, List<Integer> prev, int[] nums, int i) {
        if (i == nums.length) {
            res.add(prev);
            return;
        }

        List<Integer> withoutCurrentElem = new ArrayList<>(prev);
        backtrack_extraSpace(res, withoutCurrentElem, nums, i + 1);

        List<Integer> withCurrentElem = new ArrayList<>(prev);
        withCurrentElem.add(nums[i]);
        backtrack_extraSpace(res, withCurrentElem, nums, i + 1);
    }
```

### #4 Stack
```java
    // TC: O(2^n * n), SC: O(2^n * n)
    public List<List<Integer>> subsets_UsingStack(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        Stack<Pair<Integer, List<Integer>>> stack = new Stack<>();
        stack.add(new Pair<>(0, new ArrayList<>()));
        while (!stack.isEmpty()) {
            Pair<Integer, List<Integer>> pair = stack.pop();
            int i = pair.getKey();
            List<Integer> prev = pair.getValue();

            if (i == nums.length) {
                res.add(prev);
                continue;
            }

            List<Integer> withoutCurrentElem = new ArrayList<>(prev);
            stack.add(new Pair<>(i + 1, withoutCurrentElem));

            List<Integer> withCurrentElem = new ArrayList<>(prev);
            withCurrentElem.add(nums[i]);
            stack.add(new Pair<>(i + 1, withCurrentElem));
        }
        return res;
    }
```

### #5 DFS Without Extra ArrayList
```java
    // TC: O(2^n * n), SC: O(2^n * n)
    public List<List<Integer>> subsets_DFS_WithoutExtraArrayList(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        backtrack_noExtraArrayList(res, new ArrayList<>(), nums, 0);
        return res;
    }

    private void backtrack_noExtraArrayList(List<List<Integer>> res, List<Integer> prev, int[] nums, int i) {
        if (i == nums.length) {
            res.add(new ArrayList<>(prev));
            return;
        }

        backtrack_noExtraArrayList(res, prev, nums, i + 1);

        prev.add(nums[i]);
        backtrack_noExtraArrayList(res, prev, nums, i + 1);
        prev.remove(prev.size() - 1);
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Subsets II
## Intro
https://leetcode.com/problems/subsets-ii/
![[Pasted image 20220322094140.png]]

## Thoughts
- N/A

## Solutions
### #1 Inefficient BruteForce

```java
    public List<List<Integer>> subsetsWithDup_Very_Inefficient(int[] nums) {
        Set<Container> res = new HashSet<>(Collections.singletonList(new Container(new ArrayList<>())));
        for (int num : nums) {
            Set<Container> temp = new HashSet<>();
            for (Container c : res) {
                Container newC = new Container(c.data);
                newC.data.add(num);
                temp.add(newC);
            }
            res.addAll(temp);
        }

        return res.stream().map(c -> new ArrayList<>(c.data)).collect(Collectors.toList());
    }

    static class Container {
        List<Integer> data;

        public Container(List<Integer> data) {
            this.data = new ArrayList<>(data);
        }

        @Override
        public int hashCode() {
            int sum = 0;
            for (int num : data) sum += num;
            return sum;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;

            if (o == null || this.getClass() != o.getClass()) return false;

            Container other = (Container) o;
            List<Integer> l1 = data;
            List<Integer> l2 = other.data;

            if (l1.size() != l2.size()) return false;
            Collections.sort(l1);
            Collections.sort(l2);

            return l1.equals(l2);
        }
```

### #2 DFS Kill Right
```java
    public List<List<Integer>> subsetsWithDup_DFS_KillRight(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        helper_killRight(res, new ArrayList<>(), nums, 0, false);
        return res;
    }

    public void helper_killRight(List<List<Integer>> res, List<Integer> ls, int[] nums, int i, boolean choosePrev) {
        if (i == nums.length) {
            res.add(new ArrayList<>(ls));
            return;
        }
        helper_killRight(res, ls, nums, i + 1, false);
        if (i >= 1 && nums[i] == nums[i - 1] && !choosePrev) return;
        ls.add(nums[i]);
        helper_killRight(res, ls, nums, i + 1, true);
        ls.remove(ls.size() - 1);
    }
```

### #3 DFS Kill Left
```java
    public List<List<Integer>> subsetsWithDup_DFS_KillLeft(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        helper_killLeft(res, new ArrayList<>(), nums, 0, false);
        return res;
    }

    public void helper_killLeft(List<List<Integer>> res, List<Integer> ls, int[] nums, int i, boolean choosePrev) {
        if (i == nums.length) {
            res.add(new ArrayList<>(ls));
            return;
        }

        ls.add(nums[i]);
        helper_killLeft(res, ls, nums, i + 1, true);
        ls.remove(ls.size() - 1);

        // If the previous number is same as current number i.e., if nums[i - 1] == nums[i],
        // and if previous number was chosen(i.e., choosePrev is true),
        // then dont re-include the current number because it would then be a duplicate
        if (i >= 1 && nums[i] == nums[i - 1] && choosePrev) return;

        helper_killLeft(res, ls, nums, i + 1, false);
    }
```

### #4 N Branches
```java
    public List<List<Integer>> subsetsWithDup_NBranches(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        helper(res, new ArrayList<>(), nums, 0);
        return res;
    }

    private void helper(List<List<Integer>> res, List<Integer> list, int[] nums, int pos) {
        res.add(new ArrayList<>(list));
        for (int i = pos; i < nums.length; i++) {
            if (i > pos && nums[i] == nums[i - 1]) continue;

            list.add(nums[i]);
            helper(res, list, nums, i + 1);
            list.remove(list.size() - 1);
        }
    }
```

### #5 Iterative Grokking Approach
```java
    // TC: O(n*2^n), SC: O(n*2^n)
    public List<List<Integer>> subsetsWithDup_Iterative_Grokking(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        res.add(new ArrayList<>());
        Arrays.sort(nums);
        int start, end = 0;
        for (int i = 0; i < nums.length; i++) {
            // if current num is same as previous, then only add elements to subsets from previous iteration
            start = i > 0 && nums[i] == nums[i - 1] ? end + 1 : 0;
            end = res.size() - 1;
            for (int j = start; j <= end; j++) {
                List<Integer> list = new ArrayList<>(res.get(j));
                list.add(nums[i]);
                res.add(list);
            }
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Permutations
## Intro
https://leetcode.com/problems/permutations/
![[Pasted image 20220322094353.png]]
## Thoughts
- N/A

## Solutions
### #1 BFS

```java
    // TC: O(n * n!), SC: O(n * n!)
    public List<List<Integer>> permute_BFS(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        Queue<List<Integer>> q = new LinkedList<>();
        q.offer(new ArrayList<>());
        for (int num : nums) {
            int n = q.size();
            for (int i = 0; i < n; i++) {
                List<Integer> oldList = q.poll();
                for (int j = 0; j <= oldList.size(); j++) {
                    List<Integer> newList = new ArrayList<>(oldList);
                    newList.add(j, num);
                    if (newList.size() == nums.length) res.add(newList);
                    else q.offer(newList);
                }
            }
        }
        return res;
    }
```

### #2 DFS
```java
    // TC: O(n * n!), SC: O(n * n!)
    public List<List<Integer>> permute_DFS_AnotherWay(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        dfs(nums, 0, new ArrayList<>(), res);
        return res;
    }

    private void dfs(int[] nums, int i, List<Integer> list, List<List<Integer>> res) {
        if (i == nums.length) {
            res.add(list);
            return;
        }

        for (int j = 0; j <= list.size(); j++) {
            List<Integer> newList = new ArrayList<>(list);
            newList.add(j, nums[i]);
            dfs(nums, i + 1, newList, res);
        }
    }
```

### #3 Backtrack LC
```java
    // TC: O(n * n!), SC: O(n!)
    public List<List<Integer>> permute_LC_Way(int[] arr) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> nums = new ArrayList<>();
        for (int num : arr) nums.add(num);

        backtrack(nums, res, 0);

        return res;
    }

    private void backtrack(List<Integer> nums, List<List<Integer>> res, int first) {
        if (first == nums.size()) {
            res.add(new ArrayList<>(nums));
        }

        for (int i = first; i < nums.size(); i++) {
            // place ith integer first in the current permutation
            Collections.swap(nums, i, first);

            backtrack(nums, res, first + 1);

            Collections.swap(nums, i, first);
        }
    }
```

### #4 DFS My Way
```java
    // TC: O(n * n!), SC: O(n!)
    public List<List<Integer>> permute_MyWay(int[] A) {
        List<List<Integer>> res = new ArrayList<>();
        helper(A, res, 0, new ArrayList<>());
        return res;
    }

    private void helper(int[] A, List<List<Integer>> res, int pos, List<Integer> list) {
        if (pos == A.length) {
            res.add(new ArrayList<>(list));
            return;
        }

        for (int i = pos; i < A.length; i++) {
            swap(A, pos, i);
            list.add(A[pos]);
            helper(A, res, pos + 1, list);
            list.remove(list.size() - 1);
            swap(A, pos, i);
        }
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Letter Case Permutation
## Intro
https://leetcode.com/problems/letter-case-permutation/
![[Pasted image 20220322094526.png]]

## Thoughts
- N/A

## Solutions
### #1 Recursive My Way

```java
    // TC: O(2^n * n), SC: O(2^n * n) ???
    public List<String> letterCasePermutation_Recursive_MyWay(String s) {
        List<String> res = new ArrayList<>();
        dfs(s, "", 0, res);
        return res;
    }

    private void dfs(String s, String prevS, int i, List<String> res) {
        if (i == s.length()) {
            res.add(prevS);
            return;
        }

        char c = s.charAt(i);
        if (Character.isDigit(c)) {
            dfs(s, prevS + c, i + 1, res);
            return;
        }
        char lc = Character.toLowerCase(c);
        char uc = Character.toUpperCase(c);


        dfs(s, prevS + lc, i + 1, res);
        dfs(s, prevS + uc, i + 1, res);
    }
```

### #2 Iterative Stack
```java
    // TC: O(2^n * n), SC: O(2^n * n) ???
    public List<String> letterCasePermutation_Iterative_UsingStack(String s) {
        List<String> res = new ArrayList<>();
        Stack<Pair<String, Integer>> stack = new Stack<>();
        stack.add(new Pair<>("", 0));
        while (!stack.isEmpty()) {
            Pair<String, Integer> pair = stack.pop();
            String prevS = pair.getKey();
            int i = pair.getValue();
            if (i == s.length()) {
                res.add(prevS);
                continue;
            }

            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                stack.add(new Pair<>(prevS + c, i + 1));
            } else {
                char lc = Character.toLowerCase(c);
                char uc = Character.toUpperCase(c);
                stack.add(new Pair<>(prevS + lc, i + 1));
                stack.add(new Pair<>(prevS + uc, i + 1));
            }
        }
        return res;
    }
```

### #3 StringBuilder Approach
```java
    // TC: O(2^n * n), SC: O(2^n * n)
    public List<String> letterCasePermutation_LC_StringBuilderApproach(String s) {
        List<StringBuilder> res = new ArrayList<>();
        res.add(new StringBuilder());

        for (char c : s.toCharArray()) {
            int n = res.size();
            if (Character.isLetter(c)) {
                for (int i = 0; i < n; i++) {
                    res.add(new StringBuilder(res.get(i)));
                    res.get(i).append(Character.toLowerCase(c));
                    res.get(n + i).append(Character.toUpperCase(c));
                }
            } else {
                for (StringBuilder re : res) re.append(c);
            }

        }

        List<String> finalAns = new ArrayList<>();
        res.forEach(sb -> finalAns.add(sb.toString()));
        return finalAns;
    }
```

### #4 StringBuilder Recursive
```java
    // TC: O(2^n * n), SC: O(2^n * n)
    public List<String> letterCasePermutation_Recursive_StringBuilder(String s) {
        List<String> res = new ArrayList<>();
        dfs(s, new StringBuilder(), 0, res);
        return res;
    }

    private void dfs(String s, StringBuilder prevS, int i, List<String> res) {
        if (i == s.length()) {
            res.add(prevS.toString());
            return;
        }

        char c = s.charAt(i);
        if (Character.isDigit(c)) {
            dfs(s, prevS.append(c), i + 1, res);
            prevS.deleteCharAt(prevS.length() - 1);
            return;
        }
        char lc = Character.toLowerCase(c);
        char uc = Character.toUpperCase(c);


        dfs(s, prevS.append(lc), i + 1, res);
        prevS.deleteCharAt(prevS.length() - 1);

        dfs(s, prevS.append(uc), i + 1, res);
        prevS.deleteCharAt(prevS.length() - 1);
    }
```

### #5 Bit Enumeration
```java
    // TC: O(2^n * n), SC: O(2^n * n)
    public List<String> letterCasePermutation_Bit_Enumeration(String s) {
        int noOfLetters = 0;
        for (char c : s.toCharArray()) if (Character.isLetter(c)) noOfLetters++;

        List<String> res = new ArrayList<>();

        // instead of math.pow also u can use 1 << noOfLetters
        for (int i = 0; i < Math.pow(2, noOfLetters); i++) {
            int b = 0;
            StringBuilder sb = new StringBuilder();
            for (char c : s.toCharArray()) {
                if (Character.isLetter(c)) {
                    if (((i >> b++) & 1) == 1) sb.append(Character.toLowerCase(c));
                    else sb.append(Character.toUpperCase(c));
                } else sb.append(c);
            }
            res.add(sb.toString());
        }

        return res;
    }
```

### #6 BFS My Way
```java
    // TC: O(n*2^n), SC: O(n*2^n)
    public List<String> letterCasePermutation_BFS_MY_Way(String s) {
        Queue<String> q = new LinkedList<>();
        q.offer("");
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            int size = q.size();
            for (int j = 0; j < size; j++) {
                String cur = q.poll();
                if (Character.isDigit(c)) q.offer(cur + c);
                else {
                    q.offer(cur + Character.toLowerCase(c));
                    q.offer(cur + Character.toUpperCase(c));
                }
            }
        }
        return new LinkedList<>(q);
    }
```

### #7 Grokking Iterative
```java
    // TC: O(n*2^n), SC: O(n*2^n)
    public List<String> letterCasePermutation_Grokking(String s) {
        List<String> res = new ArrayList<>();
        res.add(s);

        for (int i = 0; i < s.length(); i++) {
            if (Character.isDigit(s.charAt(i))) continue;
            int n = res.size();
            for (int j = 0; j < n; j++) {
                char[] chs = res.get(j).toCharArray();
                if (Character.isUpperCase(chs[i])) chs[i] = Character.toLowerCase(chs[i]);
                else chs[i] = Character.toUpperCase(chs[i]);
                res.add(String.valueOf(chs));
            }
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Generate Parentheses
## Intro
https://leetcode.com/problems/generate-parentheses/
![[Pasted image 20220322094911.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java
    // TC: O(n * 2^(2n)), SC: O(n * 2^(2n))
    // for each of 2^(2n) sequences, we need to create and validate the sequence which takes O(n) work, hence the above TC and SC
    public List<String> generateParenthesis_BruteForce(int n) {
        List<String> res = new ArrayList<>();
        generateAll(new char[2 * n], 0, res);
        return res;
    }

    private void generateAll(char[] cur, int start, List<String> res) {
        if (start == cur.length) {
            if (valid(cur)) res.add(new String(cur));
        } else {
            cur[start] = '(';
            generateAll(cur, start + 1, res);
            cur[start] = ')';
            generateAll(cur, start + 1, res);
        }
    }

    private boolean valid(char[] cur) {
        int balance = 0;
        for (char c : cur) {
            // another way to shorten this if-else which works on LC btw
            // balance += c == '(' ? 1 : -1;
            if (c == '(') balance++;
            else balance--;
            if (balance < 0) return false;
        }
        return balance == 0;
    }
```

### #2 Backtracking Approach
```java
    // TC: O(4^n / sqrt(n)), SC: O(4^n / sqrt(n))
    // TC analysis is not explained in LC. Research it. This is also apparently called nth catalan number,
    // which can also be denoted as (1/(n+1)) * 2nCn, C is combinatorial formula.
    // TODO understand what the hell is catalanNumber
    public List<String> generateParenthesis_backtrackingApproach(int n) {
        List<String> res = new ArrayList<>();
        backtrack(res, "", 0, 0, n);
        return res;
    }

    private void backtrack(List<String> res, String cur, int open, int close, int max) {
        if (cur.length() == max * 2) {
            res.add(cur);
            return;
        }

        if (open < max) backtrack(res, cur + "(", open + 1, close, max);
        if (close < open) backtrack(res, cur + ")", open, close + 1, max);
    }
```

### #3 Closure Number
```java
    // TC: O(4^n / sqrt(n)), SC: O(4^n / sqrt(n))
    // TODO understand what the hell is closureNumber
    public List<String> generateParenthesis_ClosureNumber(int n) {
        List<String> res = new ArrayList<>();
        if (n == 0) res.add("");
        else {
            for (int i = 0; i < n; i++) {
                for (String left : generateParenthesis_ClosureNumber(i)) {
                    for (String right : generateParenthesis_ClosureNumber(n - 1 - i))
                        res.add("(" + left + ")" + right);
                }
            }
        }
        return res;
    }
```

### #4 Set Approach
```java
    // TC: O(?), SC: O(?)
    public List<String> generateParenthesis_SetApproach(int n) {
        if (n == 1) return new ArrayList<>(List.of("()"));
        Set<String> set = new HashSet<>();
        for (String str : generateParenthesis_SetApproach(n - 1)) {
            for (int i = 0; i < str.length(); i++) set.add(str.substring(0, i + 1) + "()" + str.substring(i + 1));
        }
        return new ArrayList<>(set);
    }
```

### #5 BFS
```java
    // TC: O(4^n / sqrt(n)), SC: O(4^n / sqrt(n))
    public List<String> generateParenthesis_BFS(int n) {
        List<String> res = new ArrayList<>();
        Queue<ParenthesesItem> q = new LinkedList<>();
        q.offer(new ParenthesesItem("", 0, 0));
        while (!q.isEmpty()) {
            ParenthesesItem item = q.poll();
            if (item.open == n && item.close == n) res.add(item.s);
            else {
                if (item.open < n) q.offer(new ParenthesesItem(item.s + "(", item.open + 1, item.close));
                if (item.open > item.close) q.offer(new ParenthesesItem(item.s + ")", item.open, item.close + 1));
            }
        }
        return res;
    }
```

### #6 DFS
```java
    // TC: O(4^n / sqrt(n)), SC: O(4^n / sqrt(n))
    // note this is similar to backtracking method below
    public List<String> generateParenthesis_DFS(int n) {
        List<String> res = new ArrayList<>();
        List<Character> list = new ArrayList<>();
        dfs(n, res, list, 0, 0);
        return res;
    }

    private void dfs(int n, List<String> res, List<Character> list, int open, int close) {
        if (open == n && close == n) {
            res.add(convert(list));
            return;
        }

        if (open < n) {
            list.add('(');
            dfs(n, res, list, open + 1, close);
            list.remove(list.size() - 1);
        }

        if (open > close) {
            list.add(')');
            dfs(n, res, list, open, close + 1);
            list.remove(list.size() - 1);
        }
    }

    private String convert(List<Character> arr) {
        return arr.stream().map(String::valueOf).collect(Collectors.joining());
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Generalized Abbreviation
## Intro
https://leetcode.com/problems/generalized-abbreviation/
![[Pasted image 20220322095146.png]]

## Thoughts
- N/A

## Solutions
### #1 DFS

```java
    // TC: O(n*2^n), SC: O(n)
    public List<String> generateAbbreviations_DFS(String word) {
        List<String> res = new ArrayList<>();
        backtrack(res, new StringBuilder(), word, 0, 0);
        return res;
    }

    // i is cur position, k is count of consecutive abbreviated characters
    private void backtrack(List<String> res, StringBuilder sb, String word, int i, int k) {
        int len = sb.length();
        if (i == word.length()) {
            if (k != 0) sb.append(k); // append the last k if non-zero
            res.add(sb.toString());
        } else {
            // the branch where word.charAt(i) is abbreviated
            backtrack(res, sb, word, i + 1, k + 1);

            // the branch where word.charAt(i) is not abbreviated and left as it is
            if (k != 0) sb.append(k);
            sb.append(word.charAt(i));
            backtrack(res, sb, word, i + 1, 0);
        }
        sb.setLength(len);
    }
```

### #2 Bit Enumeration
```java
    // TC: O(n*2^n), SC: O(n)
    public List<String> generateAbbreviations_bit(String word) {
        List<String> res = new ArrayList<>();
        for (int x = 0; x < (1 << word.length()); x++) res.add(abbr(word, x));
        return res;
    }

    private String abbr(String word, int x) {
        StringBuilder sb = new StringBuilder();
        int k = 0, n = word.length();
        for (int i = 0; i < n; i++, x >>= 1) {
            if ((x & 1) == 0) {
                if (k != 0) {
                    sb.append(k);
                    k = 0;
                }
                sb.append(word.charAt(i));
            } else k++;
        }
        // dont forget to append the last k if non-zero
        if (k != 0) sb.append(k);
        return sb.toString();
    }
```

### #3 BFS
```java
    // TC: O(n*2^n), SC: O(n)
    public List<String> generateAbbreviations_BFS(String word) {
        List<String> res = new ArrayList<>();
        Queue<Item> q = new LinkedList<>();
        q.offer(new Item("", 0, 0));
        while (!q.isEmpty()) {
            Item item = q.poll();
            String ans = item.s;
            int i = item.i, k = item.k;
            if (i == word.length()) {
                if (k != 0) ans += k;
                res.add(ans);
                continue;
            }
            q.offer(new Item(ans, i + 1, k + 1));

            if (k != 0) ans += k;
            q.offer(new Item(ans + word.charAt(i), i + 1, 0));
        }
        return res;
    }

    @AllArgsConstructor
    static
    class Item {
        String s;
        int i, k;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Different Ways To Add Parentheses
## Intro
https://leetcode.com/problems/different-ways-to-add-parentheses/
![[Pasted image 20220322095249.png]]

## Thoughts
- N/A

## Solutions
### #1 Recursion

```java
    // TC: O(n * 2^n), SC: O(2^n) - estimated upper bound
    // TC: O(4^n / sqrt(n)), SC: O(4^n / sqrt(n)) - tight bound, this is similar to balanced parentheses apparently and is similar to catalan number
    public List<Integer> diffWaysToCompute_Recursion(String s) {
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '-' || c == '+' || c == '*') {
                String a = s.substring(0, i);
                String b = s.substring(i + 1);
                List<Integer> al = diffWaysToCompute_Recursion(a);
                List<Integer> bl = diffWaysToCompute_Recursion(b);
                for (int x : al) {
                    for (int y : bl) {
                        if (c == '-') res.add(x - y);
                        else if (c == '+') res.add(x + y);
                        else res.add(x * y);
                    }
                }
            }
        }
        // base case, if we dont have any operators in our s(say s = 3), then we need to return [3]
        if (res.isEmpty()) res.add(Integer.valueOf(s));
        return res;
    }
```

### #2 Memo
```java
    Map<String, List<Integer>> memo = new HashMap<>();

    // tc probably better than normal non-memoized solution
    public List<Integer> diffWaysToCompute_Memo(String s) {
        if (memo.containsKey(s)) return memo.get(s);

        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '-' || c == '+' || c == '*') {
                String a = s.substring(0, i);
                String b = s.substring(i + 1);
                List<Integer> al = diffWaysToCompute_Memo(a);
                List<Integer> bl = diffWaysToCompute_Memo(b);
                for (int x : al) {
                    for (int y : bl) {
                        if (c == '-') res.add(x - y);
                        else if (c == '+') res.add(x + y);
                        else res.add(x * y);
                    }
                }
            }
        }
        // base case, if we dont have any operators in our s(say s = 3), then we need to return [3]
        if (res.isEmpty()) res.add(Integer.valueOf(s));
        memo.put(s, res);
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Unique Binary Search Trees
## Intro
https://leetcode.com/problems/unique-binary-search-trees/
![[Pasted image 20220322095517.png]]
## Thoughts
- N/A

## Solutions
### #1 DP

```java
    // TC: O(n^2), SC: O(n)
    // check LC solution
    public int numTrees_DP(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            for (int j = 1; j <= i; j++) dp[i] += dp[j - 1] * dp[i - j];
        }
        return dp[n];
    }
```

### #2 Catalan Number
```java
    // TC: O(n), SC: O(1)
    // todo learn catalan derivation
    public int numTrees_CatalanNumber(int n) {
        long c = 1;
        for (int i = 0; i < n; i++) {
            c = 2 * (2L * i + 1) * c / (i + 2);
        }
        return (int) c;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Unique Binary Search Trees II
## Intro
https://leetcode.com/problems/unique-binary-search-trees-ii/
![[Pasted image 20220322095604.png]]

## Thoughts
- N/A

## Solutions
### #1 DFS

```java
    // TC: O(n * 2^n), SC: O(2^n) - estimated upper bound
    // TC: O(4^n / sqrt(n)), SC: O(4^n / sqrt(n)) - tight bound, this is similar to balanced parentheses apparently and is similar to catalan number
    public List<TreeNode> generateTrees(int n) {
        if (n == 0) return new ArrayList<>();
        return generate(1, n);
    }

    private List<TreeNode> generate(int start, int end) {
        List<TreeNode> res = new ArrayList<>();
        if (start > end) {
            res.add(null);
            return res;
        }

        for (int i = start; i <= end; i++) {
            List<TreeNode> leftTrees = generate(start, i - 1);
            List<TreeNode> rightTrees = generate(i + 1, end);

            for (TreeNode l : leftTrees) {
                for (TreeNode r : rightTrees) {
                    TreeNode root = new TreeNode(i);
                    root.left = l;
                    root.right = r;
                    res.add(root);
                }
            }
        }
        return res;
    }
```

## Common Pitfalls to Avoid
- N/A

---