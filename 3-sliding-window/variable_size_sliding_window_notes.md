# Variable Size Sliding Window — Complete Conceptual Notes

## 1. What Is the Sliding Window Pattern?

- Used for problems involving **contiguous subarrays and substrings**.
- A *window* is a continuous portion of an array or string between two indices.
- In a **variable-size sliding window**, the window expands or shrinks depending on the problem's condition.

**Common problems**
- Longest subarray with sum = `K`
- Smallest subarray with sum ≥ `K`
- Longest substring without repeating characters
- Longest subarray satisfying a given constraint

### How does a window move?

- `start` → left boundary of the window.
- `end` → right boundary of the window.
- Move `end` right to include new elements.
- Move `start` right to remove elements from the window when needed.

**Example array:** `[1, 2, 1, 1, 1]`

```text
Index:    0   1   2   3   4
Array:   [1] [2] [1] [1] [1]

Initial window:
          [1]  2   1   1   1
           ^
         start = end = 0

Expand right:
          [1] [2]  1   1   1
           ^   ^
         start end

Shrink from left (remove index 0):
           1  [2]  1   1   1
               ^   ^
             start end
```

- The window contains only the elements between `start` and `end`, inclusive.
- When shrinking, the element at `start` leaves the window, then `start` increments.
- When expanding, `end` moves forward and the new element enters the window.

## 2. Brute Force Approach

**Idea:** Generate every possible subarray and check whether it satisfies the problem's condition.

Example: Find the longest subarray whose sum equals `K`.

```javascript
for (let i = 0; i < n; i++) {
    let sum = 0;

    for (let j = i; j < n; j++) {
        sum += arr[j];

        if (sum === k) {
            // Update maximum length
        }
    }
}
```

- `i` → starting index of the subarray.
- `j` → ending index of the subarray.
- For every `i`, move `j` from `i` to the end of the array.
- Add each new element to `sum` and validate the condition.

### Drawbacks

- Generates and checks every possible subarray.
- Repeatedly explores overlapping subarrays.
- Even if a subarray is not useful for the answer, it is still checked.

### Complexity

- **Time:** `O(n²)` when maintaining the running sum.
- **Auxiliary space:** `O(1)`, excluding output storage.
- If each subarray's sum is recalculated from scratch, time can reach `O(n³)`.

## 3. Sliding Window Approach

**Core idea:** We do not need to generate every possible subarray. We maintain one current window, reuse its calculations, and move its boundaries to find the answer efficiently.

Example: `arr = [1, 2, 1, 1, 1]`, `K = 3`. Find the longest subarray whose sum equals `K`.

### The 3-step approach

**Step 1 — Expand the window**
- Start with `start = 0`, `sum = 0`.
- Use a `for` loop to move `end` from left to right.
- Add the new element: `sum += arr[end]`.

**Step 2 — Shrink the window if needed**
- Check the problem's constraint.
- Here, if `sum > K`, the window is invalid (assuming non-negative elements).
- Remove elements from the left using a `while` loop:
  - `sum -= arr[start]`
  - `start++`
- Continue until `sum <= K`.

**Step 3 — Validate and update the answer**
- If `sum === K`, calculate the current window length.
- If the length is greater than `maxLen`, update:
  - `maxLen`
  - `ansStart`
  - `ansEnd`

Then continue expanding the window until `end` reaches the last index.

### Dry run: Follow the pointers

Input: `arr = [1, 2, 1, 1, 1]`, `K = 3`

| `end` | Action | `start` | `sum` | Current window | `maxLen` |
|---:|---|---:|---:|---|---:|
| 0 | Add 1 | 0 | 1 | `[1]` | 0 |
| 1 | Add 2 | 0 | 3 | `[1, 2]` | 2 |
| 2 | Add 1, remove leftmost 1 | 1 | 3 | `[2, 1]` | 2 |
| 3 | Add 1, remove leftmost 2 | 2 | 2 | `[1, 1]` | 2 |
| 4 | Add 1 | 2 | 3 | `[1, 1, 1]` | 3 |

**Final answer:** Length = `3`; subarray = `[1, 1, 1]`; indices = `2` to `4`.

### What happens in each iteration?

1. `end` advances and adds a new element to the window.
2. If the window violates the condition, `start` advances until the window is valid again.
3. Check whether the window satisfies the exact target condition.
4. Update the answer if this window is better than the best found so far.

## 4. Complete Code

```javascript
let arr = [1, 2, 1, 1, 1];
let k = 3;

let start = 0;
let sum = 0;
let maxLen = 0;
let ansStart = -1;
let ansEnd = -1;

for (let end = 0; end < arr.length; end++) {

    // 1. Expand
    sum += arr[end];

    // 2. Shrink while invalid
    while (sum > k) {
        sum -= arr[start];
        start++;
    }

    // 3. Validate and update
    if (sum === k) {
        let len = end - start + 1;

        if (len > maxLen) {
            maxLen = len;
            ansStart = start;
            ansEnd = end;
        }
    }
}

console.log(maxLen); // 3
console.log(arr.slice(ansStart, ansEnd + 1)); // [1, 1, 1]
```

## 5. Generalize the Algorithm for Other Problems

Remember the structure, but adapt the condition and update logic to the specific question.

| Step | What to do |
|---|---|
| 1. Initialize | Set `start`, window state and answer variables |
| 2. Expand | Move `end` right and include the new element |
| 3. Shrink | Use `while` to restore the required window condition |
| 4. Update | Record the length, count or other required answer |
| 5. Repeat | Continue until `end` reaches the array's end |

### How the condition changes by problem

- **Longest subarray with sum = K:** For non-negative elements, shrink while `sum > K`, then update when `sum === K`.
- **Smallest subarray with sum ≥ K:** For positive elements, update while the window is valid, then shrink to find a smaller valid window.
- **Longest substring without repeating characters:** Shrink while duplicates exist, then update the longest length.

**Important:** The order of shrinking and updating is problem-dependent. Do not copy the exact condition blindly from one problem to another.

## 6. Important Formulas and Complexity

- **Window length:** `end - start + 1`
- **Add an element:** `sum += arr[end]`
- **Remove an element:** `sum -= arr[start]`
- **Move left boundary:** `start++`
- **Typical time complexity:** `O(n)` when each pointer advances at most `n` times.
- **Auxiliary space:** `O(1)` when maintaining a constant number of variables. Some problems, such as tracking distinct characters, need additional data structures.

## 7. Main Concept to Remember

**Brute force:** Generate all possible subarrays and validate them.

**Sliding window:** Maintain a current window, expand it to explore new elements, shrink it when the constraint requires, and update the answer at the correct point.

The optimization works because the pointers move forward rather than restarting the search for every possible starting index.

### Important limitation

The standard sum-based sliding window does not generally work for arbitrary arrays containing negative numbers. For problems such as longest subarray with sum `K` when negative values are allowed, consider **prefix sum + hashmap** instead.
