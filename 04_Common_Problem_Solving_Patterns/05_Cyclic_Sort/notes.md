# Cyclic Sort

## 1. Introduction / Concept

Imagine you have a deck of cards, numbered 1 to N, but they are shuffled. You want to sort them, but you are only allowed to pick up a card and place it in its correct position. When you move a card, another card takes its place, and you might need to move that card too. This creates a "cycle" of swaps until each card is in its rightful spot.

The **Cyclic Sort pattern** is an efficient in-place sorting algorithm specifically designed for problems involving arrays that contain numbers in a given fixed range (e.g., `1 to N` or `0 to N-1`). The core idea is simple: for each number in the array, you ensure it is placed at its **correct sorted index**. If it's not, you swap it with the number that *should* be at that index. This process forms cycles of swaps until every element is in its sorted place.

This pattern is very powerful because it achieves linear time complexity (O(N)) while using constant extra space (O(1)), making it highly optimized for its specific problem domain.

## 2. Core Principles / Theory

The fundamental principle behind Cyclic Sort relies on the specific property of the input: the numbers in the array belong to a defined, contiguous range (e.g., `[1, N]` or `[0, N-1]`). This allows us to determine the "correct" position for each number.

*   **The "Correct" Index:**
    *   If numbers are in the range `[1, N]`, the number `X` should ideally be at index `X - 1`.
    *   If numbers are in the range `[0, N-1]`, the number `X` should ideally be at index `X`.
    *   *(Imagine an array `[3, 1, 5, 4, 2]`. For `nums[0] = 3`, its correct index is `3-1 = 2`. For `nums[1] = 1`, its correct index is `1-1 = 0`.)*

*   **The Algorithm (Iterative Process):**
    1.  Initialize an index `i = 0` to iterate through the array.
    2.  At each `i`:
        a.  Determine the `correct_index` for the number `nums[i]`. (e.g., `correct_index = nums[i] - 1` for 1-to-N range).
        b.  **Check if `nums[i]` is already at its `correct_index`:** If `nums[i] == nums[correct_index]`, it means the number at `i` is already where it belongs (or a duplicate is already there, meaning `nums[i]` is a duplicate). In this case, simply increment `i` to move to the next element.
        c.  **If `nums[i]` is NOT at its `correct_index`:** This means `nums[i]` is out of place. Swap `nums[i]` with the number at `nums[correct_index]`. Crucially, **do NOT increment `i` yet**. The newly swapped element at `nums[i]` might also be out of place, so we need to check it in the *current* iteration of the inner `while` loop.
        d.  Repeat steps (b) and (c) for the current `i` until `nums[i]` is at its correct position.
    3.  Once `nums[i]` is in its correct place (or handled as a duplicate), increment `i` and continue the outer loop.

    *(Imagine a step-by-step process: `[3, 1, 5, 4, 2]` -> `i=0, nums[0]=3`. Correct index for 3 is 2. `nums[0]` is not `nums[2]`. Swap `nums[0]` and `nums[2]`. Array becomes `[5, 1, 3, 4, 2]`. `i` remains 0. Now `nums[0]=5`. Correct index for 5 is 4. Swap `nums[0]` and `nums[4]`. Array becomes `[2, 1, 3, 4, 5]`. `i` remains 0. Now `nums[0]=2`. Correct index for 2 is 1. Swap `nums[0]` and `nums[1]`. Array becomes `[1, 2, 3, 4, 5]`. `i` remains 0. Now `nums[0]=1`. Correct index for 1 is 0. `nums[0]` is `nums[0]`. Increment `i` to 1. Continue.)*

## 3. Operations / Methods

The core "operations" are:

*   **Iteration:** An outer `while` or `for` loop to scan through the array (controlled by `i`).
*   **Calculating `correct_index`:** `nums[i] - 1` (for 1-to-N range) or `nums[i]` (for 0-to-N-1 range).
*   **Conditional Swap:** `if nums[i] != nums[correct_index]: nums[i], nums[correct_index] = nums[correct_index], nums[i]` (Python's tuple assignment for clean swapping).

## 4. Python Implementation Details / Considerations

*   **In-Place Modification:** Python lists are mutable, allowing for efficient in-place swapping and modification.
*   **Tuple Assignment for Swapping:** `nums[i], nums[j] = nums[j], nums[i]` is the standard Pythonic way to swap two elements.
*   **`while` vs `for` for `i`:** While a `for` loop `for i in range(len(nums))` might seem intuitive, using a `while` loop `while i < len(nums)` for the main iteration (and an inner `while` loop for swaps at `i`) is often cleaner for Cyclic Sort. This is because `i` is only incremented when the element at `nums[i]` is correctly placed, ensuring that a newly swapped element (that was moved *to* `nums[i]`) is also checked.
*   **Handling Duplicates:** The condition `nums[i] != nums[j]` where `j` is the `correct_index` is crucial. If `nums[i]` is *already* equal to `nums[j]`, it means `nums[j]` is a duplicate of `nums[i]` that's already in its correct spot. In this case, we simply increment `i`, as `nums[i]` cannot be placed without disturbing `nums[j]`. This handles scenarios like `[3, 1, 2, 3]` where 3 appears twice.

### Example: Cyclic Sort an array with numbers from 1 to N (with duplicates handled)

Let's use an example with duplicates to show how the logic correctly handles them.

```python
def cyclic_sort(nums: list[int]) -> list[int]:
    i = 0
    # Iterate through the array
    while i < len(nums):
        # Calculate the correct index for the number currently at nums[i].
        # Assuming numbers are in the range [1, N], so value X should be at index X-1.
        correct_index = nums[i] - 1 

        # Check if the number at nums[i] is already at its correct position.
        # OR if it's a duplicate that has already been placed by a previous swap (and nums[correct_index] is already the correct value).
        if nums[i] != nums[correct_index]:
            # If nums[i] is not at its correct position, swap it with the element
            # that is supposed to be at nums[correct_index].
            nums[i], nums[correct_index] = nums[correct_index], nums[i]
            # Crucially, DO NOT increment `i`. After the swap, the new element at `nums[i]`
            # might also be out of place, so we need to check it again in the current iteration.
        else:
            # If nums[i] is already at its correct position, or it's a duplicate
            # that cannot be placed because its target position is already occupied by an identical value,
            # then we move to the next element.
            i += 1
    return nums

# Edge Cases and Examples:
print(f"Sort [3, 1, 5, 4, 2]: {cyclic_sort([3, 1, 5, 4, 2])}")             # Output: [1, 2, 3, 4, 5]
print(f"Sort [2, 6, 4, 3, 1, 5]: {cyclic_sort([2, 6, 4, 3, 1, 5])}")       # Output: [1, 2, 3, 4, 5, 6]
print(f"Sort [1]: {cyclic_sort([1])}")                                   # Output: [1] (Single element)
print(f"Sort [2, 1]: {cyclic_sort([2, 1])}")                             # Output: [1, 2] (Two elements, swapped)
print(f"Sort with duplicates [3, 1, 2, 3]: {cyclic_sort([3, 1, 2, 3])}") # Output: [1, 2, 3, 3] (One 3 is at index 2, the other remains)
print(f"Sort with duplicates [1, 5, 2, 5, 4, 3]: {cyclic_sort([1, 5, 2, 5, 4, 3])}") # Output: [1, 2, 3, 4, 5, 5]
```
*   **Edge Cases Handled:** Single element array, array needing single swap, and arrays with duplicates within the range.

## 5. Time and Space Complexity Analysis

The Cyclic Sort pattern achieves optimal complexity for its specific use case.

*   **Time Complexity:**
    *   **O(N)**: Where N is the number of elements in the array.
    *   **Justification:** Although there is a `while` loop nested inside another `while` loop (or `for` loop with inner `while`), each number is swapped at most a constant number of times. An element is only swapped if it's not in its correct position. Once an element `X` is correctly placed at `index X-1`, it will not be part of another swap involving `index X-1`. Therefore, each element participates in at most one "out-of-place" swap. The total number of swaps is at most `N-1`. This means the pointers `i` and `j` combined traverse the array effectively once.
*   **Space Complexity:**
    *   **O(1)**: (Constant space).
    *   **Justification:** The algorithm performs all its operations directly on the input array, modifying it in-place. It only uses a few additional variables for indices (`i`, `correct_index`) and temporary storage during swaps.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Exceptional Efficiency:** Achieves linear time (O(N)) and constant space (O(1)) for sorting when numbers are in a specific, contiguous range. This is extremely efficient.
*   **In-Place Sorting:** Does not require any auxiliary data structures proportional to the input size, making it very memory-efficient.
*   **Versatile for Related Problems:** The core mechanism of placing numbers at their correct positions can be easily adapted to find missing numbers, duplicate numbers, the smallest positive missing number, etc., often without needing to fully sort the array.

### Cons:
*   **Limited Applicability:** This is a highly specialized sorting algorithm. It **only works** when the input array contains numbers within a contiguous range (e.g., `1 to N`, `0 to N-1`). It cannot be used for general sorting of arbitrary numbers or strings.
*   **Requires Number Range:** The values themselves must be usable as indices, implying they are integers within a constrained positive range.
*   **Duplicate Handling Nuance:** While the algorithm can be adapted to handle duplicates (as shown), the logic for when to increment `i` needs to be carefully understood. If `nums[i]` is a duplicate and is already in its correct place (meaning `nums[i] == nums[correct_index]`), simply moving `i` is correct.

## 7. Common Use Cases / Applications

The Cyclic Sort pattern is primarily used for various permutation-related problems on arrays where elements are within a specific range:

*   **Finding the Missing Number:**
    *   Given an array containing `n` distinct numbers taken from `0, 1, 2, ..., n`, find the one that is missing.
    *   *(Strategy: Use Cyclic Sort to place numbers in their correct positions. Then iterate and find the first index `i` where `nums[i] != i`. That `i` is the missing number.)*
*   **Finding the Duplicate Number:**
    *   Given an array `nums` containing `n + 1` integers where each integer is in the range `[1, n]` inclusive, prove that at least one duplicate number must exist. Assume there is only one duplicate number, find the duplicate.
    *   *(Strategy: Use Cyclic Sort. When `nums[i]` is not at `nums[nums[i]-1]`, swap. If `nums[i]` IS equal to `nums[nums[i]-1]` and `i != nums[i]-1`, then `nums[i]` is the duplicate.)*
*   **Finding All Missing Numbers:**
    *   Given an array `nums` of `n` integers where `nums[i]` is in `[1, n]`, some elements appear twice and others once. Find all elements that appear in `[1, n]` that are not in `nums`.
    *   *(Strategy: Use Cyclic Sort to put numbers in their correct positions. Then iterate: if `nums[i] != i+1`, then `i+1` is a missing number.)*
*   **Finding All Duplicate Numbers:**
    *   Given an array `nums` of `n` integers where `nums[i]` is in `[1, n]`, some elements appear twice and others once. Find all elements that appear twice in `nums`.
    *   *(Strategy: Use Cyclic Sort. If a swap leads to `nums[i] == nums[correct_index]` where `i != correct_index`, then `nums[i]` is a duplicate that needs to be added to a result list.)*
*   **Smallest Missing Positive Number:**
    *   Find the smallest missing positive integer in an unsorted array. This often involves filtering out non-positive numbers first, then applying a modified cyclic sort.
*   **Correcting a Permutation (like a cycle in a graph).**

## 9. LeetCode Problem Examples

*   [268. Missing Number](https://leetcode.com/problems/missing-number/) (Easy) - Classic application.
*   [287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/) (Medium) - Can be solved using Cyclic Sort, or Fast & Slow Pointers (viewing array as linked list).
*   [448. Find All Numbers Disappeared in an Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/) (Easy)
*   [442. Find All Duplicates in an Array](https://leetcode.com/problems/find-all-duplicates-in-an-array/) (Medium)
*   [41. First Missing Positive](https://leetcode.com/problems/first-missing-positive/) (Hard) - A challenging problem that often uses a modified Cyclic Sort.

## 10. Further Reading / Resources

*   [GeeksforGeeks - Cyclic Sort](https://www.geeksforgeeks.org/cyclic-sort/) - Provides a good introduction with examples.
*   [Educative.io - Cyclic Sort Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/xVlKz9l2Ngj) - Detailed explanations with step-by-step visuals.
*   [YouTube - Back To Back SWE: Cyclic Sort](https://www.youtube.com/watch?v=Jd9HlUf45_A) - Video explanation that might help with intuition.
*   [LeetCode Discussion - Cyclic Sort explained](https://leetcode.com/discuss/general-discussion/1218556/cyclic-sort-a-pattern-for-arrays-containing-numbers-in-a-given-range) - Community insights.
