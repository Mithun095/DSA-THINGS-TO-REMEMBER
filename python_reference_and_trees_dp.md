# Supplement: Python Reference + Trees/DP/Backtracking (Insurance Read)

> Read the Python reference fully — it's used in every question.
> Read Part B once for recognition. Don't drill it over binary search.

---

# PART A — PYTHON KEYWORDS & BUILT-INS YOU'LL ACTUALLY USE

## Core built-in functions
| Function | What it does | Example |
|---|---|---|
| `len(x)` | length | `len("abc")` → 3 |
| `range(a,b,step)` | number sequence | `range(0,10,2)` → 0,2,4,6,8 |
| `enumerate(x)` | index + value pairs | `for i,v in enumerate(arr)` |
| `zip(a,b)` | pair two lists | `for x,y in zip(a,b)` |
| `sorted(x)` | NEW sorted list | `sorted(arr, reverse=True)` |
| `sum / min / max` | aggregate | `max(arr)`, `sum(arr)` |
| `abs(x)` | absolute value | `abs(-5)` → 5 |
| `reversed(x)` | reverse iterator | `list(reversed(arr))` |
| `map(f, x)` | apply f to each | `list(map(int, input().split()))` |
| `filter(f, x)` | keep where f true | `list(filter(lambda n:n>0, arr))` |
| `divmod(a,b)` | (quotient, remainder) | `divmod(17,5)` → (3,2) |

## Type conversions
`int(x)`, `str(x)`, `list(x)`, `set(x)`, `tuple(x)`, `float(x)`, `bool(x)`

## Char ↔ number (vital for string problems)
| | | |
|---|---|---|
| `ord('a')` | char → ASCII | 97 |
| `chr(97)` | ASCII → char | 'a' |
- `'A'`–`'Z'` = 65–90, `'a'`–`'z'` = 97–122. Diff between same letter's cases = **32**.
- Lower→upper: `chr(ord(c) - 32)`. Upper→lower: `chr(ord(c) + 32)`.

## `eval()` — you asked about this
`eval("2 + 3 * 4")` → 14. Runs a string as Python code.
⚠️ Almost never needed in exams and risky; prefer `int()` for plain numbers. Know it exists, rarely use it.

## String methods
| Method | Does |
|---|---|
| `.split()` / `.split(",")` | string → list |
| `" ".join(list)` | list → string |
| `.strip()` | trim whitespace |
| `.replace(a,b)` | swap substrings |
| `.find(sub)` | index, or -1 |
| `.count(sub)` | occurrences |
| `.upper() / .lower()` | case |
| `.isalpha() .isdigit() .isalnum() .islower() .isupper() .isspace()` | True/False checks |
| `s[::-1]` | reverse |
| `s[i:j:k]` | slice (start, stop, step) |

## List methods
| Method | Does | Note |
|---|---|---|
| `.append(x)` | add to end | O(1) |
| `.pop()` / `.pop(i)` | remove & return | pop(0) is O(n) |
| `.insert(i,x)` | insert at index | |
| `.remove(x)` | delete first matching value | |
| `.sort()` | sort IN PLACE | returns None! |
| `.reverse()` | reverse in place | |
| `.count(x)` `.index(x)` | count / find | |
| `.extend(lst)` | append many | |

## Dict & Set
- Dict: `d.get(k, default)`, `d.items()`, `d.keys()`, `d.values()`, `k in d`
- The counter idiom: `d[k] = d.get(k, 0) + 1`
- Set: `s.add(x)`, `s.discard(x)`, `x in s` (O(1)), `set(list)` to dedupe

## collections (import these)
```python
from collections import Counter, defaultdict, deque
Counter("aabbc")        # {'a':2,'b':2,'c':1}
Counter(s).most_common(1)  # [(char, count)]
defaultdict(int)        # missing key → 0
defaultdict(list)       # missing key → []
deque()                 # appendleft / popleft are O(1) → use for queues
```

## Operators that matter in DSA
| Op | Meaning |
|---|---|
| `//` | integer (floor) division |
| `%` | remainder (last digit: `n%10`) |
| `**` | power (`2**10`) |
| `&  \|  ^  ~  << >>` | bitwise: and, or, xor, not, shift |
| `and  or  not` | LOGIC (use these, NOT `&` `\|`) |

## Python keywords quick list
`if elif else` · `for while` · `break continue` · `def return` · `in not in` · `and or not` · `lambda` · `True False None` · `import from` · `class` · `pass` · `try except`

## Input idioms (for terminal-style exam questions)
```python
n = int(input())
arr = list(map(int, input().split()))   # space-separated ints into a list
a, b = map(int, input().split())        # two ints on one line
```

---

# PART B — TREES, DP, BACKTRACKING (recognition only)

## TREES — the absolute minimum
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val, self.left, self.right = val, left, right
```
**Every tree function = base case `if not node: return`, then recurse left & right.**
```python
def inorder(node):      # Left, Root, Right (BST → sorted)
    if not node: return
    inorder(node.left); print(node.val); inorder(node.right)
# preorder = Root,Left,Right | postorder = Left,Right,Root (just move the print)

def height(node):
    if not node: return 0
    return 1 + max(height(node.left), height(node.right))
```
**Level-order (BFS) uses a queue:**
```python
from collections import deque
def level_order(root):
    if not root: return []
    out, q = [], deque([root])
    while q:
        n = q.popleft()
        out.append(n.val)
        if n.left: q.append(n.left)
        if n.right: q.append(n.right)
    return out
```
**If you remember one thing:** traversals are the same code, you just move where you process the node.

## DYNAMIC PROGRAMMING — recursion + a memo dict
The whole trick: write the brute-force recursion, then cache results.
```python
def fib(n, memo={}):
    if n <= 1: return n
    if n in memo: return memo[n]            # already solved?
    memo[n] = fib(n-1, memo) + fib(n-2, memo)
    return memo[n]
```
- **Climbing stairs** (1 or 2 steps) = identical to fib.
- **Coin change** (min coins): try each coin, recurse on `amount - coin`, take the min, add 1.
**If you remember one thing:** "number of ways" / "min/max to reach n" → recursion + `if state in memo: return memo[state]`.

## BACKTRACKING — one template
Shape is always **choose → recurse → un-choose.**
```python
def subsets(nums):
    res = []
    def bt(start, path):
        res.append(path[:])              # copy the path
        for i in range(start, len(nums)):
            path.append(nums[i])         # choose
            bt(i + 1, path)              # explore
            path.pop()                   # un-choose
    bt(0, [])
    return res
```
- **Permutations:** same idea, but loop over all remaining unused elements.
**If you remember one thing:** add a choice, recurse, remove it. Always append a **copy** (`path[:]`).

---

## One-line recognition guide for Part B
| Clue | Topic |
|---|---|
| node, left/right child, traversal, BST | Tree recursion (`if not node`) |
| "number of ways", "min/max steps to reach" | DP (recursion + memo) |
| "all subsets / permutations / combinations" | Backtracking (choose-recurse-undo) |
