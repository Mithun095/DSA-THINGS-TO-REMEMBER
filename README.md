# DSA Cheatsheet (Python) 

> Ordered by what your notes prove is coming. Topics 1–11 = your real syllabus.
> Topics 12–14 (trees / backtracking / DP) = insurance, read last.
> Topic 15 = final-review checklist. Use `and`/`or` for logic, never `&`/`|`.

---

# 1. PYTHON REFERENCE (used in every question)

### Core built-ins
```python
len(x)                  # length
range(a, b, step)       # number sequence (b excluded)
enumerate(arr)          # (index, value) pairs
zip(a, b)               # pair two lists
sorted(arr)             # NEW sorted list | sorted(arr, reverse=True)
sorted(arr, key=lambda x: x[1])   # sort by custom key
arr.sort()              # sorts IN PLACE, returns None
sum(arr) min(arr) max(arr) abs(x) reversed(arr)
map(int, input().split())   # apply int to each token
divmod(17, 5)           # (3, 2)  → quotient, remainder
```
### Type conversions
`int() str() list() set() tuple() float() bool()`

### Char ↔ number (string problems)
```python
ord('a')  # 97     chr(97)  # 'a'
# A–Z = 65–90, a–z = 97–122, case difference = 32
chr(ord(c) - 32)   # lower → UPPER
chr(ord(c) + 32)   # UPPER → lower
```
`eval("2+3*4")` → 14 (runs string as code). ⚠️ Rarely needed; prefer `int()`. Know it, avoid it.

### String methods (strings are IMMUTABLE — return new strings)
```python
s.split()  s.split(",")          # → list
" ".join(list)                   # list → string
s.strip()  s.replace(a, b)
s.find(sub)                      # index or -1
s.count(sub)  s.upper()  s.lower()
s.isalpha() s.isdigit() s.isalnum() s.islower() s.isupper() s.isspace()
s[::-1]                          # reverse
s[i:j:k]                         # slice: start, stop, step
```

### List methods
```python
arr.append(x)        # O(1) add end
arr.pop()  arr.pop(i)# pop(0) is O(n)!
arr.insert(i, x)  arr.remove(value)
arr.sort()           # in place, returns None
arr.reverse()  arr.extend(other)
arr.count(x)  arr.index(x)
arr[::-1]  arr[1:4]
[x*2 for x in arr]                 # comprehension
[x for x in arr if x > 2]          # filter
```

### Dict & Set (your speed weapons)
```python
d.get(key, default)                # avoids KeyError
d[k] = d.get(k, 0) + 1             # THE frequency idiom
for k, v in d.items(): ...
key in d                           # membership

seen = set(); seen.add(x)
x in seen                          # O(1) lookup
set(arr)                           # dedupe
```

### collections (import these)
```python
from collections import Counter, defaultdict, deque
Counter("aabbc")                   # {'a':2,'b':2,'c':1}
Counter(s).most_common(1)[0][0]    # most frequent item
defaultdict(int)   # missing → 0   |  defaultdict(list)  # missing → []
dq = deque(); dq.append(x); dq.appendleft(x)
dq.popleft()                       # O(1) — use for queues/BFS
```

### Heap (only if needed)
```python
import heapq
heapq.heappush(h, x);  heapq.heappop(h)   # pops SMALLEST
# max-heap: push negatives, heapq.heappush(h, -x)
```

### Operators
| Op | Meaning |
|---|---|
| `//` | floor division | `%` last digit `n%10` |
| `**` | power | `2**10` |
| `& \| ^ ~ << >>` | bitwise and/or/xor/not/shift |
| `and or not` | **LOGIC — use these, not `&` `\|`** |

### Input idioms
```python
n = int(input())
arr = list(map(int, input().split()))   # space-separated ints
a, b = map(int, input().split())
```

### Complexity quick reference
| Operation | List | Dict/Set |
|---|---|---|
| index access | O(1) | — |
| search value | O(n) | O(1) |
| append | O(1) | O(1) |
| pop(0)/insert(0) | O(n) | — |
| `in` lookup | O(n) | O(1) |

---

# 2. BIT MANIPULATION (Day 1 & 3)

| Trick | Code | Why |
|---|---|---|
| Swap no temp | `a, b = b, a` | tuple unpack |
| Even/odd | `n & 1 == 0` → even | last bit |
| Power of 2 | `n > 0 and n & (n-1) == 0` | one set bit |
| Count set bits | `while n: n &= n-1; c += 1` | clears lowest set bit |
| Check i-th bit | `(n >> i) & 1` | shift then mask |
| XOR of 1..n | `n%4: 0→n, 1→1, 2→n+1, 3→0` | pattern repeats /4 |

**XOR superpower** — `x^x=0`, `x^0=x`, order-free → pairs cancel:
```python
result = 0
for num in nums: result ^= num   # Single Number / Find the Difference
```
**Missing Number:** `n*(n+1)//2 - sum(nums)`  *or* XOR all indices & values.

---

# 3. HASH MAP / FREQUENCY (Days 3–7, most common pattern)

```python
d[x] = d.get(x, 0) + 1            # burn this in
```
**Two Sum** — store complements:
```python
d = {}
for i, x in enumerate(nums):
    if target - x in d: return [d[target-x], i]
    d[x] = i
```
**Anagram:** `Counter(a) == Counter(b)`.
**Longest Consecutive** — only start a streak when `num-1 not in s` (counts each streak once).
**Boyer–Moore Majority** (>n/2 element, O(1) space):
```python
count = 0
for num in nums:
    if count == 0: key = num
    count += 1 if num == key else -1
return key
```
**First non-repeating char:**
```python
freq = Counter(s)
for i, c in enumerate(s):
    if freq[c] == 1: return i
return -1
```

---

# 4. ARRAYS / LISTS — 4 patterns

**A. Two Pointers** (sorted array, pairs, reverse):
```python
left, right = 0, len(arr)-1
while left < right:
    s = arr[left] + arr[right]
    if s == target: return [left, right]
    elif s < target: left += 1
    else: right -= 1
```
**B. Sliding Window** (subarray of size k / condition):
```python
window = sum(arr[:k]); best = window
for i in range(k, len(arr)):
    window += arr[i] - arr[i-k]      # add new, drop old
    best = max(best, window)
```
**C. Prefix Sum** (many range-sum queries):
```python
prefix = [0]
for x in arr: prefix.append(prefix[-1] + x)
# sum arr[i..j] = prefix[j+1] - prefix[i]
```
**D. In-place reverse / overwrite:**
```python
i, j = 0, len(arr)-1
while i < j:
    arr[i], arr[j] = arr[j], arr[i]; i += 1; j -= 1
```

---

# 5. STRINGS (Days 5–6)

**Palindrome (cleaned):**
```python
s = "".join(c for c in s.lower() if c.isalnum())
return s == s[::-1]
```
**Skip-and-swap** (reverse only letters/vowels, keep specials in place):
```python
while l < r:
    if not s[l].isalnum(): l += 1
    elif not s[r].isalnum(): r -= 1
    else: s[l], s[r] = s[r], s[l]; l += 1; r -= 1
```
**Valid Palindrome II** (delete one): on mismatch, test skip-left OR skip-right substring for palindrome.
**Count vowels:** `sum(1 for c in s if c in "aeiou")`.

---

# 6. STACK — 2 patterns cover Days 7–8

Use a list: `append()` push, `pop()`, `stack[-1]` top, `not stack` empty-check.

**A. Cancellation / Matching** (valid parens, remove adjacent dups, backspace, remove stars, clear digits, make-string-great):
```python
stack = []
for ch in s:
    if <ch cancels stack[-1]>: stack.pop()
    else: stack.append(ch)
```
- Valid parens: map `{')':'('}`, the popped opener must match.
- Make string great: cancel when `abs(ord(a)-ord(b)) == 32`.

**B. Monotonic Stack** (next greater, daily temperatures, final prices) — **store INDICES:**
```python
stack = []
for i in range(len(arr)):
    while stack and arr[i] > arr[stack[-1]]:
        idx = stack.pop()
        ans[idx] = i - idx        # distance (or arr[i] for value)
    stack.append(i)
```
**RPN / Baseball:** push numbers; on operator pop last two, compute, push result.

---

# 7. QUEUE (deque, Day 8)
```python
from collections import deque
q = deque(); q.append(x); q.popleft()   # O(1) FIFO
```
Used for rotation simulations and BFS / level-order.

---

# 8. BINARY SEARCH — biggest topic (all of Day 9)

**Template (memorize exactly):**
```python
l, r = 0, len(nums)-1
while l <= r:
    mid = l + (r - l)//2
    if nums[mid] == target: return mid
    elif nums[mid] < target: l = mid + 1
    else: r = mid - 1
return -1
```
**Variants:**
| Goal | Change |
|---|---|
| First occurrence | on match: save mid, `r = mid-1` |
| Last occurrence | on match: save mid, `l = mid+1` |
| Search insert position | `return l` after loop |
| Find peak / min in rotated | `while l < r`, compare to neighbor, no `==` |

**Binary Search on the ANSWER** (Sqrt, Koko, Perfect Square) — search the *answer range*, test each candidate:
```python
# Koko: l=1, r=max(piles)
hours = sum((p + mid - 1)//mid for p in piles)   # ceil divide = (a+b-1)//b
if hours <= h: r = mid
else: l = mid + 1
```
**2D matrix:** flatten to length `m*n`; `row = mid//n`, `col = mid%n`.

---

# 9. SORTING (Day 9) — be able to write these
- **Bubble:** swap adjacent if out of order; `for j in range(n-i-1)`. O(n²)
- **Selection:** find min of the rest, swap to front. O(n²)
- **Merge:** split, recurse, merge two sorted halves with two pointers. O(n log n)
```python
while i < len(left) and j < len(right):
    if left[i] <= right[j]: ans.append(left[i]); i += 1
    else: ans.append(right[j]); j += 1
# then append leftovers of left, then right
```

---

# 10. GREEDY (Days 3 & 9)

**Gas Station:** if `sum(gas) < sum(cost)` → -1. Else track tank; reset start when it goes negative:
```python
start = tank = 0
for i in range(len(gas)):
    tank += gas[i] - cost[i]
    if tank < 0: start = i + 1; tank = 0
return start
```
**Lemonade Change:** give largest bills back first. **Remove K Digits:** monotonic stack, pop bigger digits while removals remain.

---

# 11. RECURSION (Day 2) — base case first
```python
def factorial(n):
    if n == 0 or n == 1: return 1
    return n * factorial(n-1)

def power(a, b):
    if b == 0: return 1
    return a * power(a, b-1)
```

---
---

# ===== INSURANCE SECTION (read last) =====

# 12. TREES

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val, self.left, self.right = val, left, right
```
**Every tree function:** base case `if not node: return`, then recurse left & right.
```python
def inorder(node):     # L, Root, R  (BST → sorted)
    if not node: return
    inorder(node.left); print(node.val); inorder(node.right)
# preorder = process node FIRST | postorder = process node LAST

def height(node):
    if not node: return 0
    return 1 + max(height(node.left), height(node.right))

def search_bst(node, t):
    if not node: return False
    if node.val == t: return True
    return search_bst(node.left, t) if t < node.val else search_bst(node.right, t)
```
**Level-order (BFS) — queue:**
```python
out, q = [], deque([root])
while q:
    n = q.popleft(); out.append(n.val)
    if n.left: q.append(n.left)
    if n.right: q.append(n.right)
```

# 13. BACKTRACKING — choose → recurse → un-choose
```python
def subsets(nums):
    res = []
    def bt(start, path):
        res.append(path[:])                 # copy!
        for i in range(start, len(nums)):
            path.append(nums[i])            # choose
            bt(i+1, path)                   # explore
            path.pop()                      # un-choose
    bt(0, []); return res
```
Permutations: loop over all unused elements instead of `start`.

# 14. DYNAMIC PROGRAMMING — recursion + memo dict
```python
def fib(n, memo={}):
    if n <= 1: return n
    if n in memo: return memo[n]
    memo[n] = fib(n-1, memo) + fib(n-2, memo)
    return memo[n]
```
- Climbing stairs = fib. Coin change: try each coin, recurse `amount-coin`, take min, +1.
- **Recipe:** write brute-force recursion, then add `if state in memo: return memo[state]`.

---
---

# 15. FINAL-REVIEW CHECKLIST (read right before the exam)

**Syntax traps (cost easy marks):**
- `&` is bitwise, NOT logic. Use `and` / `or`. (`w%2==0 and w>2`) ✅
- `arr.sort()` returns `None`; `sorted(arr)` returns a new list.
- Strings immutable: `s[0]='x'` errors → `list(s)`, edit, `"".join(...)`.
- `officer -= 1`, not `officer == -1`.
- `range(n)` is `0..n-1`; `arr[-1]` is last.
- Base case BEFORE the recursive call.
- Partial marks are real: comment your approach even if code is incomplete.

**"What pattern is this?" — decision guide:**
| Clue in the question | Pattern |
|---|---|
| "single / appears once", XOR-ish | Bit XOR |
| count / duplicates / frequency / two-sum | Hash map `.get(x,0)+1` |
| sorted array, find / insert | Binary search template |
| "min/max value such that...", capacity, speed | Binary search on the ANSWER |
| matching brackets / cancel adjacent / undo | Stack (cancellation) |
| "next greater / warmer / cheaper" | Monotonic stack (indices) |
| subarray of size k / window condition | Sliding window |
| reverse / palindrome / skip specials | Two pointers |
| "can you complete the circuit" | Greedy running total |
| node / left / right / traversal | Tree recursion (`if not node`) |
| "number of ways", "min steps to reach n" | DP (recursion + memo) |
| "all subsets / permutations" | Backtracking (choose-recurse-undo) |

---
This is just till day 9
