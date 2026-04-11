# **Space Complexity**

Space complexity measures **how much memory (RAM) an algorithm uses** as the input size `n` grows toward infinity. It uses the **same asymptotic notations** (Big O, Ω, Θ, o, ω) as time complexity — just applied to **memory** instead of operations.

---

## Key Formula

```
Space Complexity = Input Space + Auxiliary Space
```

**Input space** — memory occupied by the input itself (e.g., an array of size `n` takes O(n) space).

**Auxiliary space** — the *extra* temporary memory the algorithm needs during execution: variables, temporary arrays, recursion call stack, hash maps, etc.

> **Important (Interview Convention):** When people say "space complexity is O(1)", they almost always mean the *auxiliary space* is constant — input space is ignored because every algorithm solving the same problem receives the same input. Always clarify which you mean.

---

## Fixed vs Variable Space

Every algorithm's memory usage has two parts:

**Fixed part** — independent of input size. Includes instruction/code space, constants, and simple scalar variables (loop counters, pointers, a `max_val` variable). Always O(1).

**Variable part** — depends on input size. Includes dynamically allocated arrays, hash maps that grow with `n`, and the recursion call stack whose depth changes with input.

```
S(algorithm) = Fixed part + Variable part
```

Only the variable part matters for Big O analysis — constants are dropped.

---

## Why Space Complexity Matters

Memory is a hard constraint in a way that time is not. An algorithm that runs slowly will merely be slow. An algorithm that exceeds available memory will **crash**. This is especially critical in embedded systems (microcontrollers with kilobytes of RAM), competitive programming (strict memory limits), and big-data pipelines (datasets too large to fit in RAM).

There is also a fundamental **time–space trade-off**: you can often buy faster time by using more space (caching/memoization), or reduce space by doing more work (recomputing instead of storing).

---

## How to Calculate Space Complexity — Step by Step

1. List every variable and data structure **created inside** the algorithm.
2. Ignore the input array/structure itself unless you are asked for total space (not auxiliary).
3. For each variable, determine whether its size depends on `n`.
4. For recursive algorithms, find the **maximum recursion depth** — each call adds one stack frame of constant size.
5. Sum all memory contributions and identify the dominant term.
6. Drop constants and lower-order terms → express in Big O.

---

## The Recursion Rule (Critical)

> **Auxiliary space of a recursive algorithm = height of the recursion tree × space per stack frame.**

Each recursive call pushes a new frame onto the call stack. The maximum number of frames alive at any moment equals the deepest path in the recursion tree.

**Example — Recursive sum:**

```python
def rec_sum(n):
    if n <= 0:
        return 0
    return n + rec_sum(n - 1)
```

Call chain: `rec_sum(5) → rec_sum(4) → rec_sum(3) → rec_sum(2) → rec_sum(1) → rec_sum(0)`

Maximum depth = n + 1 frames → auxiliary space = **O(n)**.

**Important contrast — iterative calls that don't overlap:**

```python
def pair_sum_sequence(n):
    total = 0
    for i in range(n):
        total += pair_sum(i, i + 1)   # each call returns before the next starts
    return total
```

Even though `pair_sum` is called ~n times, at most **one** frame exists at any moment → auxiliary space = **O(1)**. The key is whether calls are *simultaneous* (recursive) or *sequential* (iterative).

---

## Common Space Complexities

| Space Complexity | Name | Cause | Examples |
|---|---|---|---|
| **O(1)** | Constant | Few scalar variables, in-place operations | Selection Sort, Insertion Sort, Heap Sort, iterative Binary Search, swapping two numbers |
| **O(log n)** | Logarithmic | Recursion stack depth = height of balanced tree | Quick Sort (average case), recursive Binary Search |
| **O(n)** | Linear | Temporary array of size n, or recursion depth = n | Merge Sort, Counting Sort, naive recursive Fibonacci, Hash Map/Set |
| **O(n log n)** | Linearithmic | Rare for space | Some advanced divide-and-conquer with extra bookkeeping |
| **O(n²)** | Quadratic | 2D matrix or DP table of size n × n | Naive LCS DP table, naive matrix multiplication output |

---

## Detailed Algorithm Examples

### 1. Selection Sort — O(1) Auxiliary Space

```python
def selection_sort(arr):
    n = len(arr)
    for i in range(n):               # i: O(1)
        min_idx = i                  # min_idx: O(1)
        for j in range(i+1, n):      # j: O(1)
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]   # temp swap: O(1)
```

Only a handful of integer variables exist. No extra array is created. Auxiliary space = **O(1)**. Total space (including input) = O(n).

---

### 2. Merge Sort — O(n) Auxiliary Space

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])     # creates sub-arrays
    right = merge_sort(arr[mid:])
    return merge(left, right)        # temp array of size n during merge
```

A temporary array of size `n` is needed during the merge step. Recursion depth = log n (much smaller). The temporary array dominates → auxiliary space = **O(n)**. Time = O(n log n) — the classic time–space trade-off.

---

### 3. Quick Sort — O(log n) Average, O(n) Worst

```
Average case: pivot splits array roughly in half each time
→ recursion depth ≈ log n
→ auxiliary space = O(log n)

Worst case: already sorted array + naive pivot (first/last element)
→ recursion depth = n (degenerates to linear chain)
→ auxiliary space = O(n)
```

With randomized pivot or median-of-three, worst case becomes O(log n) in practice. Quick Sort uses **no extra array** for merging — all swaps are in-place — so the only auxiliary space is the call stack.

---

### 4. Binary Search — Iterative vs Recursive

```python
# Iterative — O(1) auxiliary space
def binary_search_iterative(arr, target):
    low, high = 0, len(arr) - 1        # two variables: O(1)
    while low <= high:
        mid = (low + high) // 2
        if arr[mid] == target: return mid
        elif arr[mid] < target: low = mid + 1
        else: high = mid - 1
    return -1

# Recursive — O(log n) auxiliary space
def binary_search_recursive(arr, target, low, high):
    if low > high: return -1
    mid = (low + high) // 2
    if arr[mid] == target: return mid
    elif arr[mid] < target:
        return binary_search_recursive(arr, target, mid+1, high)
    else:
        return binary_search_recursive(arr, target, low, mid-1)
    # Max recursion depth = log n → O(log n) stack space
```

Same time complexity, different space — always prefer iterative when recursion depth becomes the bottleneck.

---

### 5. Hash Map / Frequency Counter — O(n) Auxiliary

```python
def has_duplicate(arr):
    seen = {}                     # hash map grows up to n entries
    for x in arr:
        if x in seen: return True
        seen[x] = True
    return False
```

In the worst case (all elements unique), the hash map stores `n` keys → auxiliary space = **O(n)**.

---

### 6. Naive Recursive Fibonacci — O(n) Auxiliary

```python
def fib(n):
    if n <= 1: return n
    return fib(n-1) + fib(n-2)
```

Although the recursion **tree** has ~2ⁿ nodes, the call **stack** only holds one path from root to leaf at any time. The deepest path has length `n` → auxiliary space = **O(n)**, not O(2ⁿ).

Time complexity is O(2ⁿ) because many sub-problems are recomputed — but space only tracks simultaneous stack frames.

**Optimized iterative Fibonacci — O(1) auxiliary:**

```python
def fib_iterative(n):
    if n <= 1: return n
    a, b = 0, 1
    for _ in range(2, n+1):
        a, b = b, a + b          # only two variables at any time
    return b
```

---

## Time–Space Trade-offs — Very Important for Interviews

| Scenario | Approach | Time | Space |
|---|---|---|---|
| Merge Sort | Extra temp array | O(n log n) | O(n) |
| Heap Sort | In-place heap | O(n log n) | **O(1)** |
| Counting/Radix Sort | Extra buckets | O(n + k) | O(n + k) |
| Naive Fibonacci | Recompute everything | O(2ⁿ) | O(n) stack |
| Memoized Fibonacci | Cache results | O(n) | O(n) table |
| Iterative Fibonacci | Track only two vars | O(n) | **O(1)** |
| LCS DP table | Full n×m table | O(nm) | O(nm) |
| LCS optimized | Two rows only | O(nm) | **O(min(n,m))** |

> **Core insight:** Heap Sort and Merge Sort have identical time complexity O(n log n), but Heap Sort is in-place (O(1) auxiliary) while Merge Sort needs O(n) extra space. In memory-constrained environments, Heap Sort wins on space at the cost of worse cache performance in practice.

---

## Space Complexity of Common Data Structures

| Data Structure | Space |
|---|---|
| Array of size n | O(n) |
| Hash Map / Hash Set (n entries) | O(n) |
| Stack / Queue (n elements) | O(n) |
| Binary Tree (n nodes) | O(n) |
| Adjacency List graph (V vertices, E edges) | O(V + E) |
| Adjacency Matrix graph (V vertices) | O(V²) |
| 2D DP table (n rows, m cols) | O(nm) |

---

## Proof-Style Analysis Example

**Problem: Find the sum of an array.**

```python
def array_sum(arr):
    total = 0           # one integer variable: O(1)
    for x in arr:       # loop variable x: O(1)
        total += x
    return total
```

Memory analysis:
- `total`: one integer → O(1)
- `x`: one integer → O(1)
- Input `arr`: n integers → O(n)
- No recursion, no extra arrays.

Auxiliary space = O(1). Total space = O(n).

**Limit verification (same technique as time complexity):**

```
Auxiliary memory / g(n) = O(1) / O(1) = constant = finite → O(1) confirmed
```

---

## Quick Checklist — Analyze Any Algorithm

| Question | Answer → Space |
|---|---|
| Is it in-place? No extra arrays? | O(1) auxiliary |
| Creates one extra array of size n? | O(n) auxiliary |
| Recursive? | O(maximum recursion depth) |
| Uses hash map or set? | O(n) worst case |
| 2D matrix or DP table? | O(n²) or O(n × m) |
| Recursive, but calls are sequential (not nested)? | O(1) auxiliary |

---

## Common Misconceptions

**"Recursive Fibonacci is O(2ⁿ) space."** — False. O(2ⁿ) is the *time* complexity (number of function calls). Space tracks simultaneous stack frames only — the maximum path depth is n, so auxiliary space is O(n).

**"Space complexity always includes input size."** — Depends on context. In interviews and most algorithm analysis, "space complexity" means auxiliary space only. In competitive programming, total space (input + auxiliary) is usually what matters.

**"In-place means O(1) total space."** — False. In-place means O(1) *auxiliary* space. The input itself still occupies O(n) space. Total space is always at least O(n) for algorithms that read n elements.

**"Sequential function calls stack up like recursion."** — False. If function B completes before function C is called, only one frame exists at any time, giving O(1) stack space regardless of how many total calls are made.

---

## Interview Pro Tip

Always state both complexities explicitly:

> "Time complexity: O(n log n). Space complexity: O(n) auxiliary (for the merge buffer), O(log n) recursion stack."

Examiners expect you to distinguish auxiliary from total, and to explain *why* — which data structure or which recursion depth drives the space cost.

---

## Complete Algorithm Reference Table

| Algorithm | Time | Auxiliary Space | Notes |
|---|---|---|---|
| Linear Search | O(n) | O(1) | No extra memory |
| Binary Search (iterative) | O(log n) | O(1) | Two pointers only |
| Binary Search (recursive) | O(log n) | O(log n) | Recursion depth |
| Bubble Sort | O(n²) | O(1) | In-place swaps |
| Selection Sort | O(n²) | O(1) | In-place |
| Insertion Sort | O(n²) | O(1) | In-place |
| Merge Sort | O(n log n) | O(n) | Temp merge buffer |
| Quick Sort (average) | O(n log n) | O(log n) | Recursion stack |
| Quick Sort (worst) | O(n²) | O(n) | Degenerate recursion |
| Heap Sort | O(n log n) | O(1) | In-place heap |
| Counting Sort | O(n + k) | O(n + k) | Frequency array |
| Fibonacci (naive recursive) | O(2ⁿ) | O(n) | Stack depth = n |
| Fibonacci (memoized) | O(n) | O(n) | DP table |
| Fibonacci (iterative) | O(n) | O(1) | Two variables |
| BFS / DFS on graph | O(V + E) | O(V) | Queue/stack for visited |
| Naive Matrix Multiply | O(n³) | O(n²) | Output matrix |