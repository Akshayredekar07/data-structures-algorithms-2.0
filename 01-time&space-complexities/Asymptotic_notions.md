# **The Five Asymptotic Notations** 

Asymptotic notation describes how an algorithm's runtime or space grows as input **n → ∞**. Constants and lower-order terms are dropped — only the dominant growth rate matters.


## **Quick Reference Overview**

| Notation | Name | Type | Limit Condition | f = g Allowed? |
|---|---|---|---|---|
| **O(g)** | Big O | Upper bound | 0 ≤ L < ∞ (finite) | Yes |
| **Ω(g)** | Big Omega | Lower bound | L > 0 (or ∞) | Yes |
| **Θ(g)** | Theta | Tight bound | 0 < L < ∞ | Yes |
| **o(g)** | Little o | Strict upper | L = 0 | No |
| **ω(g)** | Little omega | Strict lower | L = ∞ | No |

**Growth order (slowest → fastest):**

```
O(1) < O(log N) < O(N) < O(N log N) < O(N²) < O(N³) < O(2ᴺ) < O(N!)
```


## **1. Big O — O(g(n)) — Worst-Case Upper Bound**

> **"My algorithm will never be slower than this."**

### Formal Definition

**f(n) = O(g(n))** if there exist constants `c > 0` and `n₀ ≥ 0` such that:

```
0 ≤ f(n) ≤ c·g(n)   for all n ≥ n₀
```

**Limit Form:**

```
lim(n→∞)  f(n)/g(n) = L   where  0 ≤ L < ∞  (finite)
```

If the limit is **finite** (including 0), then `f(n) = O(g(n))`. f grows *no faster* than g.

### Proof Examples

**Proof 1 — Is 6N³ + 3N + 5 = O(N³)?**

```
lim(N→∞)  (6N³ + 3N + 5) / N³
= lim(N→∞)  [6 + 3/N² + 5/N³]
= 6 + 0 + 0
= 6

6 is finite ✓  →  6N³ + 3N + 5 = O(N³)
Lower-order terms (3N, 5) vanish as N → ∞. Dropped.
```

**Proof 2 — Is 5N² + 100N = O(N²)?**

```
lim(N→∞)  (5N² + 100N) / N²
= lim(N→∞)  [5 + 100/N]
= 5 + 0 = 5

5 is finite ✓  →  Yes, O(N²)
100N sounds big, but at N=1,000,000 it becomes 0.0001% of 5N². Ignored.
```

**Proof 3 — Is 2ᴺ = O(N³)?**

```
lim(N→∞)  2ᴺ / N³  = ∞

∞ is NOT finite ✗  →  2ᴺ ≠ O(N³)
Exponential functions are never bounded by a polynomial.
```

### Real Algorithm Examples

| Algorithm | Complexity | Reason |
|---|---|---|
| Linear Search | O(N) | Scans every element in worst case |
| Binary Search | O(log N) | Halves search space each step |
| Bubble Sort | O(N²) | Nested comparisons |
| Merge Sort | O(N log N) | Divide and merge |
| Hash Table Lookup | O(1) avg | Direct address via hash |
| Naive Matrix Multiply | O(N³) | Three nested loops |

> **Common Mistake:** Big O is *not* automatically "worst case" — it is a mathematical upper bound. However, in industry and interviews, Big O is habitually used for worst-case analysis.

---

## 2. Big Omega — Ω(g(n)) — Best-Case Lower Bound

> **"My algorithm will never be faster than this."**

### Formal Definition

**f(n) = Ω(g(n))** if there exist constants `c > 0` and `n₀ ≥ 0` such that:

```
0 ≤ c·g(n) ≤ f(n)   for all n ≥ n₀
```

**Limit Form:**

```
lim(n→∞)  f(n)/g(n) = L   where  L > 0  (or ∞)
```

If the limit is **positive or infinite**, then `f(n) = Ω(g(n))`. f grows *at least as fast* as g.

### Proof Examples

**Proof 1 — Is 6N³ + 3N + 5 = Ω(N³)?**

```
lim(N→∞)  (6N³ + 3N + 5) / N³  =  6

6 > 0 ✓  →  Yes, Ω(N³)
Same limit as Big O proof — finite AND > 0, so satisfies BOTH O and Ω!
```

**Proof 2 — Is N³ = Ω(N²)?**

```
lim(N→∞)  N³ / N²  =  lim(N→∞)  N  =  ∞

∞ > 0 ✓  →  Yes, N³ = Ω(N²)
N³ grows much faster than N², so the lower bound holds.
```

### Real Algorithm Examples

| Algorithm | Complexity | Reason |
|---|---|---|
| Linear Search (best case) | Ω(1) | Target found at index 0 |
| Binary Search (best case) | Ω(1) | Target found at midpoint |
| Merge Sort | Ω(N log N) | Always splits and merges |
| Bubble Sort (best case) | Ω(N) | One pass confirms sorted |
| Any algorithm reading all input | Ω(N) | Must touch every element |

> **Key Use:** Big Omega is essential for proving *optimality*. The famous result that no comparison-based sort can beat **Ω(N log N)** proves that Merge Sort and Heap Sort are optimal — no algorithm can do better.

---

## 3. Theta — Θ(g(n)) — Tight Bound

> **"The algorithm is exactly this complexity — best and worst are the same order."**

### Formal Definition

**f(n) = Θ(g(n))** if and only if `f(n) = O(g(n))` AND `f(n) = Ω(g(n))`. There exist constants `c₁, c₂ > 0` and `n₀` such that:

```
c₁·g(n) ≤ f(n) ≤ c₂·g(n)   for all n ≥ n₀
```

**Limit Form:**

```
lim(n→∞)  f(n)/g(n) = L   where  0 < L < ∞  (positive AND finite)
```

The limit must be **strictly between 0 and ∞**. Theta = Big O + Big Omega together.

### Proof Examples

**Proof 1 — Is 6N³ + 3N + 5 = Θ(N³)?**

```
lim(N→∞)  (6N³ + 3N + 5) / N³  =  6

0 < 6 < ∞ ✓  →  Yes, Θ(N³)
Limit is positive AND finite → tight bound confirmed.
```

**Proof 2 — Is N² = Θ(N³)?**

```
lim(N→∞)  N² / N³  =  lim(N→∞)  1/N  =  0

0 is NOT strictly between 0 and ∞ ✗  →  N² ≠ Θ(N³)
N² grows slower than N³ so they cannot share the same tight bound.
```

### When Can You Use Theta?

Only when the algorithm behaves the same way regardless of input — i.e., best case and worst case have the same asymptotic growth.

| Algorithm | Theta? | Reason |
|---|---|---|
| Find Max in Array | **Θ(N)** | Must always scan all N elements |
| Merge Sort | **Θ(N log N)** | Always splits and merges |
| Array Index Access | **Θ(1)** | Always constant |
| Linear Search | Cannot use Θ | Best = Ω(1), Worst = O(N) |
| Binary Search | Cannot use Θ | Best = Ω(1), Worst = O(log N) |

> **Critical Misconception:** Θ is **NOT** "average case." Average case is probabilistic. Theta is deterministic — it means f is sandwiched between c₁·g(n) and c₂·g(n) for all large n. If best ≠ worst case, you cannot use Theta.

---

## 4. Little o — o(g(n)) — Strict Upper Bound

> **"My algorithm is definitively slower than g — it can never equal g's growth rate."**

### Formal Definition

**Limit Form:**

```
lim(n→∞)  f(n)/g(n) = 0   (must be EXACTLY zero)
```

### Big O vs Little o — The Key Distinction

| Notation | Inequality | f = g allowed? |
|---|---|---|
| Big O | f ≤ c·g | **Yes** — `N² = O(N²)` is valid |
| Little o | f < c·g for every c > 0 | **No** — `N² = o(N²)` is NOT valid |

### Proof Examples

**Proof 1 — Is N² = o(N³)?**

```
lim(N→∞)  N² / N³  =  lim(N→∞)  1/N  =  0  ✓

Yes! N² grows strictly slower than N³.
```

**Proof 2 — Is N³ = o(N³)?**

```
lim(N→∞)  N³ / N³  =  1  ≠  0  ✗

No! A function CANNOT be little-o of itself.
This is the critical difference from Big O. N³ = O(N³) is valid, but N³ = o(N³) is NOT.
```

**Proof 3 — Is log N = o(N)?**

```
lim(N→∞)  log N / N  =  0  ✓

Yes, logarithmic growth is strictly slower than linear growth.
```

### More Examples at a Glance

| Claim | Limit Result | Valid? |
|---|---|---|
| N = o(N²) | 1/N → 0 | Yes |
| N² = o(N²) | 1 → 1 | No |
| N log N = o(N²) | log N / N → 0 | Yes |
| 5N = o(N) | 5 → 5 | No (same order) |
| 2ᴺ = o(3ᴺ) | (2/3)ᴺ → 0 | Yes |

> **When Used:** Little o appears in mathematical proofs and theoretical comparisons. It expresses that one algorithm is *asymptotically strictly better* than another — e.g., saying a new algorithm is `o(N²)` means it is strictly faster than any quadratic algorithm.

---

## 5. Little omega — ω(g(n)) — Strict Lower Bound

> **"My algorithm is definitively faster-growing than g — it can never equal g's rate."**

Little omega is the mirror image of little o.

### Formal Definition

**Limit Form:**

```
lim(n→∞)  f(n)/g(n) = ∞   (must be EXACTLY infinity)
```

### Big Omega vs Little omega — The Key Distinction

| Notation | Inequality | f = g allowed? |
|---|---|---|
| Big Omega | f ≥ c·g | **Yes** — `N² = Ω(N²)` is valid |
| Little omega | f > c·g for every c > 0 | **No** — `N² = ω(N²)` is NOT valid |

### Proof Examples

**Proof 1 — Is N³ = ω(N²)?**

```
lim(N→∞)  N³ / N²  =  lim(N→∞)  N  =  ∞  ✓

Yes! N³ grows strictly faster than N².
```

**Proof 2 — Is N² = ω(N²)?**

```
lim(N→∞)  N² / N²  =  1  ≠  ∞  ✗

No! A function CANNOT be little-omega of itself.
Same logic as little o — strict means no equality.
```

**Proof 3 — Is 2ᴺ = ω(N¹⁰⁰)?**

```
lim(N→∞)  2ᴺ / N¹⁰⁰  =  ∞  ✓

Yes! Exponential always eventually dominates ANY polynomial,
no matter how large the exponent.
Even N¹⁰⁰ cannot keep up with 2ᴺ at large values.
```

### More Examples at a Glance

| Claim | Limit Result | Valid? |
|---|---|---|
| N² = ω(N) | N → ∞ | Yes |
| N² = ω(N²) | 1 → 1 | No |
| N³ = ω(N log N) | N²/log N → ∞ | Yes |
| N = ω(log N) | N/log N → ∞ | Yes |

---

## Master Comparison Table

| Notation | Type | Limit Condition | Inequality | f = g Allowed? | Used In Practice? |
|---|---|---|---|---|---|
| **O(g)** | Upper bound | 0 ≤ L < ∞ | f ≤ c·g | Yes | Always |
| **Ω(g)** | Lower bound | L > 0 (or ∞) | f ≥ c·g | Yes | Sometimes |
| **Θ(g)** | Tight bound | 0 < L < ∞ | c₁·g ≤ f ≤ c₂·g | Yes | Often |
| **o(g)** | Strict upper | L = 0 | f < c·g ∀c > 0 | No | Rarely |
| **ω(g)** | Strict lower | L = ∞ | f > c·g ∀c > 0 | No | Rarely |

---

## Real Algorithm Summary

| Algorithm | Big O | Big Ω | Theta? |
|---|---|---|---|
| Linear Search | O(N) | Ω(1) | Cannot use Θ |
| Binary Search | O(log N) | Ω(1) | Cannot use Θ |
| Bubble Sort | O(N²) | Ω(N) | Cannot use Θ |
| Merge Sort | O(N log N) | Ω(N log N) | **Θ(N log N)** |
| Find Max in Array | O(N) | Ω(N) | **Θ(N)** |
| Array Index Access | O(1) | Ω(1) | **Θ(1)** |
| Naive Matrix Multiply | O(N³) | Ω(N³) | **Θ(N³)** |

> **Rule of Thumb:** If the algorithm must do the same work regardless of input (Merge Sort always splits and merges, Find Max always scans all elements), use Theta. If the algorithm can short-circuit on lucky inputs (Linear Search finds target at index 0), best ≠ worst — use O and Ω separately.

---

## Final Analogy — Mumbai to Pune (150 km)

| Notation | Meaning |
|---|---|
| **O** | "I'll arrive within 4 hours." Upper bound — could be faster, but never slower. |
| **Ω** | "I'll take at least 1.5 hours." Lower bound — minimum possible, no matter how lucky. |
| **Θ** | "It takes exactly 2.5 hours every time." Tight bound — same regardless of conditions. |
| **o** | "I'll take strictly less than 4 hours." Strictly under the bound — never equals it. |
| **ω** | "I'll take strictly more than 1.5 hours." Strictly over the bound — never equals it. |

---

## Common Misconceptions Clarified

### 1. "Big O = Worst Case"

False. Big O is a mathematical upper bound. You can say "best case is O(1)" — it means 1 is an upper bound on the best-case behaviour. In practice, engineers habitually use Big O for worst-case analysis, but the two concepts are not the same by definition.

### 2. "Theta = Average Case"

False. Theta is a tight asymptotic bound — f is sandwiched between c₁·g(n) and c₂·g(n) for all large n. Average case is a probabilistic concept depending on input distribution. They are related only when the expected runtime equals the tight bound.

### 3. "Constants Don't Matter At All"

True asymptotically, but for small n, an O(N²) algorithm with tiny constants can outperform an O(N log N) algorithm with large overhead. Asymptotic notation describes trends as n → ∞, not behaviour at small sizes.

### 4. "Little o and ω Are Just Academic"

They appear in approximation algorithms, parameterized complexity, and when rigorously proving that one complexity class is strictly separate from another — e.g., that polynomial time is strictly easier than exponential time.

---

## Practice Problems

Try proving these using the limit technique. Compute lim(n→∞) f(n)/g(n) and apply the correct rule.

| # | Claim | Expected Answer |
|---|---|---|
| 1 | 100N + log N = Θ(N) | Limit = 100. 0 < 100 < ∞ → Θ(N) |
| 2 | N log N = o(N²) | log N / N → 0 → Little o |
| 3 | 2ᴺ = ω(N¹⁰⁰) | Limit = ∞ → Little omega |
| 4 | N² + N log N = O(N²) | Limit = 1. Finite → Big O |
| 5 | √N = ω(log N) | √N / log N → ∞ → Little omega |