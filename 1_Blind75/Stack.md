# Stack

# [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

## Intro

![Untitled](1_Blind75/Stack%2082adc/Untitled.png)

## Thoughts

- Whenever you notice parentheses, just think about using Stack.
- Also think about running counters for open and close and if at any point close is more than open, then it becomes invalid. And finally at the end of the loop, if `open ≠ close`, then also its invalid. However this won’t work for this problem. It will work only if you have single kind of parentheses. In this problem, we have 3 types of parentheses(small, flower and square brackets). Hence only stack solution works.

## Solutions

### Stack

```java
// TC: O(n), SC: O(n)
public boolean isValid(String s) {
    Stack<Character> stack = new Stack<>();
    Map<Character, Character> map = Map.of('(', ')', '{', '}', '[', ']');
    for (char c : s.toCharArray()) {
        if (map.containsKey(c)) stack.push(c);
        else if (stack.isEmpty() || c != map.get(stack.pop())) return false;
    }
    return stack.isEmpty();
}
```

## Common Pitfalls to Avoid

- Make sure to check `stack.isEmpty()` before popping from stack.
- Also once the loop ends, check if `stack.isEmpty()` again. If you only keep adding open brackets, then stack will still have some open brackets **left out** which are **not yet closed**. In this case also, you should return `false`.