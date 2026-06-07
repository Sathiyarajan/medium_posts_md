# Cracking Two Sum: From Brute Force to Hash Map Mastery

> *From a naive O(n²) brute force to an elegant O(n) hash map — every step explained.*

**Tags:** Python · Hash Maps · Arrays · LeetCode Easy

---

## The Problem

Two Sum is the first problem on LeetCode, and for good reason. It's the perfect introduction to one of the most important ideas in competitive programming: *trading space for speed*.

Given an array of integers `nums` and an integer `target`, return the indices of the two numbers that add up to `target`. You may assume exactly one solution exists.

```python
# Example
nums   = [2, 7, 11, 15]
target = 9
# Output: [0, 1]  because  2 + 7 = 9
```

---

## Approach 1 — Brute Force

The most natural instinct: check every pair of numbers and see if they sum to `target`. Two loops, simple logic.

```python
def twoSum_brute(nums, target):
    n = len(nums)

    for i in range(n):              # first number
        for j in range(i + 1, n):  # second (always after i)
            if nums[i] + nums[j] == target:
                return [i, j]
```

Notice `j = i + 1` — this ensures we never pair a number with itself, and we never check the same pair twice.

> **Why not j = 0?** Starting j from 0 would pair each number with itself (e.g. `nums[0] + nums[0]`), which is invalid. Starting from `i + 1` guarantees j is always strictly ahead of i.

**Complexity:**
- Time: O(n²)
- Space: O(1)
- At n = 10,000: ~50 million operations

For small arrays this is fine. But at scale, two nested loops become painfully slow. We need a better idea.

---

## Approach 2 — Hash Map (Optimal)

Here's the key insight that unlocks the optimal solution:

> *"For every number n, its partner is target − n. Instead of searching the whole array for that partner, store what we've already seen in a hash map for O(1) lookup."*

We walk through the array once. At each number, we ask: *have I already seen its complement?* If yes — we're done. If no — store the current number for future lookups.

### The Algorithm Step by Step

1. Create an empty dictionary `seen` that maps a value to its index.
2. For each number `n` at index `i`, compute `diff = target - n`.
3. Check if `diff` is already in `seen`. If yes — return `[seen[diff], i]`.
4. Otherwise store `seen[n] = i` and continue.

```python
def twoSum(nums, target):
    seen = {}                         # value → index

    for i, n in enumerate(nums):
        diff = target - n             # the partner we need

        if diff in seen:              # O(1) lookup — found it!
            return [seen[diff], i]

        seen[n] = i                   # store after checking
```

### Tracing Through an Example

Let's trace `nums = [2, 7, 11, 15]` with `target = 9`:

| i | n  | diff (9-n) | diff in seen? | seen after   |
|---|----|------------|---------------|--------------|
| 0 | 2  | 7          | No            | {2: 0}       |
| 1 | 7  | 2          | **Yes ✓ → return [0, 1]** | —  |

We only needed 2 iterations instead of checking all pairs.

Let's trace a harder example: `nums = [3, 5, -4, 8, 11, 1, -1]`, `target = 10`:

| i | n  | diff | in seen?                    |
|---|----|------|-----------------------------|
| 0 | 3  | 7    | No → store {3:0}            |
| 1 | 5  | 5    | No → store {3:0, 5:1}       |
| 2 | -4 | 14   | No → store {..., -4:2}      |
| 3 | 8  | 2    | No → store {..., 8:3}       |
| 4 | 11 | -1   | No → store {..., 11:4}      |
| 5 | 1  | 9    | No → store {..., 1:5}       |
| 6 | -1 | 11   | **Yes ✓ → return [4, 6]**   |

**Complexity:**
- Time: O(n)
- Space: O(n)
- At n = 10,000: ~10,000 operations

---

## A Subtle Edge Case — Duplicates

What if the answer pair is two of the same number, like `nums = [3, 3]` with `target = 6`?

```
i=0, n=3  →  diff=3  →  not in {}          →  store {3: 0}
i=1, n=3  →  diff=3  →  found at index 0!  →  return [0, 1] ✓
```

It works because we store `seen[n] = i` *after* the check. At `i=0` we haven't stored 3 yet, so we can't accidentally match it with itself. By the time `i=1` checks, the first 3 is safely in `seen`.

---

## Brute Force vs Hash Map

| | Brute Force | Hash Map |
|---|---|---|
| Time | O(n²) | O(n) |
| Space | O(1) | O(n) |
| Loops | 2 (nested) | 1 |
| n = 10,000 | ~50M ops | ~10K ops |
| Strategy | Check all pairs | Store & lookup |

The two approaches represent a fundamental trade-off that appears throughout algorithm design:

- **Brute force** uses no extra memory but checks every pair — O(n²) time, O(1) space.
- **Hash map** spends O(n) memory to store what it has seen — and buys O(n) time in return.

This trade-off — *pay in space to save in time* — is the core idea behind hash maps, memoization, caching, and many other optimizations you'll encounter throughout LeetCode.

---

## The Takeaway

Two Sum teaches a pattern that recurs in dozens of problems: *for each element, ask what its complement looks like, and use a hash map to check for it in O(1)*. Once this thinking clicks, problems like Three Sum, Four Sum, and Subarray Sum Equals K all start to feel approachable.

Next time you see a problem asking "do any two elements satisfy some condition", reach for a hash map first.

---

*Tags: #Python #LeetCode #HashMaps #Arrays #DataStructures #CodingInterview*
