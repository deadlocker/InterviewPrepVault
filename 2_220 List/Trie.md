
# Design Search Autocomplete System
## Intro
https://leetcode.com/problems/design-search-autocomplete-system/
![[Pasted image 20220321143558.png]]
![[Pasted image 20220321143606.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java

    Map<String, Integer> map = new HashMap<>();
    StringBuilder sb = new StringBuilder();

    // TC: O(k*l), SC: O(k*l), k is avg sentence length, l is no.of sentences.
    // map.put is O(1), but to create hash value for a sentence string, it must be traversed at least once apparently.
    // therefore we have a factor of k.
    public P642_DesignSearchAutoCompleteSystem_BruteForce(String[] sentences, int[] times) {
        for (int i = 0; i < sentences.length; i++) map.put(sentences[i], times[i]);
    }

    // TC: O(n + mlogm), SC: O(m?), SC coz of List<Node>
    // n is map.keyset length. m is the no.of elements in list we are about to sort below.
    public List<String> input(char c) {
        List<String> res = new ArrayList<>();
        if (c == '#') {
            String word = sb.toString();
            map.put(word, map.getOrDefault(word, 0) + 1);
            sb.setLength(0);
        } else {
            List<Node> list = new ArrayList<>();
            sb.append(c);
            map.forEach((sentence, times) -> {
                String word = sb.toString();
                if (sentence.startsWith(word)) list.add(new Node(sentence, times));
            });

            list.sort((a, b) -> a.times == b.times ? a.sentence.compareTo(b.sentence) : b.times - a.times);
            for (int i = 0; i < min(3, list.size()); i++) res.add(list.get(i).sentence);
        }
        return res;
    }

    @AllArgsConstructor
    static class Node {
        String sentence;
        int times;
    }
```

### #2 LC Solution
```java

    TrieNode root = new TrieNode();
    StringBuilder sb = new StringBuilder();

    // TC: O(k*l), SC: O(k*l), k is avg length of sentence, l is no.of sentences
    public P642_DesignSearchAutoCompleteSystem_LC_Solution(String[] sentences, int[] times) {
        for (int i = 0; i < sentences.length; i++) insert(sentences[i], times[i]);
    }

    // TC: O(p + q + m log m), SC: O(m?), SC coz of List<Node>, p is length of sentence formed till now,
    // q is no.of nodes in trie considering the sentence formed till now, m is no.of elements we are about to sort.
    public List<String> input(char c) {
        List<String> res = new ArrayList<>();
        if (c == '#') {
            insert(sb.toString(), 1);
            sb = new StringBuilder();
        } else {
            sb.append(c);
            List<Node> list = lookup(sb.toString());
            list.sort((a, b) -> a.times == b.times ? a.sentence.compareTo(b.sentence) : b.times - a.times);
            for (int i = 0; i < min(3, list.size()); i++) res.add(list.get(i).sentence);
        }
        return res;
    }

    private List<Node> lookup(String s) {
        TrieNode cur = root;
        List<Node> list = new ArrayList<>();
        for (char c : s.toCharArray()) {
            int index = toInt(c);
            if (cur.children[index] == null) return list;
            cur = cur.children[index];
        }
        traverse(cur, s, list);
        return list;
    }

    private void traverse(TrieNode cur, String s, List<Node> list) {
        if (cur.times > 0) list.add(new Node(s, cur.times));
        for (char i = 'a'; i <= 'z'; i++) {
            int index = i - 'a';
            if (cur.children[index] != null) traverse(cur.children[index], s + i, list);
        }
        if (cur.children[26] != null) traverse(cur.children[26], s + ' ', list);
    }

    private void insert(String s, int times) {
        TrieNode cur = root;
        for (int i = 0; i < s.length(); i++) {
            int index = toInt(s.charAt(i));
            if (cur.children[index] == null) cur.children[index] = new TrieNode();
            cur = cur.children[index];
        }
        cur.times += times;
    }

    private int toInt(char c) {
        return c == ' ' ? 26 : c - 'a';
    }

    static class TrieNode {
        int times;
        TrieNode[] children = new TrieNode[27];
    }

    @AllArgsConstructor
    static class Node {
        String sentence;
        int times;
    }
```

### #3 Trie My Way
```java

    TrieNode root = new TrieNode();
    StringBuilder sb = new StringBuilder();

    // TC: O(k*l), SC: O(k*l), k is avg length of sentence, l is no.of sentences
    public P642_DesignSearchAutoCompleteSystem_Trie_MyWay(String[] sentences, int[] times) {
        for (int i = 0; i < times.length; i++) insert(sentences[i], times[i]);
    }

    // TC: O(p + q + m log m), SC: O(m?), SC coz of List<Node>, p is length of sentence formed till now,
    // q is no.of nodes in trie considering the sentence formed till now, m is no.of elements we are about to sort.
    public List<String> input(char c) {
        List<String> res = new ArrayList<>();
        if (c == '#') {
            insert(sb.toString(), 1);
            sb.setLength(0);
        } else {
            sb.append(c);
            List<Node> list = lookup(sb.toString());
            list.sort((a, b) -> a.times == b.times ? a.sentence.compareTo(b.sentence) : b.times - a.times);
            for (int i = 0; i < min(3, list.size()); i++) res.add(list.get(i).sentence);
        }
        return res;
    }

    private List<Node> lookup(String prefix) {
        TrieNode cur = root;
        List<Node> res = new ArrayList<>();
        for (char c : prefix.toCharArray()) {
            cur = cur.children.get(c);
            if (cur == null) return res;
        }
        traverse(res, cur, new StringBuilder(prefix));
        return res;
    }

    private void traverse(List<Node> res, TrieNode cur, StringBuilder sb) {
        if (cur.times > 0) res.add(new Node(sb.toString(), cur.times));

        cur.children.forEach((c, tNode) -> {
            sb.append(c);
            traverse(res, tNode, sb);
            sb.deleteCharAt(sb.length() - 1);
        });
    }

    private void insert(String word, int times) {
        TrieNode cur = root;
        for (char c : word.toCharArray()) cur = cur.children.computeIfAbsent(c, k -> new TrieNode());
        cur.times += times;
    }

    static class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        int times;
    }

    @AllArgsConstructor
    static class Node {
        String sentence;
        int times;
    }
```

### #4 Trie PQ Approach
```java

    TrieNode root = new TrieNode();
    StringBuilder sb = new StringBuilder();

    public P642_DesignSearchAutoCompleteSystem_Trie_PQApproach(String[] sentences, int[] times) {
        for (int i = 0; i < sentences.length; i++) insert(sentences[i], times[i]);
    }

    public List<String> input(char c) {
        List<String> res = new ArrayList<>();
        if (c == '#') {
            insert(sb.toString(), 1);
            sb.setLength(0);
        } else {
            sb.append(c);
            TrieNode cur = root;
            for (int i = 0; i < sb.length(); i++) {
                char ch = sb.charAt(i);
                cur = cur.children.get(ch);
                if (cur == null) return new ArrayList<>();
            }

            Queue<Pair<String, Integer>> pq = new PriorityQueue<>(
                    (a, b) -> (Objects.equals(a.getValue(), b.getValue())
                            ? a.getKey().compareTo(b.getKey())
                            : b.getValue() - a.getValue())
            );
            cur.counts.forEach((s, count) -> pq.offer(new Pair<>(s, count)));

            for (int i = 0; i < 3 && !pq.isEmpty(); i++) res.add(pq.poll().getKey());
        }
        return res;
    }

    private void insert(String s, int count) {
        TrieNode cur = root;
        for (char c : s.toCharArray()) {
            cur = cur.children.computeIfAbsent(c, k -> new TrieNode());
            cur.counts.put(s, cur.counts.getOrDefault(s, 0) + count);
        }
    }

    static class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        Map<String, Integer> counts = new HashMap<>();
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Palidrome Pairs
## Intro
https://leetcode.com/problems/palindrome-pairs/
![[Pasted image 20220321143747.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java
    // TC: O(k^2 * n), SC: O(kn + k^2), k is no.of words, n is avg length of word
    public List<List<Integer>> palindromePairs_BruteForce(String[] words) {
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < words.length; i++) {
            for (int j = 0; j < words.length; j++) {
                if (i == j) continue;
                String combined = words[i].concat(words[j]);
                String reversed = new StringBuilder(combined).reverse().toString();
                if (combined.equals(reversed)) res.add(Arrays.asList(i, j));
            }
        }
        return res;
    }
```

### #2 BruteForce Better
```java
    // TC: O(k^2 * n), SC: O(kn + k^2), k is no.of words, n is avg length of word
    public List<List<Integer>> palindromePairs_BruteForce_Better(String[] words) {
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < words.length; i++) {
            for (int j = i + 1; j < words.length; j++) {
                String word1 = words[i], word2 = words[j];
                String first = word1 + word2, second = word2 + word1;
                if (isPalindrome(first)) res.add(new ArrayList<>(Arrays.asList(i, j)));
                if (isPalindrome(second)) res.add(new ArrayList<>(Arrays.asList(j, i)));
            }
        }
        return res;
    }

    private boolean isPalindrome(String s) {
        int i = 0, j = s.length() - 1;
        while (i < j) {
            if (s.charAt(i++) != s.charAt(j--)) return false;
        }
        return true;
    }
```

### #3 HashMap Approach
```java
    // TC: O(k^2 * n), SC: O((k + n)^2)
    // n is no.of words and k is length of longest word
    public List<List<Integer>> palindromePairs_HashMapApproach(String[] words) {
        Map<String, Integer> map = new HashMap<>();
        for (int i = 0; i < words.length; i++) map.put(words[i], i);

        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            String reversedWord = new StringBuilder(word).reverse().toString();

            // case #1, cat -> tac, i.e., word and reversedWord are of same length
            if (map.containsKey(reversedWord) && map.get(reversedWord) != i)
                res.add(Arrays.asList(i, map.get(reversedWord)));

            // case #2: [x, abax]
            res.addAll(validIndices(i, allValidSuffixes(word), map, true));

            // case #3: [x, xaba]
            res.addAll(validIndices(i, allValidPrefixes(word), map, false));
        }
        return res;
    }

    private List<List<Integer>> validIndices(int i, List<String> affixes, Map<String, Integer> map, boolean reversed) {
        List<List<Integer>> res = new ArrayList<>();
        for (String affix : affixes) {
            String reversedAffix = new StringBuilder(affix).reverse().toString();
            if (map.containsKey(reversedAffix)) {
                res.add(reversed ? Arrays.asList(map.get(reversedAffix), i) : Arrays.asList(i, map.get(reversedAffix)));
            }
        }
        return res;
    }

    private List<String> allValidPrefixes(String s) {
        List<String> validPrefixes = new ArrayList<>();
        for (int i = 0; i < s.length(); i++) {
            if (isPalindromeBetween(s, i, s.length() - 1)) validPrefixes.add(s.substring(0, i));
        }
        return validPrefixes;
    }

    private List<String> allValidSuffixes(String s) {
        List<String> validSuffixes = new ArrayList<>();
        for (int i = 0; i < s.length(); i++) {
            if (isPalindromeBetween(s, 0, i)) validSuffixes.add(s.substring(i + 1, s.length()));
        }
        return validSuffixes;
    }

    private boolean isPalindromeBetween(String s, int i, int j) {
        while (i < j) if (s.charAt(i++) != s.charAt(j--)) return false;
        return true;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Prefix and Suffix Search
## Intro
https://leetcode.com/problems/prefix-and-suffix-search/
![[Pasted image 20220321143910.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java
    List<String> list = new ArrayList<>();

    // TC: O(n), SC: O(n), n is no.of words
    public P745_PrefixAndSuffixSearch_BruteForce(String[] words) {
        list.addAll(Arrays.asList(words));
    }

    // TC: O(n * l^2), SC: O(1), l is the average word length. TC of l^2 because of startsWith and endsWith methods
    public int f(String prefix, String suffix) {
        int ans = 0;
        for (int i = 0; i < list.size(); i++) {
            String word = list.get(i);
            if (word.startsWith(prefix) && word.endsWith(suffix)) ans = i;
        }
        return ans;
    }
```

### #2 Trie Paired
```java

    TrieNode root = new TrieNode();

    public P745_PrefixAndSuffixSearch_Trie_Paired(String[] words) {
        for (int k = 0, w = 0; k < words.length; k++, w++) {
            String word = words[k];
            TrieNode cur = root;
            cur.weight = w;
            int L = word.length();
            for (int i = 0; i < L; i++) {
                TrieNode tmp = cur;
                for (int j = i; j < L; j++) tmp = insert(tmp, (word.charAt(j) - '`') * 27, w);

                tmp = cur;
                for (int j = L - 1 - i; j >= 0; j--) tmp = insert(tmp, word.charAt(j) - '`', w);

                cur = insert(cur, (word.charAt(i) - '`') * 27 + (word.charAt(L - 1 - i) - '`'), w);
            }
        }
    }

    private TrieNode insert(TrieNode cur, int code, int w) {
        cur = cur.children.computeIfAbsent(code, k -> new TrieNode());
        cur.weight = w;
        return cur;
    }

    // TC: O(nk^2 + qk), SC: O(nk^2), n is no.of words, k is max length of word, q is no.of queries
    // TODO how the heck does this work?
    public int f(String prefix, String suffix) {
        TrieNode cur = root;
        int i = 0, j = suffix.length() - 1;
        while (i < prefix.length() || j >= 0) {
            char c1 = i < prefix.length() ? prefix.charAt(i) : '`';
            char c2 = j >= 0 ? suffix.charAt(j) : '`';
            int code = (c1 - '`') * 27 + (c2 - '`');
            cur = cur.children.get(code);
            if (cur == null) return -1;
            i++;
            j--;
        }
        return cur.weight;
    }

    static class TrieNode {
        Map<Integer, TrieNode> children = new HashMap<>();
        int weight;
    }
```

### #3 Trie Set Intersection
```java

    TrieNode t1, t2;

    public P745_PrefixAndSuffixSearch_Trie_SetIntersection(String[] words) {
        t1 = new TrieNode();
        t2 = new TrieNode();
        for (int i = 0, w = 0; i < words.length; i++, w++) {
            String reversedWord = new StringBuilder(words[i]).reverse().toString();
            insert(t1, words[i], w);
            insert(t2, reversedWord, w);
        }
    }

    private void insert(TrieNode cur, String word, int w) {
        cur.weight.add(w);
        for (char c : word.toCharArray()) {
            cur = cur.children.computeIfAbsent(c, k -> new TrieNode());
            cur.weight.add(w);
        }
    }

    // TC: O(nk + q(n + k)), SC: O(nk), n is no.of words, k is max length of word
    // q is no.of queries
    // TODO how does this work? also how is this TC/SC derived?
    public int f(String prefix, String suffix) {
        TrieNode p1 = traverse(t1, prefix);
        TrieNode p2 = traverse(t2, new StringBuilder(suffix).reverse().toString());

        if (p1 == null || p2 == null) return -1;

        int ans = -1;
        for (int w1 : p1.weight) if (w1 > ans && p2.weight.contains(w1)) ans = w1;
        return ans;
    }

    private TrieNode traverse(TrieNode cur, String word) {
        for (char c : word.toCharArray()) {
            if (!cur.children.containsKey(c)) return null;
            cur = cur.children.get(c);
        }
        return cur;
    }

    static class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        Set<Integer> weight = new HashSet<>();
    }
```

### #4 Trie Suffix Wrapped Words
```java

    TrieNode root = new TrieNode();

    public P745_PrefixAndSuffixSearch_Trie_SuffixWrappedWords(String[] words) {
        for (int w = 0; w < words.length; w++) {
            String word = words[w] + "{";
            for (int i = 0; i < word.length(); i++) {
                TrieNode cur = root;
                cur.weight = w;
                for (int j = i; j < 2 * word.length() - 1; j++) {
                    int code = word.charAt(j % word.length()) - 'a';
                    cur = cur.children.computeIfAbsent(code, k -> new TrieNode());
                    cur.weight = w;
                }
            }
        }
    }

    // TC: O(nk^2 + qk), SC: O(nk^2), n is no.of words, k is max length of word
    // q is no.of queries
    // todo how does this work?
    public int f(String prefix, String suffix) {
        TrieNode cur = root;
        String searchString = suffix + "{" + prefix;
        for (char c : searchString.toCharArray()) {
            if (!cur.children.containsKey(c - 'a')) return -1;
            cur = cur.children.get(c - 'a');
        }
        return cur.weight;
    }

    static class TrieNode {
        Map<Integer, TrieNode> children = new HashMap<>();
        int weight;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Concatenated Words
## Intro
https://leetcode.com/problems/concatenated-words/
![[Pasted image 20220321144115.png]]

## Thoughts
- N/A

## Solutions
### #1 Using Word Break

```java

    TrieNode root = new TrieNode();

    // TC: O(t * (n^2 + t)), SC: O(n + t), n = s.length(), t is total chars in words
    public List<String> findAllConcatenatedWordsInADict_UsingWordBreak(String[] words) {
        Arrays.stream(words).filter(x -> !x.isEmpty()).forEach(this::insert);
        List<String> res = new ArrayList<>();
        for (String word : words) {
            boolean ret = dfs(word, 0, new Boolean[word.length()]);
            if (ret) res.add(word);
        }
        return res;
    }

    // TC: O(n^2 + t), SC: O(n + t), n = s.length(), t is total chars in words
    private boolean dfs(String s, int start, Boolean[] memo) {
        if (s.isEmpty()) return false;
        if (start == s.length()) return true;
        if (memo[start] != null) return memo[start];

        TrieNode cur = root;
        for (int end = start + 1; end <= s.length(); end++) {
            char c = s.charAt(end - 1);
            cur = cur.children.get(c);
            if (cur == null) break;
            if (cur.word != null && dfs(s, end, memo) && !cur.word.equals(s)) return memo[start] = true;
        }
        return memo[start] = false;
    }

    private void insert(String word) {
        TrieNode cur = root;
        for (char c : word.toCharArray()) cur = cur.children.computeIfAbsent(c, k -> new TrieNode());
        cur.word = word;
    }

    static class TrieNode {
        String word;
        Map<Character, TrieNode> children = new HashMap<>();
    }
```

### #2 DP
```java
    // TC: O(n * l^3), SC: O(n), n = no.of words, l = highest word length
    public List<String> findAllConcatenatedWordsInADict_DP(String[] words) {
        List<String> res = new ArrayList<>();
        Set<String> beforeWordsSet = new HashSet<>();
        Arrays.sort(words, Comparator.comparingInt(String::length));

        for (String word : words) {
            if (canForm(word, beforeWordsSet)) res.add(word);
            beforeWordsSet.add(word); // this set will always contain only those strings before our ith string
        }
        return res;
    }

    private boolean canForm(String word, Set<String> set) {
        if (set.isEmpty()) return false;

        boolean[] dp = new boolean[word.length() + 1];
        dp[0] = true;
        for (int i = 1; i <= word.length(); i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && set.contains(word.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[word.length()];
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Longest Word In Dictionary
## Intro
https://leetcode.com/problems/longest-word-in-dictionary/
![[Pasted image 20220321144241.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java
    // TC: O(Sigma wi^2), SC: O(Sigma wi^2), where wi is length of words[i];
    public String longestWord_BruteForce(String[] words) {
        String res = "";
        Set<String> set = new HashSet<>(Arrays.asList(words));
        for (String word : words) {
            if (word.length() > res.length() || (word.length() == res.length() && word.compareTo(res) < 0)) {
                boolean good = true;
                for (int k = 1; k < word.length(); k++) {
                    if (!set.contains(word.substring(0, k))) {
                        good = false;
                        break;
                    }
                }
                if (good) res = word;
            }
        }
        return res;
    }
```

### #2 BruteForce Alternate
```java
    // TC: O(Sigma wi^2), SC: O(Sigma wi^2), where wi is length of words[i];
    public String longestWord_BruteForce_Alternate(String[] words) {
        Set<String> set = new HashSet<>(Arrays.asList(words));
        Arrays.sort(words, (a, b) -> a.length() == b.length() ? a.compareTo(b) : b.length() - a.length());

        for (String word : words) {
            boolean good = true;
            for (int k = 1; k < word.length(); k++) {
                if (!set.contains(word.substring(0, k))) {
                    good = false;
                    break;
                }
            }
            if (good) return word;
        }
        return "";
    }
```

### #3 Trie
```java
    // TC: O(Sigma wi), SC: O(Sigma wi), where wi is length of words[i];
    public String longestWord_TrieApproach(String[] words) {
        Trie trie = new Trie(words);
        return trie.dfs(words);
    }

    class Trie {
        TrieNode root = new TrieNode();

        public Trie(String[] words) {
            int i = 0;
            for (String word : words) {
                TrieNode cur = root;
                for (char c : word.toCharArray()) cur = cur.children.computeIfAbsent(c, k -> new TrieNode());
                cur.isLeaf = true;
                cur.index = i++;
            }
        }

        public String dfs(String[] words) {
            String res = "";
            Stack<TrieNode> stack = new Stack<>();
            stack.push(root);
            while (!stack.isEmpty()) {
                TrieNode node = stack.pop();
                if (node.isLeaf || node == root) {
                    if (node != root) {
                        String word = words[node.index];
                        if (word.length() > res.length() || (word.length() == res.length() && word.compareTo(res) < 0))
                            res = word;
                    }
                    for (TrieNode child : node.children.values()) stack.push(child);
                }
            }
            return res;
        }
    }

    class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        int index;
        boolean isLeaf;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Index Pairs of a String
## Intro
https://leetcode.com/problems/index-pairs-of-a-string/
![[Pasted image 20220321144533.png]]

## Thoughts
- N/A

## Solutions
### #1 Trie

```java
    // TC: O(n^2 * ?), SC: O(c), c is total chars in words, n is text.length();
    public int[][] indexPairs_TrieApproach(String text, String[] words) {
        List<int[]> res = new ArrayList<>();
        TrieNode root = new TrieNode();

        for (String word : words) {
            TrieNode cur = root;
            for (char c : word.toCharArray()) cur = cur.children.computeIfAbsent(c, k -> new TrieNode());
            cur.isLeaf = true;
        }

        for (int i = 0; i < text.length(); i++) {
            TrieNode cur = root;
            for (int j = i; j < text.length(); j++) {
                cur = cur.children.get(text.charAt(j));
                if (cur == null) break;
                if (cur.isLeaf) res.add(new int[]{i, j});
            }
        }

        return res.toArray(new int[0][0]);
    }

    static class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        boolean isLeaf;
    }
```

### #2 Set Approach
```java
    // TC: O(n ^ 3), SC: O(m), n = text.length(), m = words.length
    public int[][] indexPairs_SetApproach(String text, String[] words) {
        Set<String> set = new HashSet<>();
        Collections.addAll(set, words);
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < text.length(); i++) {
            for (int j = i + 1; j <= text.length(); j++) {
                String cur = text.substring(i, j);
                if (set.contains(cur)) res.add(List.of(i, j - 1));
            }
        }
        int[][] ans = new int[res.size()][2];
        for (int i = 0; i < res.size(); i++) {
            ans[i][0] = res.get(i).get(0);
            ans[i][1] = res.get(i).get(1);
        }
        return ans;
    }
```

## Common Pitfalls to Avoid
- N/A

---

# Problem Name
## Intro
https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/
![[Pasted image 20220321144644.png]]

## Thoughts
- N/A

## Solutions
### #1 BruteForce

```java
    // TC: O(n^2), SC: O(1)
    public int findMaximumXOR_BruteForce(int[] nums) {
        int ans = 0;
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                ans = Math.max(ans, nums[i] ^ nums[j]);
            }
        }
        return ans;
    }
```

### #2 Set Prefixes
```java
    // TC: O(n), SC: O(1)
    // Todo: Redo this
    public int findMaximumXOR_SetPrefixes(int[] nums) {
        int maxNum = Arrays.stream(nums).max().getAsInt();

        int L = Integer.toBinaryString(maxNum).length();

        int maxXor = 0, curXor;
        Set<Integer> prefixes = new HashSet<>();
        for (int i = L - 1; i > -1; i--) {
            // go to next bit by left shift
            maxXor <<= 1;
            // set 1 in smallest bit
            curXor = maxXor | 1;
            prefixes.clear();
            // compute all possible prefixes
            // of length L - i in binary representation
            for (int num : nums) prefixes.add(num >> i);
            // update maxXor, if two of these prefixes could result in curXor;
            // Check if p1^p2 == curXor i.e., p1 == curXor ^ p2;
            for (int p : prefixes) {
                if (prefixes.contains(curXor ^ p)) {
                    maxXor = curXor;
                    break;
                }
            }
        }
        return maxXor;
    }
```

## Common Pitfalls to Avoid
- N/A

---