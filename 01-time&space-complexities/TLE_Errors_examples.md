# Time Complexity — 30+ Worked Calculations and Edge Cases

> This file is pure practice. Every example shows the full step-by-step calculation, the math check, the constraint check, and where people go wrong. No shortcuts. Work through each one.

---

## How to Use This File

Every example follows this structure:
1. See the code or problem
2. Do the step-by-step calculation
3. Check against LeetCode constraint
4. Do the math (ops / 10⁸ = seconds)
5. Pass or TLE verdict

The math rule: **1 second ≈ 10⁸ operations**. LeetCode time limit is usually 1–2 seconds.

---

## Part 1 — The Core Math: Constraint to Complexity

Before touching any code, this is the check you do in your head.

### How the math works

```
Operations = f(n)  where n comes from the constraint
Time = Operations / 10⁸   (in seconds)
If Time > 2 seconds → TLE
```

### The master table (memorise this)

| Max n | Max operations allowed | Max complexity |
|---|---|---|
| 10 | 10⁸ | O(n!) works |
| 20 | 10⁸ | O(2ⁿ) works |
| 100 | 10⁸ | O(n⁴) works |
| 500 | 10⁸ | O(n³) works |
| 2,000 | 10⁸ | O(n²) works |
| 10,000 | 10⁸ | O(n²) is borderline |
| 100,000 | 10⁸ | O(n log n) or O(n) only |
| 1,000,000 | 10⁸ | O(n) only |
| 10⁹ | 10⁸ | O(log n) or O(sqrt n) only |

---

## Part 2 — Constraint Check Examples (10 examples)

These are the 10-second checks you do before writing a single line of code.

---

### Example 1 — Two Sum brute force check

**Problem:** Find two numbers that sum to target. Array size up to n = 10⁵.
**Your idea:** Two nested loops checking every pair.

**Step 1: What is the complexity?**
```
for i in range(n):
    for j in range(i+1, n):
        if nums[i] + nums[j] == target:
            return [i, j]

Outer loop: n times
Inner loop: roughly n times each
Total: O(n²)
```

**Step 2: Plug in the constraint**
```
n = 10⁵
Operations = (10⁵)² = 10¹⁰
```

**Step 3: Calculate seconds**
```
10¹⁰ / 10⁸ = 100 seconds
```

**Verdict: TLE. Never submit this.**

**Fix:** Use a hash map. Single pass through the array.
```python
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i

# Operations = n = 10⁵
# Time = 10⁵ / 10⁸ = 0.001 seconds → PASS
```

---

### Example 2 — Sorting check

**Problem:** Sort an array. n = 10⁵.
**Your idea:** Bubble sort (nested loops).

**Step 1: Complexity of bubble sort**
```
Outer loop: n times
Inner loop: n times
Total: O(n²)
```

**Step 2: Math**
```
n = 10⁵
Operations = (10⁵)² = 10¹⁰
Time = 10¹⁰ / 10⁸ = 100 seconds
```

**Verdict: TLE.**

**Fix:** Use merge sort or Python's built-in sort (Tim sort).
```
Operations = n log n = 10⁵ × log₂(10⁵) = 10⁵ × 17 ≈ 1.7 × 10⁶
Time = 1.7 × 10⁶ / 10⁸ = 0.017 seconds → PASS
```

---

### Example 3 — Binary search check

**Problem:** Search a sorted array. n = 10⁹.
**Your idea:** Linear scan.

**Step 1: Complexity of linear scan**
```
Single loop: O(n)
```

**Step 2: Math**
```
n = 10⁹
Operations = 10⁹
Time = 10⁹ / 10⁸ = 10 seconds
```

**Verdict: TLE.**

**Fix:** Binary search.
```
Operations = log₂(10⁹) ≈ 30 steps
Time = 30 / 10⁸ ≈ 0.0000003 seconds → PASS instantly
```

---

### Example 4 — Matrix multiplication check

**Problem:** Multiply two n×n matrices. n = 300.
**Your idea:** Triple nested loop.

**Step 1: Complexity**
```
for i in range(n):
    for j in range(n):
        for k in range(n):
            result[i][j] += A[i][k] * B[k][j]

Total: O(n³)
```

**Step 2: Math**
```
n = 300
Operations = 300³ = 27,000,000 = 2.7 × 10⁷
Time = 2.7 × 10⁷ / 10⁸ = 0.27 seconds → PASS
```

**What if n = 600?**
```
Operations = 600³ = 216,000,000 = 2.16 × 10⁸
Time = 2.16 × 10⁸ / 10⁸ = 2.16 seconds → Borderline, likely TLE
```

**Key insight:** O(n³) is only safe up to about n = 500.

---

### Example 5 — Floyd-Warshall (all pairs shortest path)

**Problem:** Find shortest path between all pairs. n nodes up to n = 400.

**Step 1: Complexity**
```
for k in range(n):           # n
    for i in range(n):       # n
        for j in range(n):   # n
            ...
Total: O(n³)
```

**Step 2: Math**
```
n = 400
Operations = 400³ = 64,000,000 = 6.4 × 10⁷
Time = 6.4 × 10⁷ / 10⁸ = 0.64 seconds → PASS
```

**If n = 700:**
```
Operations = 700³ = 343,000,000 = 3.43 × 10⁸
Time = 3.43 × 10⁸ / 10⁸ = 3.43 seconds → TLE
```

---

### Example 6 — Subsets (backtracking)

**Problem:** Generate all subsets of an array. n = 20.

**Step 1: Complexity**
```
Each element is either in or out: 2 choices per element
Total subsets = 2ⁿ
Generating each takes O(n) time to copy
Total: O(n × 2ⁿ)
```

**Step 2: Math**
```
n = 20
Operations = 20 × 2²⁰ = 20 × 1,048,576 ≈ 2 × 10⁷
Time = 2 × 10⁷ / 10⁸ = 0.2 seconds → PASS
```

**If n = 25:**
```
Operations = 25 × 2²⁵ = 25 × 33,554,432 ≈ 8.4 × 10⁸
Time = 8.4 × 10⁸ / 10⁸ = 8.4 seconds → TLE
```

**Key insight:** O(2ⁿ) is safe only up to about n = 23.

---

### Example 7 — Permutations (backtracking)

**Problem:** Generate all permutations. n = 8.

**Step 1: Complexity**
```
n! permutations, each takes O(n) to generate
Total: O(n × n!)
```

**Step 2: Math**
```
n = 8
Operations = 8 × 8! = 8 × 40,320 = 322,560 → trivially fast

n = 12
Operations = 12 × 12! = 12 × 479,001,600 ≈ 5.7 × 10⁹
Time = 5.7 × 10⁹ / 10⁸ = 57 seconds → TLE
```

**Key insight:** O(n!) is only safe up to about n = 10 or 11.

---

### Example 8 — Dijkstra's algorithm

**Problem:** Shortest path in a graph. V vertices, E edges. V = 10⁵, E = 2 × 10⁵.

**Step 1: Complexity with min-heap**
```
Each vertex processed once: O(V)
Each edge relaxed once: O(E)
Each heap operation: O(log V)
Total: O((V + E) log V)
```

**Step 2: Math**
```
V = 10⁵, E = 2 × 10⁵
Operations = (10⁵ + 2 × 10⁵) × log₂(10⁵)
           = 3 × 10⁵ × 17
           = 5.1 × 10⁶
Time = 5.1 × 10⁶ / 10⁸ = 0.051 seconds → PASS easily
```

---

### Example 9 — Dynamic programming on a grid

**Problem:** Count paths in an m×n grid. m = n = 1000.

**Step 1: Complexity**
```
for i in range(m):
    for j in range(n):
        dp[i][j] = dp[i-1][j] + dp[i][j-1]

Total: O(m × n)
```

**Step 2: Math**
```
m = n = 1000
Operations = 1000 × 1000 = 10⁶
Time = 10⁶ / 10⁸ = 0.01 seconds → PASS
```

**If m = n = 10⁴:**
```
Operations = 10⁴ × 10⁴ = 10⁸
Time = 10⁸ / 10⁸ = 1 second → borderline, might TLE in Python
```

---

### Example 10 — Prefix sum

**Problem:** Range sum queries. Array size n = 10⁵, up to 10⁵ queries.

**Naive approach:** For each query, loop through the range.
```
Each query: O(n)
All queries: O(n × q) = O(n²)
Operations = (10⁵)² = 10¹⁰ → TLE
```

**Prefix sum approach:** Precompute once, answer in O(1).
```
Build prefix array: O(n)
Each query: O(1)
All queries: O(q) = O(n)
Total: O(n)
Operations = 10⁵ + 10⁵ = 2 × 10⁵
Time = 2 × 10⁵ / 10⁸ = 0.002 seconds → PASS
```

---

## Part 3 — Code Reading Examples (10 examples)

Now read code and calculate complexity step by step.

---

### Example 11 — Dependent nested loop

```python
def example11(n):
    count = 0
    for i in range(n):
        for j in range(i, n):    # j starts from i, not 0
            count += 1
    return count
```

**Step 1: Count inner iterations per outer iteration**
```
i=0: j runs from 0 to n-1 → n iterations
i=1: j runs from 1 to n-1 → n-1 iterations
i=2: j runs from 2 to n-1 → n-2 iterations
...
i=n-1: j runs from n-1 to n-1 → 1 iteration
```

**Step 2: Sum it up**
```
Total = n + (n-1) + (n-2) + ... + 1
      = n(n+1)/2
      = (n² + n) / 2
```

**Step 3: Apply Big O rules**
```
Drop constant: (n² + n) / 2 → n² + n
Drop lower term: n² + n → n²
Result: O(n²)
```

---

### Example 12 — Multiplication not addition

```python
def example12(n):
    i = 1
    while i < n:
        j = 1
        while j < n:
            j *= 3          # j triples each time
        i *= 2              # i doubles each time
```

**Step 1: Inner loop**
```
j starts at 1, triples each time: 1 → 3 → 9 → 27 → ...
Runs until j >= n
Number of steps = log₃(n)
```

**Step 2: Outer loop**
```
i starts at 1, doubles each time: 1 → 2 → 4 → 8 → ...
Runs until i >= n
Number of steps = log₂(n)
```

**Step 3: Nested, so multiply**
```
Total = log₂(n) × log₃(n)
```

Both are logarithms. Logs of different bases differ only by a constant factor (log change of base formula). So:
```
Result: O(log²n)   (log n squared, sometimes written O((log n)²))
```

---

### Example 13 — Three separate blocks

```python
def example13(arr):
    n = len(arr)

    # Block 1
    arr.sort()                      # O(n log n)

    # Block 2
    for i in range(n):              # O(n)
        print(arr[i])

    # Block 3
    for i in range(n):              # O(n²) total
        for j in range(n):
            print(arr[i] + arr[j])
```

**Step 1: Complexity of each block**
```
Block 1: O(n log n)
Block 2: O(n)
Block 3: O(n²)
```

**Step 2: Sequential blocks → ADD, then take dominant term**
```
Total = O(n log n) + O(n) + O(n²)
      = O(n log n + n + n²)
      = O(n²)    ← n² dominates
```

---

### Example 14 — Sort then binary search in a loop

```python
def find_pairs(arr, targets):
    arr.sort()                               # O(n log n)
    results = []
    for t in targets:                        # O(m) iterations
        lo, hi = 0, len(arr) - 1
        while lo <= hi:                      # O(log n) each
            mid = (lo + hi) // 2
            if arr[mid] == t:
                results.append(mid)
                break
            elif arr[mid] < t:
                lo = mid + 1
            else:
                hi = mid - 1
    return results
```

**Step 1: Each piece**
```
Sort: O(n log n)
Loop over targets: m iterations
Binary search inside: O(log n) per iteration
Total for loop: O(m log n)
```

**Step 2: Add**
```
Total = O(n log n) + O(m log n)
      = O((n + m) log n)
```

**If m ≈ n:**
```
Total = O(n log n)
```

---

### Example 15 — Recursion with memoization

```python
memo = {}
def dp(i, j, grid):
    if i >= len(grid) or j >= len(grid[0]):
        return 0
    if (i, j) in memo:
        return memo[(i, j)]
    result = grid[i][j] + max(dp(i+1, j, grid), dp(i, j+1, grid))
    memo[(i, j)] = result
    return result
```

**Step 1: How many unique states?**
```
i ranges from 0 to m-1 → m values
j ranges from 0 to n-1 → n values
Total unique (i, j) pairs = m × n
```

**Step 2: Work per state**
```
Each state computed once (memoization)
Work per state = O(1) (just max of two values)
```

**Step 3: Total**
```
Result: O(m × n)
```

---

### Example 16 — String operations in a loop

```python
def build_string_bad(words):
    result = ""
    for word in words:
        result += word + " "    # BAD
    return result

def build_string_good(words):
    parts = []
    for word in words:
        parts.append(word)      # O(1) amortized
    return " ".join(parts)      # O(total characters)
```

**Why the bad version is O(n²):**
```
Iteration 1: result = "hello " → copies 6 chars
Iteration 2: result = "hello world " → copies 12 chars (re-copies "hello ")
Iteration 3: result = "hello world foo " → copies 17 chars
...
Iteration k: copies roughly k × avg_word_length chars

Total copies ≈ 1 + 2 + 3 + ... + n = n(n+1)/2 = O(n²)
```

**Why the good version is O(n):**
```
append() is O(1) amortized (see amortized section below)
join() makes exactly one pass → O(total characters) = O(n)
Total: O(n)
```

---

### Example 17 — Hidden O(n) inside O(n) loop

```python
def example17(arr):
    n = len(arr)
    for i in range(n):           # O(n)
        if arr[i] in arr:        # O(n) ← searching a LIST is O(n)!
            print(arr[i])
```

**Step 1: `in` operator on a list is O(n)**
```
Python's `in` on a list does a linear scan
arr[i] in arr → checks every element → O(n)
```

**Step 2: This is inside an O(n) loop**
```
Total = O(n) × O(n) = O(n²)
```

**Looks like O(n) but is actually O(n²). This is a very common bug.**

**Fix:**
```python
arr_set = set(arr)               # O(n) to build
for i in range(n):               # O(n)
    if arr[i] in arr_set:        # O(1) ← set lookup is O(1)
        print(arr[i])

Total: O(n) + O(n) = O(n)
```

---

### Example 18 — Sorting inside a loop

```python
def example18(matrix):
    result = []
    for row in matrix:                      # n rows
        sorted_row = sorted(row)            # O(m log m) per row
        result.append(sorted_row[0])
    return result
```

**n = number of rows, m = number of columns**

**Step 1: Inner operation**
```
sorted(row) on a row of m elements → O(m log m)
```

**Step 2: Loop**
```
Loop runs n times
Each iteration: O(m log m)
Total: O(n × m log m)
```

**If it's a square matrix (n = m):**
```
Total = O(n × n log n) = O(n² log n)
```

This is worse than O(n²). Sort outside the loop if possible.

---

### Example 19 — Sliding window

```python
def max_sum_subarray(arr, k):
    n = len(arr)
    window_sum = sum(arr[:k])      # O(k)
    max_sum = window_sum

    for i in range(k, n):          # O(n - k) iterations
        window_sum += arr[i]       # O(1)
        window_sum -= arr[i - k]   # O(1)
        max_sum = max(max_sum, window_sum)

    return max_sum
```

**Step 1: Initial window sum**
```
sum(arr[:k]) → O(k)
```

**Step 2: The loop**
```
Runs n - k times
Each iteration: O(1)
Total: O(n - k)
```

**Step 3: Add**
```
Total = O(k) + O(n - k) = O(n)
```

**Compare to naive approach:**
```
Naive: O(n × k) — for each starting position, sum k elements
Sliding window: O(n)

If n = 10⁵, k = 10⁴:
Naive: 10⁵ × 10⁴ = 10⁹ operations → TLE
Sliding window: 10⁵ operations → 0.001 sec
```

---

### Example 20 — Two pointers

```python
def pair_sum_sorted(arr, target):
    left = 0
    right = len(arr) - 1

    while left < right:         # at most n iterations total
        current = arr[left] + arr[right]
        if current == target:
            return [left, right]
        elif current < target:
            left += 1           # O(1)
        else:
            right -= 1          # O(1)
    return []
```

**Step 1: How many iterations?**
```
left starts at 0, right starts at n-1
Each iteration: left increases OR right decreases
They can only cross at most n times
Total iterations: O(n)
```

**Step 2: Work per iteration**
```
O(1) arithmetic and comparison
```

**Total: O(n)**

**Compare to brute force:**
```
Brute force pairs: O(n²)
Two pointers: O(n)

At n = 10⁵:
Brute force: 10¹⁰ ops → 100 seconds → TLE
Two pointers: 10⁵ ops → 0.001 sec → PASS
```

---

## Part 4 — Edge Cases and Tricky Situations (10 examples)

These are the ones that look simple but catch people out.

---

### Example 21 — list.pop(0) in a loop

```python
def process_queue_bad(arr):
    while arr:
        item = arr.pop(0)     # O(n) each time! shifts all elements
        process(item)

def process_queue_good(arr):
    from collections import deque
    q = deque(arr)
    while q:
        item = q.popleft()    # O(1)
        process(item)
```

**Why pop(0) is O(n):**
```
When you remove index 0 from a Python list, every element shifts left by one.
n elements → n shifts → O(n)
```

**Bad version complexity:**
```
Loop: n iterations
Each pop(0): O(n)
Total: O(n²)
```

**Good version complexity:**
```
Loop: n iterations
Each popleft(): O(1)
Total: O(n)
```

**At n = 10⁵:**
```
Bad: (10⁵)² = 10¹⁰ → TLE
Good: 10⁵ → 0.001 sec
```

---

### Example 22 — Python list slicing

```python
def example22(arr):
    result = 0
    for i in range(len(arr)):
        sub = arr[i:]           # O(n - i) to create a copy
        result += sum(sub)      # O(n - i)
    return result
```

**arr[i:] creates a new list by copying n-i elements → O(n-i)**

**Step 1: Inner work per iteration**
```
i=0: arr[0:] → copies n elements + sum → O(n)
i=1: arr[1:] → copies n-1 elements + sum → O(n-1)
...
i=n-1: arr[n-1:] → copies 1 element + sum → O(1)
```

**Step 2: Sum**
```
Total = n + (n-1) + (n-2) + ... + 1 = n(n+1)/2 = O(n²)
```

**Looks like O(n) but is O(n²) because of the slice.**

---

### Example 23 — `sorted()` on a list vs `in` on a set

```python
# Checking membership — which is faster?

arr = [1, 2, 3, ..., n]
target = 42

# Option A
arr.sort()
# now arr is sorted, binary search: O(log n)
import bisect
bisect.bisect_left(arr, target)

# Option B
arr_set = set(arr)
target in arr_set    # O(1) average
```

**Building the set: O(n)**
**Lookup: O(1) average**

**If you do one lookup:** Set wins. O(n) to build + O(1) lookup = O(n).
**If you sort and do many lookups:** Sort costs O(n log n) once, then O(log n) per lookup.

**When to use which:**
```
Few lookups (< log n): use set
Many lookups: sort + binary search is O(n log n + m log n)
Many lookups with set: O(n + m)
```

---

### Example 24 — Amortized O(1) for list append

```python
arr = []
for i in range(n):
    arr.append(i)    # O(1) amortized
```

**What actually happens internally:**
```
Python lists double in size when full.
Occasionally append copies the entire list → O(n)
But this happens at sizes 1, 2, 4, 8, 16, ...

Total copies for n appends:
1 + 2 + 4 + 8 + ... + n = 2n - 1 ≈ 2n

Total cost over n appends = O(2n) = O(n)
Average cost per append = O(n) / n = O(1)
```

**This is amortized O(1). Not worst-case O(1). Rarely an issue in practice.**

---

### Example 25 — Hash map collision worst case

```python
seen = {}
for num in arr:
    if num in seen:    # O(1) average, O(n) worst case
        return True
    seen[num] = True
```

**Average case: O(n)** — hash map lookups are O(1) on average.

**Worst case: O(n²)** — if all keys hash to the same bucket, every lookup scans the whole bucket.

**In practice:** Python's hash function is very good. Collisions are rare for standard types. For coding interviews, treat dict/set operations as O(1).

**When it becomes a real problem:** When an attacker can craft inputs to force collisions (hash flooding). Python uses randomized hash seeds to mitigate this.

---

### Example 26 — Recursion stack depth

```python
def sum_recursive(arr, index=0):
    if index == len(arr):
        return 0
    return arr[index] + sum_recursive(arr, index + 1)
```

**Time complexity: O(n)** — n recursive calls, each O(1).

**But here is the edge case:**
```
Each recursive call adds a frame to the call stack.
Python's default recursion limit = 1000.
For n > 1000 → RecursionError, not TLE.
```

**Fix for large n:**
```python
import sys
sys.setrecursionlimit(10**6)   # increase limit

# Or better: use iteration
def sum_iterative(arr):
    total = 0
    for x in arr:
        total += x
    return total   # O(n) time, O(1) space
```

**The hidden cost of deep recursion: O(n) space on the call stack.**

---

### Example 27 — BFS and DFS on a graph

```python
def bfs(graph, start):
    from collections import deque
    visited = set()
    queue = deque([start])
    visited.add(start)

    while queue:
        node = queue.popleft()    # O(1)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

**V = number of vertices, E = number of edges**

**Step 1: How many times does each vertex get processed?**
```
Each vertex enters the queue at most once → O(V) total dequeues
```

**Step 2: How many times do we iterate over edges?**
```
For each vertex, we look at all its neighbors
Total neighbor checks = sum of all adjacency lists = E
Total: O(E)
```

**Step 3: Total**
```
O(V) + O(E) = O(V + E)
```

**Practical check with n = 10⁵ vertices and 10⁵ edges:**
```
Operations = 10⁵ + 10⁵ = 2 × 10⁵
Time = 2 × 10⁵ / 10⁸ = 0.002 seconds → PASS easily
```

---

### Example 28 — Multiple inputs with different sizes

```python
def process(arr1, arr2):
    n = len(arr1)
    m = len(arr2)

    for x in arr1:              # O(n)
        for y in arr2:          # O(m) each
            print(x + y)
```

**This is NOT O(n²). It is O(n × m).**

**Why it matters:**
```
If arr1 has 10 elements and arr2 has 10⁵ elements:
n × m = 10 × 10⁵ = 10⁶ → PASS

If you mistakenly called it O(n²) with n = 10⁵:
You would calculate (10⁵)² = 10¹⁰ → and wrongly conclude TLE
```

**Always use separate variables for different input sizes.**

---

### Example 29 — Sorting changes everything

```python
def find_duplicate_pairs(arr):
    arr.sort()                      # O(n log n)
    count = 0
    for i in range(len(arr) - 1):
        if arr[i] == arr[i+1]:     # O(1) after sorting
            count += 1
    return count
```

**Without sort approach:**
```
Naive: for every element, check all others → O(n²)
```

**With sort approach:**
```
Sort: O(n log n)
Single pass: O(n)
Total: O(n log n) — dominant term
```

**Sorting first converts an O(n²) problem into O(n log n). Always ask: "would sorting help?"**

---

### Example 30 — Monotonic stack

```python
def next_greater_element(arr):
    n = len(arr)
    result = [-1] * n
    stack = []

    for i in range(n):
        while stack and arr[stack[-1]] < arr[i]:
            idx = stack.pop()
            result[idx] = arr[i]
        stack.append(i)

    return result
```

**This looks like O(n²) because of the while loop inside the for loop. Is it?**

**Step 1: How many times can an element be pushed onto the stack?**
```
Each element is pushed exactly once.
```

**Step 2: How many times can an element be popped?**
```
Each element is popped at most once.
```

**Step 3: Total push + pop operations**
```
n pushes + at most n pops = 2n operations total
```

**Result: O(n) — not O(n²). The while loop does not run n times per outer iteration. Over the entire loop, total pops ≤ n.**

**This is the amortized analysis in action. The while loop looks expensive but the total work is bounded.**

---

### Example 31 — Merge intervals

```python
def merge_intervals(intervals):
    intervals.sort(key=lambda x: x[0])     # O(n log n)
    merged = [intervals[0]]

    for start, end in intervals[1:]:        # O(n)
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)    # O(1)
        else:
            merged.append([start, end])     # O(1) amortized

    return merged
```

**Step 1: Sort**
```
O(n log n)
```

**Step 2: Single pass through sorted intervals**
```
O(n) — each interval examined once
Each operation inside is O(1)
```

**Total: O(n log n) + O(n) = O(n log n)**

**Constraint check:**
```
n = 10⁴ intervals
Operations = n log n = 10⁴ × 14 = 1.4 × 10⁵
Time = 1.4 × 10⁵ / 10⁸ = 0.0014 seconds → PASS easily
```

---

### Example 32 — DP with 1D optimization

```python
# 2D DP solution — O(n × amount) time, O(n × amount) space
def coin_change_2d(coins, amount):
    n = len(coins)
    dp = [[float('inf')] * (amount + 1) for _ in range(n + 1)]
    for i in range(n + 1):
        dp[i][0] = 0
    for i in range(1, n + 1):
        for j in range(1, amount + 1):
            dp[i][j] = dp[i-1][j]
            if coins[i-1] <= j:
                dp[i][j] = min(dp[i][j], dp[i][j - coins[i-1]] + 1)
    return dp[n][amount]

# 1D DP solution — same time, O(amount) space
def coin_change_1d(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    for coin in coins:              # O(n)
        for j in range(coin, amount + 1):    # O(amount)
            dp[j] = min(dp[j], dp[j - coin] + 1)
    return dp[amount]
```

**Both have the same time complexity:**
```
O(n × amount)
```

**Constraint check:**
```
n = 12 coins, amount = 10⁴
Operations = 12 × 10⁴ = 1.2 × 10⁵
Time = 1.2 × 10⁵ / 10⁸ = 0.0012 seconds → PASS

n = 100 coins, amount = 10⁶
Operations = 100 × 10⁶ = 10⁸
Time = 10⁸ / 10⁸ = 1 second → tight, might TLE in Python
```

---

## Part 5 — Quick Calculation Cheat Sheet

This is your 10-second mental math guide.

### The log₂ values you need to know

```
log₂(10) ≈ 3.3
log₂(100) ≈ 7
log₂(1,000) ≈ 10
log₂(10,000) ≈ 13
log₂(100,000) ≈ 17
log₂(1,000,000) ≈ 20
log₂(10⁹) ≈ 30
```

### Operations at common n × complexity combinations

| n | O(n) | O(n log n) | O(n²) | O(n³) | verdict at each |
|---|---|---|---|---|---|
| 10² | 10² | 700 | 10⁴ | 10⁶ | all pass |
| 10³ | 10³ | 10,000 | 10⁶ | 10⁹ | n³ borderline |
| 10⁴ | 10⁴ | 130,000 | 10⁸ | 10¹² | n³ TLE, n² borderline |
| 10⁵ | 10⁵ | 1.7 × 10⁶ | 10¹⁰ | 10¹⁵ | n² TLE, n log n pass |
| 10⁶ | 10⁶ | 2 × 10⁷ | 10¹² | — | only O(n) safe |

### Time in seconds (divide operations by 10⁸)

| Operations | Time | Verdict |
|---|---|---|
| 10⁶ | 0.01 sec | Always pass |
| 10⁷ | 0.1 sec | Pass |
| 10⁸ | 1 sec | Borderline |
| 10⁹ | 10 sec | TLE |
| 10¹⁰ | 100 sec | Hard TLE |

### Python-specific constant factor warning

Python is roughly 5–10x slower than C++ for the same operations. LeetCode accounts for this with longer time limits, but you should still be careful near the boundary.

If your solution is borderline (1–2 seconds in theory), it might TLE in Python even if it passes in C++. In that case, look for a strictly better complexity or avoid Python-slow operations like inner function calls in tight loops.

---

## Part 6 — The 5-Step Pre-Coding Checklist

Every single LeetCode problem, before writing code:

```
Step 1: Read the constraint. What is max n?

Step 2: From the constraint, what complexity do you need?
        n ≤ 10⁵ → need O(n log n) or better
        n ≤ 2000 → O(n²) is fine
        etc.

Step 3: Think of your approach. What is its complexity?
        Write it down explicitly.

Step 4: Do the math.
        operations = f(n) with the actual max n
        time = operations / 10⁸
        Is time < 2 seconds?

Step 5: If yes, code it. If no, think of a better approach first.
        Do NOT code a TLE solution hoping it will pass.
```

---

*Use this file as a problem bank. Pick any example, cover the answer, calculate it yourself, then check. Do 5 of these every day for the first week and constraint-reading becomes automatic.*