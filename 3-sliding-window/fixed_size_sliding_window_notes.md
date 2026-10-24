# Fixed Size Sliding Window — Complete Conceptual Notes

## 1. What Is the Fixed Size Sliding Window Pattern?

- Used for problems involving **contiguous subarrays or substrings of a fixed length `k`**.
- The window always contains exactly `k` elements once it is fully formed.
- `start` is the left boundary and `end` is the right boundary.
- Unlike a variable-size window, we do **not** shrink based on a condition. We move the window one position at a time.
- Common problems:
  - Maximum or minimum sum of a subarray of size `k`
  - Average of every subarray of size `k`
  - Maximum element in every window of size `k`
  - First negative number in every window of size `k`
  - Count anagrams using a window of length `p.length`

### How does a fixed-size window move?

Example: `arr = [2, 1, 5, 1, 3, 2]`, `k = 3`

```text
Window 1: [2, 1, 5]  1   3   2
Window 2:  2 [1, 5, 1]  3   2
Window 3:  2   1 [5, 1, 3]  2
Window 4:  2   1   5 [1, 3, 2]
```

- Each move removes one element from the left and adds one new element from the right.
- The window length stays `k = 3`.

## 2. Brute Force Approach

**Example:** Find the maximum sum of any subarray of size `k = 3`.

```js
const arr = [2, 1, 5, 1, 3, 2];
const k = 3;
let maxSum = -Infinity;

for (let start = 0; start <= arr.length - k; start++) {
  let sum = 0;

  for (let j = start; j < start + k; j++) {
    sum += arr[j];
  }

  maxSum = Math.max(maxSum, sum);
}

console.log(maxSum); // 9
```

- The outer loop chooses each possible starting index.
- The inner loop adds all `k` elements in that window.
- Overlapping windows recalculate most of the same elements.

**Complexity**
- **Time:** `O(n * k)`.
- **Auxiliary space:** `O(1)`.

## 3. Fixed Size Sliding Window Approach

**Core idea:** Calculate the first window once. For each next window, reuse the previous result instead of recalculating all `k` elements.

Example: `arr = [2, 1, 5, 1, 3, 2]`, `k = 3`. Find the maximum window sum.

### The 3-step approach

- **Step 1 — Build the first window**
  - Set `windowSum = 0`.
  - Add the first `k` elements.
  - Set `maxSum = windowSum`.

- **Step 2 — Slide the window**
  - Move `end` from index `k` to the end of the array.
  - Remove the outgoing element: `windowSum -= arr[end - k]`.
  - Add the incoming element: `windowSum += arr[end]`.

- **Step 3 — Update the answer**
  - After each slide, update `maxSum = Math.max(maxSum, windowSum)`.
  - Continue until all windows have been checked.

### Dry run

Input: `arr = [2, 1, 5, 1, 3, 2]`, `k = 3`

| Window | Outgoing | Incoming | Current window | Sum | Maximum sum |
|---|---:|---:|---|---:|---:|
| First window | — | — | `[2, 1, 5]` | 8 | 8 |
| Slide 1 | 2 | 1 | `[1, 5, 1]` | 7 | 8 |
| Slide 2 | 1 | 3 | `[5, 1, 3]` | 9 | 9 |
| Slide 3 | 5 | 2 | `[1, 3, 2]` | 6 | 9 |

**Final answer:** `9`, from the window `[5, 1, 3]`.

## 4. Complete Code — Maximum Sum of a Subarray of Size `k`

```js
const arr = [2, 1, 5, 1, 3, 2];
const k = 3;

if (k <= 0 || k > arr.length) {
  console.log(null);
} else {
  let windowSum = 0;

  // 1. Build the first window.
  for (let i = 0; i < k; i++) {
    windowSum += arr[i];
  }

  let maxSum = windowSum;

  // 2. Slide the window one position at a time.
  for (let end = k; end < arr.length; end++) {
    windowSum -= arr[end - k]; // Remove outgoing element.
    windowSum += arr[end];     // Add incoming element.

    // 3. Update the best answer.
    maxSum = Math.max(maxSum, windowSum);
  }

  console.log(maxSum); // 9
}
```

## 5. Generalize the Algorithm for Other Problems

Remember the structure, but adapt the **window state** and **answer update** to the question.

| Problem | What to maintain | What to update for each window |
|---|---|---|
| Maximum sum of size `k` | Running sum | Maximum sum |
| Minimum sum of size `k` | Running sum | Minimum sum |
| Average of size `k` | Running sum | `sum / k` |
| Maximum element in every window | Monotonic decreasing deque of indices | Value at deque front |
| First negative in every window | Queue of negative-element indices | First negative, or `0` |
| Anagram windows | Character-frequency counts | Count if frequencies match |

### Example: Average of every window

- First calculate the sum of the first `k` elements.
- For each next window, subtract the outgoing element and add the incoming element.
- Append `windowSum / k` to the result.

### Example: Maximum element in every window

- Rechecking all `k` elements in every window takes `O(n * k)`.
- Use a **monotonic decreasing deque** of indices.
- Remove indices outside the current window and smaller values from the back.
- The deque's front identifies the maximum.
- Time: `O(n)` amortized; auxiliary space: `O(k)`.
- Implement the deque with efficient front removal; JavaScript `Array.shift()` can take `O(k)` and may ruin the intended linear time.

## 6. Important Formulas and Complexity

- **Window size:** `end - start + 1 = k`.
- **Outgoing element index:** `end - k`.
- **Update running sum:** `windowSum = windowSum - arr[end - k] + arr[end]`.
- **Number of windows:** `n - k + 1`, when `1 <= k <= n`.
- **Brute force time:** `O(n * k)` for recalculating each window.
- **Running-sum sliding window time:** `O(n)`.
- **Auxiliary space:** `O(1)` for running-sum problems, excluding the output array. Deques, queues, or frequency maps may require `O(k)` space.

## 7. Main Concept to Remember

- **Brute force:** Recalculate the result for all `k` elements in every window.
- **Fixed-size sliding window:** Calculate the first window once; then remove one outgoing element and add one incoming element.
- The window length never changes.
- For a sum, each slide is updated in `O(1)` time, giving `O(n)` total time.
- Check edge cases: `k <= 0`, `k > arr.length`, and arrays shorter than the requested window.
