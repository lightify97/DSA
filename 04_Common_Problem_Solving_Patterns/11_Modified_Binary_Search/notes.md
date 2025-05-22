# Modified Binary Search

## 1. Introduction / Concept
Modified Binary Search refers to adapting the classic binary search algorithm to solve problems that don't involve a simple lookup in a sorted array. The core idea remains the same: reducing the search space by half in each step by checking a middle element and deciding which half to continue searching in. This pattern is often used when the "answer" to a problem lies within a range and can be found by evaluating a "monotonic" function.

## 2. Core Principles / Theory
The power of binary search lies in its ability to quickly narrow down a search space. For a modified binary search, the key is to identify:
1.  **A sorted (or sortable) collection:** This can be an actual array, or a conceptual search space (e.g., a range of possible answers).
2.  **A monotonic property:** A property that is true for one part of the search space and false for the other. This allows you to eliminate half of the search space with each comparison.
3.  **A `check(mid)` function:** This function takes a `mid` value from the search space and determines whether the desired property holds for `mid` (or its relationship to `mid`). Based on this check, you decide to search in the left or right half.

**Typical Loop Structure:**
*   Initialize `low` and `high` to define the search space.
*   `while low <= high:` (or `low < high` depending on template and whether `mid` can be a final answer).
*   Calculate `mid`.
*   Apply the `check(mid)` function:
    *   If `check(mid)` is true (or satisfies criteria), it means `mid` could be the answer, or we need to search in the left/right half for an even better answer. Adjust `high` or `ans = mid; high = mid - 1`.
    *   If `check(mid)` is false, it means `mid` is too small/large, so adjust `low` accordingly (`low = mid + 1`).

## 3. Operations / Methods
*   **Integer Division:** `mid = (low + high) // 2` (or `(low + high) >> 1` for bitwise efficiency).
*   **Comparison and Branching:** `if` / `elif` / `else` statements to guide the search.
*   **Conceptual `check(value)` function:** This function encapsulates the problem's specific logic to determine if `value` satisfies the condition.

## 4. Python Implementation Details / Considerations
*   Python's integer division `//` works as expected.
*   The `bisect` module can be used for simpler insertions into sorted lists, which is a form of binary search.
*   For problems searching on the *answer space*, you might need to determine a reasonable range for `low` and `high`.

**Example: Search in Rotated Sorted Array**

```python
def search_rotated_sorted_array(nums, target):
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = (left + right) // 2

        if nums[mid] == target:
            return mid

        # Determine which part is sorted (left or right half)
        if nums[left] <= nums[mid]: # Left half is sorted
            if nums[left] <= target < nums[mid]:
                right = mid - 1 # Target is in the sorted left half
            else:
                left = mid + 1 # Target is in the unsorted right half
        else: # Right half is sorted
            if nums[mid] < target <= nums[right]:
                left = mid + 1 # Target is in the sorted right half
            else:
                right = mid - 1 # Target is in the unsorted left half
    
    return -1

# Example usage:
# print(search_rotated_sorted_array([4, 5, 6, 7, 0, 1, 2], 0)) # Output: 4
# print(search_rotated_sorted_array([4, 5, 6, 7, 0, 1, 2], 3)) # Output: -1
```

## 5. Time and Space Complexity Analysis
*   **Time Complexity:** **O(log N)**, where N is the size of the search space. Each comparison reduces the problem size by half. This assumes the `check(mid)` function takes O(1) time. If `check(mid)` takes O(K) time, then total time is O(K log N).
*   **Space Complexity:** **O(1)** (constant space), as only a few variables are used.

## 6. Pros and Cons / Trade-offs
**Pros:**
*   **Extremely Efficient:** Achieves logarithmic time complexity, making it suitable for very large inputs.
*   **Versatile:** Applicable to a wide range of problems beyond simple lookups, including finding specific elements, boundaries, or even optimal answers.

**Cons:**
*   **Requires Monotonicity:** The underlying search space must have a monotonic property that can be leveraged.
*   **Tricky Implementation:** Off-by-one errors in `low`, `high`, and `mid` calculations, or in boundary conditions, are common. The `while` loop condition (`low < high` vs `low <= high`) and `low`/`high` updates (`mid` vs `mid +/- 1`) need to be very precise for correctness.

## 7. Common Use Cases / Applications
*   **Searching in Rotated Sorted Array:** (e.g., LeetCode 33, 81).
*   **Finding First/Last Occurrence of an Element.**
*   **Finding Peak Element:** (e.g., LeetCode 162).
*   **Finding Minimum in Rotated Sorted Array:** (e.g., LeetCode 153, 154).
*   **Finding Square Root (integer part):** (e.g., LeetCode 69. Sqrt(x)).
*   **Search a 2D Matrix (sorted rows and columns):** (e.g., LeetCode 74, 240).
*   **Problems where you binary search on the "answer" space:** (e.g., Koko Eating Bananas, Ship Within Days, Smallest Divisor). This requires designing a `check(mid)` function to verify if `mid` is a valid answer.

## 8. Related Concepts / Variations
*   **Binary Search:** The foundational algorithm.
*   **Ternary Search:** For finding the minimum/maximum of a unimodal function (less common in interviews).

## 9. LeetCode Problem Examples
*   [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/) (Medium)
*   [162. Find Peak Element](https://leetcode.com/problems/find-peak-element/) (Medium)
*   [69. Sqrt(x)](https://leetcode.com/problems/sqrtx/) (Easy)
*   [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/) (Medium)
*   [153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) (Medium)
*   [875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/) (Medium)
*   [1011. Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/) (Medium)

## 10. Further Reading / Resources
*   [GeeksforGeeks - Binary Search](https://www.geeksforgeeks.org/binary-search/)
*   [NeetCode.io - Binary Search](https://neetcode.io/roadmap) (Look for binary search section)
*   [LeetCode Explore - Binary Search](https://leetcode.com/explore/learn/card/binary-search/)
