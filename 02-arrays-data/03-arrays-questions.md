# Arrays Complete DSA Notes

> Covers all patterns, examples in Python, and curated LeetCode problems from Blind 75, NeetCode 150, and Striver A2Z sheets.

---

## Table of Contents

1. [Core Concepts](#1-core-concepts)
2. [Pattern 1 — Two Pointers](#2-pattern-1--two-pointers)
3. [Pattern 2 — Sliding Window](#3-pattern-2--sliding-window)
4. [Pattern 3 — Prefix Sum](#4-pattern-3--prefix-sum)
5. [Pattern 4 — Kadane's Algorithm](#5-pattern-4--kadanes-algorithm)
6. [Pattern 5 — Binary Search on Arrays](#6-pattern-5--binary-search-on-arrays)
7. [Pattern 6 — Cyclic Sort](#7-pattern-6--cyclic-sort)
8. [Pattern 7 — Dutch National Flag](#8-pattern-7--dutch-national-flag)
9. [Pattern 8 — Merge Intervals](#9-pattern-8--merge-intervals)
10. [Pattern 9 — Hash Map / Frequency Count](#10-pattern-9--hash-map--frequency-count)
11. [Pattern 10 — Monotonic Stack](#11-pattern-10--monotonic-stack)
12. [Pattern 11 — Matrix / 2D Arrays](#12-pattern-11--matrix--2d-arrays)
13. [Time and Space Complexity Quick Reference](#13-time-and-space-complexity-quick-reference)
14. [Problem List by Difficulty](#14-problem-list-by-difficulty)

---

## 1. Core Concepts

### What is an array?

A contiguous block of memory storing elements of the same type.

```
index:  0   1   2   3   4
value: [10, 20, 30, 40, 50]
```

### Key operations

| Operation | Time | Notes |
|---|---|---|
| Access by index | O(1) | Direct memory address |
| Search (unsorted) | O(n) | Linear scan |
| Search (sorted) | O(log n) | Binary search |
| Insert at end | O(1) amortized | Dynamic array |
| Insert at index | O(n) | Shift elements right |
| Delete at index | O(n) | Shift elements left |
| Delete at end | O(1) | Just decrement size |

### When to use arrays

- You need O(1) random access by index
- Data size is fixed or bounded
- Cache performance matters (contiguous memory)

---

## 2. Pattern 1 — Two Pointers

### When to use

- Sorted array problems
- Finding pairs with a target sum
- Palindrome checking
- Removing duplicates in-place
- Container / water problems

### Template

```python
left, right = 0, len(arr) - 1
while left < right:
    if condition:
        left += 1
    elif condition:
        right -= 1
    else:
        # found answer
        break
```

### Example — Two Sum (sorted array)

```python
def two_sum_sorted(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        s = nums[left] + nums[right]
        if s == target:
            return [left + 1, right + 1]   # 1-indexed
        elif s < target:
            left += 1
        else:
            right -= 1
    return []

# Input:  nums = [2, 7, 11, 15], target = 9
# Output: [1, 2]
# Time:   O(n)   Space: O(1)
```

### Example — 3Sum

```python
def three_sum(nums):
    nums.sort()
    result = []
    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue                          # skip duplicates
        left, right = i + 1, len(nums) - 1
        while left < right:
            s = nums[i] + nums[left] + nums[right]
            if s == 0:
                result.append([nums[i], nums[left], nums[right]])
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1
            elif s < 0:
                left += 1
            else:
                right -= 1
    return result

# Time: O(n²)   Space: O(1) ignoring output
```

### Example — Container With Most Water

```python
def max_area(height):
    left, right = 0, len(height) - 1
    max_water = 0
    while left < right:
        water = min(height[left], height[right]) * (right - left)
        max_water = max(max_water, water)
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    return max_water

# Key insight: always move the shorter wall inward
# Time: O(n)   Space: O(1)
```

---

## 3. Pattern 2 — Sliding Window

### When to use

- Contiguous subarray or substring problems
- "Longest / shortest subarray with condition X"
- "Maximum sum of k consecutive elements"

### Two types

**Fixed window** — window size k is given.

```python
# Max sum of k consecutive elements
def max_sum_k(arr, k):
    window_sum = sum(arr[:k])
    max_sum = window_sum
    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]   # slide: add new, remove old
        max_sum = max(max_sum, window_sum)
    return max_sum

# Time: O(n)   Space: O(1)
```

**Variable window** — expand right, shrink left when condition breaks.

```python
# Longest substring without repeating characters
def length_of_longest_substring(s):
    char_set = set()
    left = 0
    max_len = 0
    for right in range(len(s)):
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        char_set.add(s[right])
        max_len = max(max_len, right - left + 1)
    return max_len

# Time: O(n)   Space: O(min(n, alphabet))
```

### Example — Minimum Size Subarray Sum

```python
def min_sub_array_len(target, nums):
    left = 0
    current_sum = 0
    min_len = float('inf')
    for right in range(len(nums)):
        current_sum += nums[right]
        while current_sum >= target:
            min_len = min(min_len, right - left + 1)
            current_sum -= nums[left]
            left += 1
    return 0 if min_len == float('inf') else min_len

# Time: O(n)   Space: O(1)
```

### Example — Best Time to Buy and Sell Stock

```python
def max_profit(prices):
    min_price = float('inf')
    max_profit = 0
    for price in prices:
        min_price = min(min_price, price)           # track lowest buy
        max_profit = max(max_profit, price - min_price)
    return max_profit

# Sliding window: left = buy day, right = sell day
# Time: O(n)   Space: O(1)
```

---

## 4. Pattern 3 — Prefix Sum

### When to use

- Range sum queries in O(1) after O(n) build
- Subarray sum equals K
- Count of subarrays with given sum

### Template

```python
prefix = [0] * (len(arr) + 1)
for i in range(len(arr)):
    prefix[i + 1] = prefix[i] + arr[i]

# Sum of arr[l..r] = prefix[r+1] - prefix[l]
```

### Example — Range Sum Query

```python
class NumArray:
    def __init__(self, nums):
        self.prefix = [0] * (len(nums) + 1)
        for i, n in enumerate(nums):
            self.prefix[i + 1] = self.prefix[i] + n

    def sum_range(self, left, right):
        return self.prefix[right + 1] - self.prefix[left]

# Build: O(n)   Query: O(1)
```

### Example — Subarray Sum Equals K (prefix + hashmap)

```python
def subarray_sum(nums, k):
    count = 0
    prefix_sum = 0
    freq = {0: 1}               # empty subarray has sum 0
    for num in nums:
        prefix_sum += num
        count += freq.get(prefix_sum - k, 0)
        freq[prefix_sum] = freq.get(prefix_sum, 0) + 1
    return count

# Key insight: if prefix[j] - prefix[i] == k, then subarray i..j sums to k
# Time: O(n)   Space: O(n)
```

### Example — Product of Array Except Self (no division)

```python
def product_except_self(nums):
    n = len(nums)
    result = [1] * n
    # left pass: result[i] = product of all elements left of i
    prefix = 1
    for i in range(n):
        result[i] = prefix
        prefix *= nums[i]
    # right pass: multiply by product of all elements right of i
    suffix = 1
    for i in range(n - 1, -1, -1):
        result[i] *= suffix
        suffix *= nums[i]
    return result

# Time: O(n)   Space: O(1) ignoring output
```

---

## 5. Pattern 4 — Kadane's Algorithm

### When to use

- Maximum subarray sum
- Maximum product subarray
- Circular subarray problems

### Core logic

At every index, decide: start fresh OR extend previous subarray.

```python
def max_subarray(nums):
    max_sum = nums[0]
    current = nums[0]
    for num in nums[1:]:
        current = max(num, current + num)   # restart or extend
        max_sum = max(max_sum, current)
    return max_sum

# Input:  [-2, 1, -3, 4, -1, 2, 1, -5, 4]
# Output: 6  (subarray [4, -1, 2, 1])
# Time:   O(n)   Space: O(1)
```

### Example — Maximum Product Subarray

```python
def max_product(nums):
    max_prod = nums[0]
    min_p = max_p = nums[0]        # track both because negatives flip sign
    for num in nums[1:]:
        candidates = (num, max_p * num, min_p * num)
        max_p = max(candidates)
        min_p = min(candidates)
        max_prod = max(max_prod, max_p)
    return max_prod

# Time: O(n)   Space: O(1)
```

---

## 6. Pattern 5 — Binary Search on Arrays

### When to use

- Sorted array: find element, first/last occurrence
- "Minimum / maximum feasible value" → binary search on answer
- Rotated sorted array problems

### Template — standard

```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2    # avoids overflow
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

### Example — Search in Rotated Sorted Array

```python
def search_rotated(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] == target:
            return mid
        # left half is sorted
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        # right half is sorted
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    return -1

# Time: O(log n)   Space: O(1)
```

### Example — Find Minimum in Rotated Array

```python
def find_min(nums):
    left, right = 0, len(nums) - 1
    while left < right:
        mid = (left + right) // 2
        if nums[mid] > nums[right]:
            left = mid + 1       # min is in right half
        else:
            right = mid          # mid could be the min
    return nums[left]

# Time: O(log n)   Space: O(1)
```

---

## 7. Pattern 6 — Cyclic Sort

### When to use

- Array contains numbers in range [1, n] or [0, n]
- Find missing number, find duplicate, first missing positive

### Template

```python
def cyclic_sort(nums):
    i = 0
    while i < len(nums):
        correct = nums[i] - 1              # where nums[i] should be
        if nums[i] != nums[correct]:
            nums[i], nums[correct] = nums[correct], nums[i]
        else:
            i += 1
    return nums
```

### Example — Find Missing Number

```python
def missing_number(nums):
    i = 0
    n = len(nums)
    while i < n:
        if nums[i] < n and nums[i] != nums[nums[i]]:
            nums[i], nums[nums[i]] = nums[nums[i]], nums[i]
        else:
            i += 1
    for i in range(n):
        if nums[i] != i:
            return i
    return n

# Time: O(n)   Space: O(1)
```

### Example — Find All Duplicates

```python
def find_duplicates(nums):
    result = []
    i = 0
    while i < len(nums):
        correct = nums[i] - 1
        if nums[i] != nums[correct]:
            nums[i], nums[correct] = nums[correct], nums[i]
        else:
            i += 1
    for i in range(len(nums)):
        if nums[i] != i + 1:
            result.append(nums[i])
    return result

# Time: O(n)   Space: O(1) ignoring output
```

---

## 8. Pattern 7 — Dutch National Flag

### When to use

- Sort array with 3 distinct values (0s, 1s, 2s)
- Partition around a pivot

### Template (3-way partition)

```python
def sort_colors(nums):
    low = mid = 0
    high = len(nums) - 1
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:                       # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1               # don't increment mid

# Input:  [2, 0, 2, 1, 1, 0]
# Output: [0, 0, 1, 1, 2, 2]
# Time:   O(n)   Space: O(1)
```

---

## 9. Pattern 8 — Merge Intervals

### When to use

- Overlapping interval problems
- Meeting room scheduling
- Insert interval

### Template

```python
def merge(intervals):
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= merged[-1][1]:           # overlaps
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    return merged

# Time: O(n log n)   Space: O(n)
```

### Example — Insert Interval

```python
def insert(intervals, new_interval):
    result = []
    i = 0
    # all intervals ending before new_interval starts
    while i < len(intervals) and intervals[i][1] < new_interval[0]:
        result.append(intervals[i])
        i += 1
    # merge overlapping
    while i < len(intervals) and intervals[i][0] <= new_interval[1]:
        new_interval[0] = min(new_interval[0], intervals[i][0])
        new_interval[1] = max(new_interval[1], intervals[i][1])
        i += 1
    result.append(new_interval)
    # remaining
    result.extend(intervals[i:])
    return result

# Time: O(n)   Space: O(n)
```

---

## 10. Pattern 9 — Hash Map / Frequency Count

### When to use

- Count occurrences
- Anagram problems
- Find duplicates, pairs
- O(1) lookup needed

### Example — Contains Duplicate

```python
def contains_duplicate(nums):
    return len(nums) != len(set(nums))

# Time: O(n)   Space: O(n)
```

### Example — Two Sum (unsorted)

```python
def two_sum(nums, target):
    seen = {}                          # value → index
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []

# Time: O(n)   Space: O(n)
```

### Example — Longest Consecutive Sequence

```python
def longest_consecutive(nums):
    num_set = set(nums)
    max_len = 0
    for num in num_set:
        if num - 1 not in num_set:      # start of a sequence
            length = 1
            while num + length in num_set:
                length += 1
            max_len = max(max_len, length)
    return max_len

# Time: O(n)   Space: O(n)
```

---

## 11. Pattern 10 — Monotonic Stack

### When to use

- Next greater / smaller element
- Daily temperatures
- Histogram / trapping rain water

### Example — Daily Temperatures

```python
def daily_temperatures(temps):
    result = [0] * len(temps)
    stack = []                          # stores indices
    for i, temp in enumerate(temps):
        while stack and temps[stack[-1]] < temp:
            idx = stack.pop()
            result[idx] = i - idx       # days to wait
        stack.append(i)
    return result

# Time: O(n)   Space: O(n)
```

### Example — Trapping Rain Water

```python
def trap(height):
    left, right = 0, len(height) - 1
    left_max = right_max = 0
    water = 0
    while left < right:
        if height[left] < height[right]:
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1
    return water

# Two-pointer approach: O(n) time, O(1) space
```

---

## 12. Pattern 11 — Matrix / 2D Arrays

### Example — Rotate Matrix 90° clockwise

```python
def rotate(matrix):
    n = len(matrix)
    # step 1: transpose (swap across diagonal)
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
    # step 2: reverse each row
    for row in matrix:
        row.reverse()

# Time: O(n²)   Space: O(1)
```

### Example — Spiral Order

```python
def spiral_order(matrix):
    result = []
    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1
    while top <= bottom and left <= right:
        for col in range(left, right + 1):
            result.append(matrix[top][col])
        top += 1
        for row in range(top, bottom + 1):
            result.append(matrix[row][right])
        right -= 1
        if top <= bottom:
            for col in range(right, left - 1, -1):
                result.append(matrix[bottom][col])
            bottom -= 1
        if left <= right:
            for row in range(bottom, top - 1, -1):
                result.append(matrix[row][left])
            left += 1
    return result
```

---

## 13. Time and Space Complexity Quick Reference

| Pattern | Time | Space |
|---|---|---|
| Two pointers | O(n) | O(1) |
| Fixed sliding window | O(n) | O(1) |
| Variable sliding window | O(n) | O(k) |
| Prefix sum build | O(n) | O(n) |
| Prefix sum query | O(1) | — |
| Kadane's | O(n) | O(1) |
| Binary search | O(log n) | O(1) |
| Cyclic sort | O(n) | O(1) |
| Dutch national flag | O(n) | O(1) |
| Merge intervals | O(n log n) | O(n) |
| Hash map / set | O(n) | O(n) |
| Monotonic stack | O(n) | O(n) |
| Matrix traversal | O(n²) | O(1) |

---

## 14. Problem List by Difficulty

### Easy — build fundamentals

| Problem | Pattern | Link |
|---|---|---|
| Two Sum | Hash map | https://leetcode.com/problems/two-sum/ |
| Contains Duplicate | Hash set | https://leetcode.com/problems/contains-duplicate/ |
| Best Time to Buy and Sell Stock | Sliding window | https://leetcode.com/problems/best-time-to-buy-and-sell-stock/ |
| Maximum Subarray | Kadane's | https://leetcode.com/problems/maximum-subarray/ |
| Move Zeroes | Two pointers | https://leetcode.com/problems/move-zeroes/ |
| Remove Duplicates from Sorted Array | Two pointers | https://leetcode.com/problems/remove-duplicates-from-sorted-array/ |
| Missing Number | Cyclic sort / XOR | https://leetcode.com/problems/missing-number/ |
| Single Number | XOR | https://leetcode.com/problems/single-number/ |
| Majority Element | Boyer-Moore | https://leetcode.com/problems/majority-element/ |
| Pascal's Triangle | 2D array | https://leetcode.com/problems/pascals-triangle/ |
| Find Pivot Index | Prefix sum | https://leetcode.com/problems/find-pivot-index/ |
| Running Sum of 1d Array | Prefix sum | https://leetcode.com/problems/running-sum-of-1d-array/ |
| Squares of a Sorted Array | Two pointers | https://leetcode.com/problems/squares-of-a-sorted-array/ |
| Check if Array Is Sorted | Linear scan | https://leetcode.com/problems/check-if-array-is-sorted-and-rotated/ |
| Search Insert Position | Binary search | https://leetcode.com/problems/search-insert-position/ |

---

### Medium — core interview problems

| Problem | Pattern | Link |
|---|---|---|
| Product of Array Except Self | Prefix + suffix | https://leetcode.com/problems/product-of-array-except-self/ |
| 3Sum | Sort + two pointers | https://leetcode.com/problems/3sum/ |
| Container With Most Water | Two pointers | https://leetcode.com/problems/container-with-most-water/ |
| Longest Substring Without Repeating Characters | Sliding window | https://leetcode.com/problems/longest-substring-without-repeating-characters/ |
| Maximum Sum Subarray of Size K | Fixed sliding window | https://leetcode.com/problems/maximum-average-subarray-i/ |
| Minimum Size Subarray Sum | Variable sliding window | https://leetcode.com/problems/minimum-size-subarray-sum/ |
| Subarray Sum Equals K | Prefix sum + hash | https://leetcode.com/problems/subarray-sum-equals-k/ |
| Longest Consecutive Sequence | Hash set | https://leetcode.com/problems/longest-consecutive-sequence/ |
| Sort Colors | Dutch national flag | https://leetcode.com/problems/sort-colors/ |
| Find All Duplicates in an Array | Cyclic sort | https://leetcode.com/problems/find-all-duplicates-in-an-array/ |
| Maximum Product Subarray | Kadane's variant | https://leetcode.com/problems/maximum-product-subarray/ |
| Find Minimum in Rotated Sorted Array | Binary search | https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/ |
| Search in Rotated Sorted Array | Binary search | https://leetcode.com/problems/search-in-rotated-sorted-array/ |
| Merge Intervals | Intervals | https://leetcode.com/problems/merge-intervals/ |
| Insert Interval | Intervals | https://leetcode.com/problems/insert-interval/ |
| Non-overlapping Intervals | Greedy + intervals | https://leetcode.com/problems/non-overlapping-intervals/ |
| Rotate Array | Reverse trick | https://leetcode.com/problems/rotate-array/ |
| Majority Element II | Boyer-Moore | https://leetcode.com/problems/majority-element-ii/ |
| Daily Temperatures | Monotonic stack | https://leetcode.com/problems/daily-temperatures/ |
| Next Permutation | Two pointers | https://leetcode.com/problems/next-permutation/ |
| Spiral Matrix | Matrix simulation | https://leetcode.com/problems/spiral-matrix/ |
| Rotate Image | Matrix | https://leetcode.com/problems/rotate-image/ |
| Set Matrix Zeroes | Matrix | https://leetcode.com/problems/set-matrix-zeroes/ |
| 4Sum | Sort + two pointers | https://leetcode.com/problems/4sum/ |
| Count Subarrays With Sum Divisible by K | Prefix sum + mod | https://leetcode.com/problems/subarray-sums-divisible-by-k/ |
| Jump Game | Greedy | https://leetcode.com/problems/jump-game/ |
| Jump Game II | Greedy | https://leetcode.com/problems/jump-game-ii/ |
| First Missing Positive | Cyclic sort | https://leetcode.com/problems/first-missing-positive/ |

---

### Hard — advanced practice

| Problem | Pattern | Link |
|---|---|---|
| Trapping Rain Water | Two pointers / mono stack | https://leetcode.com/problems/trapping-rain-water/ |
| Minimum Window Substring | Sliding window + hash | https://leetcode.com/problems/minimum-window-substring/ |
| Sliding Window Maximum | Monotonic deque | https://leetcode.com/problems/sliding-window-maximum/ |
| Longest Duplicate Substring | Binary search + rolling hash | https://leetcode.com/problems/longest-duplicate-substring/ |
| Find Duplicate Number | Fast-slow pointers | https://leetcode.com/problems/find-the-duplicate-number/ |
| Maximum Sum Circular Subarray | Kadane's variant | https://leetcode.com/problems/maximum-sum-circular-subarray/ |
| Count of Smaller Numbers After Self | Merge sort / BIT | https://leetcode.com/problems/count-of-smaller-numbers-after-self/ |
| Largest Rectangle in Histogram | Monotonic stack | https://leetcode.com/problems/largest-rectangle-in-histogram/ |
| Maximal Rectangle | Monotonic stack | https://leetcode.com/problems/maximal-rectangle/ |
| First Missing Positive | Cyclic sort | https://leetcode.com/problems/first-missing-positive/ |


## Which sheet covers which

| Sheet | Focus | Link |
|---|---|---|
| Blind 75 | Interview essentials | https://neetcode.io/practice/practice/blind75 |
| NeetCode 150 | Extended coverage | https://neetcode.io/practice |
| Striver A2Z | Full beginner to advanced | https://takeuforward.org/strivers-a2z-dsa-course |
| Tech Interview Handbook | Array cheatsheet | https://www.techinterviewhandbook.org/algorithms/array/ |