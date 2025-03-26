  

Validate Parentheses

**You are given a string** `**s**` **consisting of the following characters:** `**'('**`**,** `**')'**`**,** `**'{'**`**,** `**'}'**`**,** `**'['**` **and** `**']'**`**.**

**The input string** `**s**` **is valid if and only if:**

**Every open bracket is closed by the same type of close bracket.Open brackets are closed in the correct order.Every close bracket has a corresponding open bracket of the same type.**

**Return** `**true**` **if** `**s**` **is a valid string, and** `**false**` **otherwise.**

**Example 1:**

```Java
Input: s = "[]"

Output: true
```

**Copy**

**Example 2:**

```Java
Input: s = "([{}])"

Output: true
```

**Copy**

**Example 3:**

```Java
Input: s = "[(])"

Output: false
```

**Copy**

**Explanation: The brackets are not closed in the correct order.**

  

Solution:

We will create a hashmap(dictionary) to store the key(close bracket) and value(open brackets)

Create a stack to store the open brackets

We will foreach char of the string

if the char is not a closing bracket, then we will push it to the stack (open bracket branch)

else if the char is a closing brackets, we will return false if: (closing bracket branch)

- The stack.Count == 0: this means that we are at a closing bracket branch, but the stack is 0 → no open bracket → there are close but no open bracket → invalid
- || (or)
- stack.Pop() ≠ pairs[c]: this means the value(open bracket) of the key(close bracket) is not equal to the closest open parenthesis → wrong order of open close → invalid

if one of these condition is true, we will return false → not a valid parentheses string

  

after the interate, we will return stack.Count == 0, which means that all the open brackets have their closing brackets, or else, it have a open bracket but no corresponding close bracket

Ex: “( ) { “

  

```C#
public class Solution {
    public bool IsValid(string s) {
        Dictionary<char, char> pairs = new Dictionary<char, char>(){
            [')'] = '(',
            [']'] = '[',
            ['}'] = '{'
        };
        Stack<char> stack = new Stack<char>();
        foreach(char c in s){
            if(!pairs.ContainsKey(c)){
                stack.Push(c);
            }
            else if (stack.Count == 0 || stack.Pop() != pairs[c]){
                return false;
            }
        }
        return stack.Count == 0;
    }
}
```