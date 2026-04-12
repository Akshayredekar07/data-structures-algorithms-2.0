# **Python-Specific Array Notes**
> **Python lists, built-ins, comprehensions, libraries — everything that matters in contests**
> *Companion file to the language-agnostic Array notes*

---

## Table of Contents
1. [Python List = Array](#1-python-list--array)
2. [Initialization Patterns](#2-initialization-patterns)
3. [Core List Operations & True Complexity](#3-core-list-operations--true-complexity)
4. [Comprehensions vs For Loops — The Real Comparison](#4-comprehensions-vs-for-loops--the-real-comparison)
5. [Slicing — Power & Pitfalls](#5-slicing--power--pitfalls)
6. [Built-in Functions You Must Know](#6-built-in-functions-you-must-know)
7. [Negative Indexing & Circular Access](#7-negative-indexing--circular-access)
8. [Essential Standard Library for Array Problems](#8-essential-standard-library-for-array-problems)
   - `collections` (Counter, defaultdict, deque)
   - `itertools` (accumulate, combinations, permutations)
   - `bisect` (binary search on sorted lists)
   - `heapq` (top-k problems)
9. [Two Pointers & Sliding Window in Python](#9-two-pointers--sliding-window-in-python)
10. [Prefix Sum in Python](#10-prefix-sum-in-python)
11. [Sorting in Python — All Variants](#11-sorting-in-python--all-variants)
12. [Input / Output for Competitive Programming](#12-input--output-for-competitive-programming)
13. [Python-Specific Pitfalls in Array Problems](#13-python-specific-pitfalls-in-array-problems)
14. [Performance Tips for CP](#14-performance-tips-for-cp)
15. [Python CP Cheat Sheet](#15-python-cp-cheat-sheet)

---

## 1. Python List = Array

In Python, **`list` is your array**. It's a dynamic array (like C++ `vector`) — not a fixed-size array.

```python
arr = [10, 20, 30, 40, 50]
#      0    1    2    3    4   ← index
#     -5   -4   -3   -2   -1  ← negative index

# Important: Python lists store OBJECT REFERENCES, not raw values
# This means the "array" in memory is [ptr→10, ptr→20, ...]
# This is why Python lists can hold mixed types (but avoid this in CP)
```

**Key difference from C++/Java arrays:**

| Feature | Python list | C++ vector | Java int[] |
|---------|------------|------------|------------|
| Typed | No (any type) | Yes | Yes |
| Resizable | Yes | Yes | No |
| In memory | References | Values | Values |
| Random access | O(1) | O(1) | O(1) |
| Integer overflow | Never (BigInt) | Can overflow | Can overflow |

> **CP advantage**: Python integers never overflow. No need for `long long` or `%MOD` worries on integer size.

---

## 2. Initialization Patterns

These are the most common ways to create arrays in CP — know them all cold.

```python
# Empty list
arr = []

# Fixed size, all zeros — MOST COMMON in CP
arr = [0] * n             # [0, 0, 0, ..., 0]  (n elements)

# Fixed size with a value
arr = [-1] * n            # [-1, -1, ...]
arr = [float('inf')] * n  # [∞, ∞, ...]

# Initialize from 1..n
arr = list(range(1, n+1)) # [1, 2, 3, ..., n]

# Initialize from input (single line space-separated)
arr = list(map(int, input().split()))

# 2D array (n rows, m columns) — all zeros
matrix = [[0] * m for _ in range(n)]

# WRONG WAY to init 2D array (common beginner trap):
matrix = [[0] * m] * n    # All rows point to THE SAME list!
# Test: matrix[0][0] = 5 → matrix[1][0] is also 5!

# CORRECT 2D:
matrix = [[0] * m for _ in range(n)]

# Boolean arrays
visited = [False] * n
seen = [False] * (max_val + 1)  # as frequency/hash array

# Frequency / count array (values 0..max_val)
freq = [0] * (max_val + 1)
for x in arr:
    freq[x] += 1
```

---

## 3. Core List Operations & True Complexity

```python
arr = [1, 2, 3, 4, 5]

# O(1) operations
arr[i]             # access by index
arr[-1]            # last element
arr[i] = val       # update
arr.append(val)    # add to end (amortized O(1))
arr.pop()          # remove last (O(1))
len(arr)           # length

# O(n) operations — be careful in loops!
arr.insert(i, val) # insert at position i → shifts elements
arr.pop(i)         # remove at index i → shifts elements
arr.remove(val)    # removes FIRST occurrence of val
val in arr         # linear search! Use set for O(1) lookup
arr.index(val)     # position of first occurrence
arr.count(val)     # count occurrences

# O(n) but often forgotten:
arr[l:r]           # SLICING creates a NEW list — O(r-l)
arr[::-1]          # reversed copy — O(n)
arr.copy()         # shallow copy — O(n)
arr + arr2         # concatenation — O(n+m)

# O(n log n)
arr.sort()         # in-place sort (Timsort)
sorted(arr)        # returns new sorted list
```

**Important**: `arr.sort()` modifies in-place and returns `None`. `sorted(arr)` returns a new list.

---

## 4. Comprehensions vs For Loops — The Real Comparison

### Performance Benchmark (actual relative speed)

```
Task: build list of squares for 1,000,000 elements

Method                    Relative Speed
─────────────────────────────────────────
list comprehension        1.0x  (fastest)
map() + list()            ~1.0x (similar)
for loop + .append()      ~1.5x (slower)
```

Why comprehensions are faster: CPython implements them at the C level, avoiding the Python-level `append` lookup on each iteration.

### When to use each — the definitive guide

```python
# USE COMPREHENSION when:
# 1. Building a new list with transformation
squares = [x**2 for x in arr]

# 2. Filtering
positives = [x for x in arr if x > 0]

# 3. Both transform + filter
result = [x**2 for x in arr if x % 2 == 0]

# 4. 2D list flattening
flat = [x for row in matrix for x in row]

# USE FOR LOOP when:
# 1. Complex multi-step logic
result = []
for x in arr:
    if x > 0:
        y = some_complex_function(x)
        if y not in seen:
            result.append(y)
            seen.add(y)

# 2. You need to break/continue early
for x in arr:
    if found_answer:
        break

# 3. Side effects matter (modifying external state)
for i in range(len(arr)):
    if arr[i] < 0:
        arr[i] = 0  # modifying in-place

# USE GENERATOR EXPRESSION when you only need to ITERATE (not store):
total = sum(x**2 for x in arr)        # no list created, memory efficient
any_neg = any(x < 0 for x in arr)     # short-circuits on first True
all_pos = all(x > 0 for x in arr)
```

### Comprehension syntax — full reference

```python
# Basic
[expression for item in iterable]

# With condition
[expression for item in iterable if condition]

# Nested (2D flatten)
[expr for row in matrix for item in row]

# 2D → 2D
[[cell*2 for cell in row] for row in matrix]

# Dict comprehension (very useful in CP)
freq = {x: arr.count(x) for x in set(arr)}  # but Counter is faster

# Set comprehension
unique_squares = {x**2 for x in arr}

# Conditional expression (ternary) inside comprehension
clamped = [x if x >= 0 else 0 for x in arr]
```

### Comprehension performance: when it stops being faster

```python
# If the operation inside is heavy, the overhead advantage disappears.
# Rule: if it fits cleanly on one line and is simple → comprehension.
# If you'd need nested ifs, multiple assignments → use loop.

# Example where loop is MORE READABLE (prefer this):
dp = [0] * n
dp[0] = arr[0]
for i in range(1, n):
    dp[i] = max(arr[i], dp[i-1] + arr[i])   # Kadane's
```

---

## 5. Slicing — Power & Pitfalls

```python
arr = [10, 20, 30, 40, 50]
#      0    1    2    3    4

# arr[start:stop:step]  → CREATES A NEW LIST (O(n))
arr[1:4]      # [20, 30, 40]  → indices 1, 2, 3 (stop is exclusive)
arr[:3]       # [10, 20, 30]  → from beginning
arr[2:]       # [30, 40, 50]  → to end
arr[::2]      # [10, 30, 50]  → every 2nd element
arr[::-1]     # [50, 40, 30, 20, 10]  → reversed copy

# Reverse in-place (no copy, O(n)):
arr.reverse()
# or two-pointer:
left, right = 0, len(arr)-1
while left < right:
    arr[left], arr[right] = arr[right], arr[left]
    left += 1
    right -= 1

# PITFALL: Slicing in a loop creates hidden O(n²)
# WRONG (TLE for n=10⁵):
for i in range(n):
    process(arr[i:])      # each slice is O(n) → total O(n²)

# CORRECT: Use index pointers
for i in range(n):
    process_with_indices(arr, i, n)
```

---

## 6. Built-in Functions You Must Know

```python
arr = [3, 1, 4, 1, 5, 9, 2, 6]

# Max, Min
max(arr)                    # 9
min(arr)                    # 1
max(arr, key=abs)           # by absolute value
max(a, b)                   # works on two values too

# Sum
sum(arr)                    # 31
sum(arr[l:r+1])             # range sum (O(n) — use prefix sum if repeated)

# Sorting (returns NEW list)
sorted(arr)                 # [1, 1, 2, 3, 4, 5, 6, 9]
sorted(arr, reverse=True)   # descending
sorted(arr, key=lambda x: -x)  # same as reverse=True
sorted(pairs, key=lambda x: (x[0], -x[1]))  # sort by first ASC, second DESC

# Enumerate (index + value together — use always over range(len()))
for i, val in enumerate(arr):
    print(i, val)

for i, val in enumerate(arr, start=1):  # 1-indexed
    print(i, val)

# Zip (iterate two arrays together)
a = [1, 2, 3]
b = [4, 5, 6]
for x, y in zip(a, b):
    print(x + y)  # 5, 7, 9

# Any / All (short-circuit — faster than manual loop)
any(x > 5 for x in arr)   # True if at least one element > 5
all(x > 0 for x in arr)   # True if all elements > 0

# In operator
5 in arr          # True  — O(n) for list, O(1) for set
5 not in arr      # True/False

# Reversed (iterator, no copy)
for x in reversed(arr):
    print(x)

# Map (apply function to each element)
doubled = list(map(lambda x: x * 2, arr))
as_str = list(map(str, arr))
```

---

## 7. Negative Indexing & Circular Access

```python
arr = [10, 20, 30, 40, 50]

arr[-1]   # 50  — last element
arr[-2]   # 40  — second last
arr[-n:]  # last n elements

# Circular index (wrapping around)
n = len(arr)
i = 7
circular_index = i % n   # 7 % 5 = 2 → arr[2] = 30

# Works for negative too:
i = -3
circular_index = i % n   # -3 % 5 = 2 in Python (Python mod is always non-negative!)
# Note: In C++, -3 % 5 could be -3. In Python it's always 2. ✓

# Python's modulo is always non-negative for positive divisor:
print(-1 % 5)   # 4 (not -1 like in C++)
print(-7 % 5)   # 3
```

> **Python modulo advantage in CP**: `(x % n)` is always in `[0, n-1]` in Python, even for negative `x`. No need for `((x % n) + n) % n` like in C++.

---

## 8. Essential Standard Library for Array Problems

### 8.1 `collections.Counter` — Frequency in One Line

```python
from collections import Counter

arr = [1, 2, 2, 3, 3, 3, 4]
freq = Counter(arr)
# Counter({3: 3, 2: 2, 1: 1, 4: 1})

freq[3]             # 3   (count of 3)
freq[99]            # 0   (missing key returns 0, not KeyError!)
freq.most_common(2) # [(3, 3), (2, 2)] — top 2 most frequent
list(freq.keys())   # [1, 2, 3, 4]
sum(freq.values())  # 7 (total elements)

# Use instead of:
# freq = {}
# for x in arr: freq[x] = freq.get(x, 0) + 1
```

### 8.2 `collections.defaultdict` — HashMap Without KeyError

```python
from collections import defaultdict

# Prefix sum with hashmap pattern
prefix_count = defaultdict(int)
prefix_count[0] = 1   # empty prefix

count, prefix_sum = 0, 0
for x in arr:
    prefix_sum += x
    count += prefix_count[prefix_sum - k]
    prefix_count[prefix_sum] += 1

# defaultdict(list) for grouping
groups = defaultdict(list)
for i, x in enumerate(arr):
    groups[x].append(i)
```

### 8.3 `collections.deque` — O(1) Both Ends

```python
from collections import deque

# Use for sliding window maximum (monotonic deque)
dq = deque()       # stores indices

# Never use list as a queue (list.pop(0) is O(n))
# Always use deque for queue operations
dq.append(x)       # add to right  O(1)
dq.appendleft(x)   # add to left   O(1)
dq.pop()           # remove right  O(1)
dq.popleft()       # remove left   O(1)
dq[0]              # peek left     O(1)
dq[-1]             # peek right    O(1)
```

### 8.4 `itertools.accumulate` — Prefix Sum in One Line

```python
from itertools import accumulate

arr = [3, 1, 4, 1, 5]

# Prefix sum
prefix = list(accumulate(arr))
# [3, 4, 8, 9, 14]
# Note: this is prefix[i] = sum(arr[0..i]), NOT prefix[i] = sum(arr[0..i-1])
# So: sum(arr[l..r]) = prefix[r] - (prefix[l-1] if l > 0 else 0)

# Or build with initial=0 (cleaner for range queries):
prefix = [0] + list(accumulate(arr))
# [0, 3, 4, 8, 9, 14]
# sum(arr[l..r]) = prefix[r+1] - prefix[l]  ✓

# Prefix product
from operator import mul
prefix_prod = list(accumulate(arr, mul))
# [3, 3, 12, 12, 60]

# Prefix max
prefix_max = list(accumulate(arr, max))
# [3, 3, 4, 4, 5]
```

### 8.5 `bisect` — Binary Search on Sorted Lists

```python
import bisect

arr = [1, 3, 5, 7, 9]  # must be sorted

# Find insertion point (for values NOT in list → returns where it would go)
bisect.bisect_left(arr, 5)   # 2  — index of leftmost 5 (or where 5 would go)
bisect.bisect_right(arr, 5)  # 3  — index after rightmost 5

# Common uses:
# 1. Check if value exists (binary search)
i = bisect.bisect_left(arr, target)
if i < len(arr) and arr[i] == target:
    print("Found at", i)

# 2. Count elements less than target
count_less = bisect.bisect_left(arr, target)

# 3. Count elements in range [lo, hi]
count_range = bisect.bisect_right(arr, hi) - bisect.bisect_left(arr, lo)

# 4. Insert while keeping sorted (O(log n) search + O(n) insert)
bisect.insort(arr, 4)   # arr becomes [1, 3, 4, 5, 7, 9]
```

### 8.6 `heapq` — Top-K and Priority Queue

```python
import heapq

arr = [3, 1, 4, 1, 5, 9, 2, 6]

# k largest elements — O(n log k)
heapq.nlargest(3, arr)   # [9, 6, 5]

# k smallest elements — O(n log k)
heapq.nsmallest(3, arr)  # [1, 1, 2]

# Min-heap (heapq is min-heap by default)
heap = []
heapq.heappush(heap, 5)
heapq.heappush(heap, 2)
heapq.heappush(heap, 8)
heapq.heappop(heap)   # returns 2 (smallest)

# Max-heap trick: negate values
heap = []
heapq.heappush(heap, -5)
heapq.heappush(heap, -2)
max_val = -heapq.heappop(heap)  # 5
```

### 8.7 `itertools` — Combinations, Permutations

```python
from itertools import combinations, permutations, product, chain

arr = [1, 2, 3]

list(combinations(arr, 2))    # [(1,2), (1,3), (2,3)]  — C(3,2)=3
list(permutations(arr))       # all 6 permutations
list(product([0,1], repeat=3)) # all 8 binary strings of length 3

# Flatten list of lists
nested = [[1,2], [3,4], [5]]
flat = list(chain.from_iterable(nested))  # [1, 2, 3, 4, 5]
```

---

## 9. Two Pointers & Sliding Window in Python

### Two Pointers (Opposite Ends)

```python
def two_sum_sorted(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        s = arr[left] + arr[right]
        if s == target:
            return (left, right)
        elif s < target:
            left += 1
        else:
            right -= 1
    return None
```

### Two Pointers (Slow-Fast / In-Place Filter)

```python
# Remove zeros in-place, maintain relative order
def remove_zeros(arr):
    slow = 0
    for fast in range(len(arr)):
        if arr[fast] != 0:
            arr[slow] = arr[fast]
            slow += 1
    # Optionally zero out the rest
    for i in range(slow, len(arr)):
        arr[i] = 0
    return arr
```

### Fixed Sliding Window

```python
def max_sum_subarray_k(arr, k):
    n = len(arr)
    window_sum = sum(arr[:k])     # first window
    max_sum = window_sum

    for i in range(k, n):
        window_sum += arr[i] - arr[i - k]   # slide
        max_sum = max(max_sum, window_sum)

    return max_sum
```

### Variable Sliding Window

```python
def longest_subarray_sum_at_most_k(arr, k):
    left = 0
    current_sum = 0
    max_len = 0

    for right in range(len(arr)):
        current_sum += arr[right]

        while current_sum > k:           # shrink
            current_sum -= arr[left]
            left += 1

        max_len = max(max_len, right - left + 1)

    return max_len
```

---

## 10. Prefix Sum in Python

### Manual prefix sum

```python
def build_prefix(arr):
    n = len(arr)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + arr[i]
    return prefix

# Range sum query O(1):
# sum(arr[l..r]) = prefix[r+1] - prefix[l]
```

### With `itertools.accumulate` (cleaner)

```python
from itertools import accumulate

arr = [3, 1, 4, 1, 5]
prefix = [0] + list(accumulate(arr))

def range_sum(l, r):    # inclusive [l, r]
    return prefix[r + 1] - prefix[l]

print(range_sum(1, 3))  # 1+4+1 = 6
```

### Prefix Sum + Counter (subarray sum = k)

```python
from collections import defaultdict

def count_subarrays_sum_k(arr, k):
    count = 0
    prefix_sum = 0
    seen = defaultdict(int)
    seen[0] = 1

    for x in arr:
        prefix_sum += x
        count += seen[prefix_sum - k]
        seen[prefix_sum] += 1

    return count
```

---

## 11. Sorting in Python — All Variants

```python
arr = [3, 1, 4, 1, 5, 9]

# In-place sort (modifies arr, returns None)
arr.sort()                          # ascending
arr.sort(reverse=True)              # descending

# New sorted list (original unchanged)
s = sorted(arr)
s = sorted(arr, reverse=True)

# Sort by key
pairs = [(3, 'c'), (1, 'a'), (2, 'b')]
pairs.sort(key=lambda x: x[0])           # by first element
pairs.sort(key=lambda x: (x[1], x[0]))  # by second, then first

# Sort by absolute value
arr.sort(key=abs)

# Reverse sort + secondary sort trick
# Sort by first descending, second ascending:
data.sort(key=lambda x: (-x[0], x[1]))

# Custom comparator (using functools)
from functools import cmp_to_key
def compare(a, b):
    # return negative if a should come first
    # return positive if b should come first
    return a - b   # ascending

arr.sort(key=cmp_to_key(compare))

# Sort intervals by start time
intervals.sort(key=lambda x: x[0])

# Stable sort: Python's sort is STABLE (Timsort)
# Equal elements maintain their original relative order

# Sort strings by length, then lexicographically
words.sort(key=lambda w: (len(w), w))
```

---

## 12. Input / Output for Competitive Programming

Fast I/O is critical. These patterns save significant time.

```python
import sys
input = sys.stdin.readline   # Faster than built-in input()

# Read single integer
n = int(input())

# Read space-separated integers into a list
arr = list(map(int, input().split()))

# Read n lines, each with one integer
arr = [int(input()) for _ in range(n)]

# Read n lines of space-separated integers (2D)
matrix = [list(map(int, input().split())) for _ in range(n)]

# Read all input at once (fastest for large input)
data = sys.stdin.read().split()
# Then consume data[0], data[1], ... as needed

# Fast output
print(*arr)               # print list space-separated (no loop)
print('\n'.join(map(str, arr)))  # one per line
sys.stdout.write(str(ans) + '\n')  # fastest single output

# Multiple test cases template
import sys
input = sys.stdin.readline

def solve():
    n = int(input())
    arr = list(map(int, input().split()))
    # ... solution here

t = int(input())
for _ in range(t):
    solve()
```

---

## 13. Python-Specific Pitfalls in Array Problems

### The Mutable Default Argument Trap

```python
# WRONG: default mutable argument — shared across calls!
def process(arr=[]):
    arr.append(1)
    return arr
# process() → [1], process() → [1, 1]  ← BUG

# CORRECT:
def process(arr=None):
    if arr is None:
        arr = []
```

### Shallow Copy of 2D Array

```python
# WRONG: shallow copy — rows are shared
import copy
matrix2 = matrix.copy()      # copies outer list, not inner lists
matrix2[0][0] = 99           # changes BOTH matrix and matrix2!

# CORRECT:
matrix2 = [row[:] for row in matrix]  # copy each row
matrix2 = copy.deepcopy(matrix)       # deep copy (slower)
```

### Modifying a List While Iterating

```python
# WRONG:
for x in arr:
    if x < 0:
        arr.remove(x)   # skips elements, unpredictable

# CORRECT (iterate over a copy, or use comprehension):
arr = [x for x in arr if x >= 0]
# or:
for x in arr[:]:   # iterate over a copy
    if x < 0:
        arr.remove(x)
```

### `list * n` Trap for 2D

```python
# WRONG — all rows are the same object:
matrix = [[0] * 3] * 3
matrix[0][1] = 5
# matrix is now [[0,5,0], [0,5,0], [0,5,0]] !

# CORRECT:
matrix = [[0] * 3 for _ in range(3)]
```

### `in` on a List is O(n)

```python
# WRONG for frequent lookups:
seen = []
for x in arr:
    if x not in seen:       # O(n) each time → O(n²) total
        seen.append(x)

# CORRECT: use set
seen = set()
for x in arr:
    if x not in seen:       # O(1) lookup
        seen.add(x)
```

### Using `sum(arr[l:r])` in a Loop

```python
# WRONG: O(n) per query → O(n²) total for many queries
for l, r in queries:
    ans = sum(arr[l:r+1])   # TLE!

# CORRECT: Precompute prefix sum
prefix = [0] + list(accumulate(arr))
for l, r in queries:
    ans = prefix[r+1] - prefix[l]  # O(1)
```

### Integer Division

```python
# In Python 3: / is float division, // is integer division
5 / 2     # 2.5  (float)
5 // 2    # 2    (integer, floor)
-5 // 2   # -3   (floor toward -∞, NOT toward 0 like C++)

# For mid in binary search, use:
mid = (left + right) // 2    # always correct in Python (no overflow)
# In C++: mid = left + (right-left)/2  (to avoid overflow)
# In Python: (left + right) // 2 is fine since no overflow
```

---

## 14. Performance Tips for CP

### Speed hierarchy (fastest → slowest)

```
Built-in C functions (sum, max, min, sorted)
  → much faster than
List comprehensions
  → slightly faster than
For loops with .append()
  → much faster than
While loops doing the same thing
  → much faster than
Recursive Python calls (stack overhead)
```

### Practical tips

**1. Use built-ins over manual loops**
```python
# SLOW:
total = 0
for x in arr: total += x

# FAST:
total = sum(arr)

# SLOW:
maximum = arr[0]
for x in arr:
    if x > maximum: maximum = x

# FAST:
maximum = max(arr)
```

**2. Avoid global variable access in tight loops**
```python
# Wrapping code in a function speeds it up (local variable access is faster)
def solve():
    # all your code here — local vars are faster than globals
    pass
solve()
```

**3. Use `sys.stdin` for fast input**
```python
import sys
input = sys.stdin.readline   # do this always in CP
```

**4. `array` module for large integer arrays**
```python
from array import array
# If n=10⁷ and all integers, this uses far less memory than list
arr = array('i', [0] * n)    # 'i' = signed int, 'l' = long
```

**5. `bytearray` as fast boolean array**
```python
visited = bytearray(n)    # faster than [False]*n for large n
visited[i] = 1            # mark
if visited[i]: ...        # check
```

**6. Avoid `dict` when array (list) suffices**
```python
# If keys are small integers 0..max_val, list is ~3-5x faster than dict
freq = [0] * (max_val + 1)   # instead of defaultdict(int)
freq[x] += 1
```

**7. PyPy > Python for speed**
> If the platform offers PyPy, always choose it. PyPy is typically 5-10x faster than CPython for loop-heavy code. All Python syntax works — just faster.

---

## 15. Python CP Cheat Sheet

```python
# ─── INPUT ────────────────────────────────────────────────
import sys
input = sys.stdin.readline

n = int(input())
arr = list(map(int, input().split()))
matrix = [list(map(int, input().split())) for _ in range(n)]

# ─── ARRAY CREATION ───────────────────────────────────────
zeros   = [0] * n
inf_arr = [float('inf')] * n
visited = [False] * n
matrix  = [[0] * m for _ in range(n)]    # 2D

# ─── USEFUL ONE-LINERS ────────────────────────────────────
# Prefix sum
from itertools import accumulate
prefix = [0] + list(accumulate(arr))
# range_sum(l, r) = prefix[r+1] - prefix[l]

# Frequency count
from collections import Counter
freq = Counter(arr)

# Sort by multiple keys
arr.sort(key=lambda x: (x[0], -x[1]))

# Binary search
import bisect
pos = bisect.bisect_left(arr, target)   # leftmost position

# Top-k
import heapq
top_k = heapq.nlargest(k, arr)

# Flatten 2D
flat = [x for row in matrix for x in row]

# ─── PATTERNS ─────────────────────────────────────────────
# Two Sum (unsorted) → defaultdict
# Two Sum (sorted) → two pointers: left=0, right=n-1
# Subarray sum = k → prefix + defaultdict
# Max subarray sum → Kadane's
# Sliding window → expand right, shrink left
# Sort 0/1/2 → Dutch National Flag (low, mid, high pointers)
# Binary search → bisect module
# Priority queue → heapq
# Deque window → collections.deque

# ─── COMMON PITFALLS ──────────────────────────────────────
# [[0]*m]*n     → WRONG 2D init (shared rows)
# list.pop(0)   → O(n), use deque.popleft()
# x in list     → O(n), use set for O(1)
# arr[l:r] sum  → O(n), use prefix sum
# sort() on int → fine, on tuple → sort key=lambda x: x[0]
# -5 // 2 = -3  → floor division (not truncation like C++)
```

---

> **Python CP Mantra**: *"Use the right tool: Counter for frequency, defaultdict for grouping, deque for O(1) both ends, bisect for sorted search, heapq for top-k, accumulate for prefix sums. Avoid `list.pop(0)`, avoid `x in list`, avoid `sum(arr[l:r])` in loops."*

---

*Python-specific notes — companion to the language-agnostic Array notes.*