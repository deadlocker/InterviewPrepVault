# Tips/Tricks

# Things To Try If Blocked

- Whenever you notice parentheses, at least think about using a Stack(example [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/) )
- Whenever someone asks no.of ways or min/max/optimization of: then at least think about DP in the back of your mind.
- When dealing with dp problems, dp[i] can be formulated as subarray ending at ith index or some result for first i(index) elements i.e., the meaning of dp[i] is important. Think carefully about what dp[i] represents. This will also lead to the returned answer. dp[n - 1] will not always be the answer. Sometimes it can be the max of the entire dp array. ([MaxSubarray](https://leetcode.com/problems/maximum-subarray/) problem is one such example)
- In case of linkedList or array, think of floyd cycle detection or cyclic sort.
- Whenever there is TopK in question, think of using PQ
- Also sometimes think of reversed approach. For example, instead of following problem’s description of water going from cells to oceans, what if we flood the cells from the oceans instead? (PacificAtlantic Water Flow for example)
- Whenever palindrome exists, think about expandAroundCenter approach.

# Programming Tips/Patterns/Tricks

- Remember the linkedlist dummy node trick.
- `n & (n - 1)` bit trick for NumberOf1Bits problem. LSB(rightMost 1 bit) 1bit in n will always be 0 bit in n-1 representation. So you can do n&(n-1) until n == 0 and calculate the no.of 1 bits in input n number.
- XOR trick(Missing Number). Remember that `a ^ a = 0`. In some problems you can use this to your advantage. You can XOR the input array with indices or some range(like 1 to n inclusive) for example to find missing number.
- `isPowerOfTwo(int x) => x != 0 && (x & (x - 1)) == 0`
- Memorize the Union Find DSA implementation
- Memorize the QuickSelect implementation
- For a graph to be valid tree, it should satify three conditions: 1) edges.length = n - 1(any less it will not be fully connected, any more it will have cycles), 2) no cycles 3) no disconnected components.

# Common Pitfalls to Avoid

- Be “proactive” instead of being “reactive”. Valid Palindrome question is one such example. Gather full requirements and constraints beforehand(String can contain mixed case, punctuation, digits etc). Companies have rejected people for reacting to bugs instead of being proactive and gathering requirements beforehand itself.

## Language Pitfalls to Avoid(Java)

- If using `ArrayDeque`, be careful to not add `null` values in it. ArrayDeque will throw null pointer if you add null values in it. In such cases use `LinkedList`
- `>>(right shift operator)` vs `>>>(unsigned right shift operator)`. `>>` will always use the leftMostBit as filler. If its 1, it will always be 1, if its 0 it will always be zero. However `>>>` will only use zero as filler. `>>>` is what you need for a problem like [NumberOf1Bits](https://leetcode.com/problems/number-of-1-bits/) problem.
- When using lambda comparator, use Integer.compare(a,b) instead of a - b. That is use a.equals(b) instead of a == b. [https://javarevisited.blogspot.com/2010/10/what-is-problem-while-using-in.html](https://javarevisited.blogspot.com/2010/10/what-is-problem-while-using-in.html)