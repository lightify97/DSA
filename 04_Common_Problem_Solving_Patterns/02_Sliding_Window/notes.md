# Sliding Window

## 1. Introduction / Concept

Imagine you're inspecting a very long train, but you only care about a certain number of consecutive carriages at a time. Instead of repeatedly stopping the train, picking up those carriages, inspecting them, then putting them back and moving to the next set, you just slide your inspection team along a continuous window.

The **Sliding Window pattern** is a powerful technique used to solve problems that involve finding a subsegment (e.g., subarray, substring) of a given size or satisfying certain conditions within a larger sequence (array, string, or list). It works by maintaining a "window" of elements and efficiently "sliding" it across the data, rather than re-evaluating every possible subsegment from scratch.

This pattern is fundamental for problems that ask for the "longest," "shortest," or "number of" subsegments that meet specific criteria.

## 2. Core Principles / Theory

The core idea is to efficiently explore all possible contiguous subsegments. Instead of using nested loops (which would be O(N^2) or O(N^3) for checking every subsegment), the sliding window uses two pointers (`window_start` and `window_end`) that define the current window, moving each pointer through the sequence only once.

*   **Window Definition:** A "window" is a contiguous segment of the input sequence, defined by a `window_start` (left pointer) and a `window_end` (right pointer).
*   **Expansion:** The `window_end` pointer advances one step at a time, incorporating a new element into the window. As the window expands, you update any relevant metrics or data structures (like character counts or sums).
*   **Contraction (Sliding):** If the window (or the elements within it) violates a given constraint or condition (e.g., window size exceeds `K`, number of distinct characters exceeds `D`, sum exceeds `S`), the `window_start` pointer is advanced. This effectively "shrinks" the window from the left, removing elements until the condition is met again. When shrinking, you also update your metrics by removing the contribution of the element leaving the window.
*   **Optimization:** The efficiency comes from the fact that you don't re-calculate the entire window's properties each time. You only incrementally update them as elements enter or leave the window.

    *(Imagine a visual diagram of an array with a `window_start` and `window_end` pointer. `window_end` keeps moving right, expanding the window. When a condition is met, `window_start` also moves right, shrinking the window. The window "slides" along.)*

### Types of Sliding Window Problems:

1.  **Fixed-size Window:** The window size `K` is constant. You expand the window until it reaches size `K`, then in each subsequent step, you add a new element from the right and remove one from the left.
    *   *Example:* Maximum sum subarray of size K.
2.  **Dynamic-size Window (Shrinking/Expanding based on a condition):** The window size is variable. You expand the window until a condition is met (e.g., "sum >= target"). Once met, you try to shrink the window from the left while maintaining the condition, looking for the optimal smallest/longest window.
    *   *Example:* Longest substring without repeating characters.

## 3. Operations / Methods

The core "operations" are about managing the window's boundaries and its internal state:

*   `window_start = 0`: Initialize the left boundary.
*   `window_end`: Loop variable, typically `for window_end in range(len(arr))`.
*   **Expand Window:**
    *   `current_element = arr[window_end]`
    *   Add `current_element`'s contribution to `current_sum`, `char_frequency` map, etc.
*   **Check Condition & Shrink Window (if needed):**
    *   `while <condition_violated_or_needs_shrinking_from_left_side>:`
        *   `element_leaving_window = arr[window_start]`
        *   Remove `element_leaving_window`'s contribution from `current_sum`, `char_frequency` map, etc.
        *   `window_start += 1`
*   **Update Result:** After each expansion (and optional contraction), update the overall `max_length`, `min_sum`, `count`, etc.

## 4. Python Implementation Details / Considerations

Python's data structures are very well-suited for the Sliding Window pattern:

*   **Lists and Strings:** Directly represent the sequences to be processed.
*   **`for` Loop for `window_end`:** Often, the `window_end` pointer can be managed by a simple `for` loop iterating through the input.
*   **`while` Loop for `window_start`:** The `window_start` pointer is usually controlled by an inner `while` loop that handles the window shrinking.
*   **Dictionaries (`dict` or `collections.Counter`):** Invaluable for keeping track of frequencies or counts of elements within the current window. This allows for O(1) average time lookups and updates. `collections.defaultdict` can simplify initialization.
*   **`max()` / `min()`:** For easily updating the optimal result found so far.

### Example: Dynamic-size Sliding Window - Longest Substring Without Repeating Characters

This problem is a classic example of a dynamic sliding window where the window shrinks if a repeating character is encountered.

```python
def length_of_longest_substring(s: str) -> int:
    char_index_map = {}  # Stores character and its last seen index
    max_length = 0
    window_start = 0

    for window_end in range(len(s)):
        right_char = s[window_end]

        # If the character is already in the map and its index is within the current window,
        # move the start of the window to the right of the last occurrence of this character.
        if right_char in char_index_map and char_index_map[right_char] >= window_start:
            window_start = char_index_map[right_char] + 1
        
        # Update the character's last seen index
        char_index_map[right_char] = window_end
        
        # Calculate the current window length and update max_length
        # Current window length is (window_end - window_start + 1)
        max_length = max(max_length, window_end - window_start + 1)
    
    return max_length

# Edge Cases and Examples:
print(f"Longest substring for 'abcabcbb': {length_of_longest_substring('abcabcbb')}") # Output: 3 ("abc")
print(f"Longest substring for 'bbbbb': {length_of_longest_substring('bbbbb')}")       # Output: 1 ("b")
print(f"Longest substring for 'pwwkew': {length_of_longest_substring('pwwkew')}")     # Output: 3 ("wke" or "kew")
print(f"Longest substring for '': {length_of_longest_substring('')}")                 # Output: 0 (Edge case: empty string)
print(f"Longest substring for 'a': {length_of_longest_substring('a')}")               # Output: 1 (Edge case: single character string)
print(f"Longest substring for 'au': {length_of_longest_substring('au')}")             # Output: 2 ("au")
```
*   **Edge Cases Handled:** Empty string, single-character string. The `char_index_map[right_char] >= window_start` check is crucial to ensure `window_start` only moves if the duplicate is *within* the current active window.

## 5. Time and Space Complexity Analysis

The Sliding Window pattern is highly optimized and generally achieves linear time complexity.

*   **Time Complexity:**
    *   **O(N)**: Where N is the length of the input sequence (array or string).
    *   **Justification:** Both `window_start` and `window_end` pointers traverse the sequence at most once. Each element is added to the window and potentially removed from the window only a constant number of times. Operations on the internal data structure (like dictionary lookups/updates) are typically O(1) on average.
*   **Space Complexity:**
    *   **O(K)** or **O(C)**:
        *   **O(K):** For fixed-size windows, if you're storing the window elements (e.g., a `deque` for Sliding Window Maximum).
        *   **O(C):** Where C is the size of the character set (e.g., 26 for English alphabet, 128 for ASCII, 256 for extended ASCII) or the number of distinct elements in the input. This applies when using a hash map to store frequencies/counts. In the worst case, C can be O(N) if all elements are distinct.
    *   **Justification:** The space used for the window's internal state (e.g., `char_index_map` or `char_frequency` in Python) depends on the number of unique elements or the fixed window size, not the total input size beyond that.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Significant Performance Improvement:** Transforms brute-force O(N^2) or O(N^3) solutions into efficient O(N) solutions, making them viable for large datasets.
*   **Optimized for Contiguous Subsegments:** Specifically designed for problems requiring analysis of contiguous parts of a sequence.
*   **Flexibility:** Can be adapted for both fixed and dynamically sized windows, and for various criteria (sum, counts, distinct characters, etc.).
*   **Relatively Simple Implementation:** Once the core concept is understood, the two-pointer logic is quite elegant.

### Cons:
*   **Limited Applicability:** Only works for problems that inherently involve *contiguous* subsegments. Cannot be used for problems requiring non-contiguous subsets or arbitrary selections.
*   **Careful Condition Handling:** The conditions for expanding and especially for *contracting* the window must be precisely defined and implemented. Incorrect logic for shrinking the window is a common source of errors.
*   **State Management:** For complex problems, managing the internal state (e.g., `char_frequency` map) as elements enter and leave the window can be intricate.

## 7. Common Use Cases / Applications

The Sliding Window pattern is one of the most frequently encountered patterns in coding interviews due to its versatility and efficiency:

*   **Maximum/Minimum Sum/Average Subarray of Size K:**
    *   Find the sub-array of a given fixed length `K` that has the largest sum.
*   **Longest Substring Problems:**
    *   Longest Substring Without Repeating Characters (Example shown).
    *   Longest Substring with K Distinct Characters.
    *   Longest Substring with Same Letters After Replacement.
*   **Smallest Subarray Problems:**
    *   Smallest Subarray with a Given Sum.
    *   Minimum Window Substring (find smallest window in `S` that contains all characters of `T`).
*   **Anagram / Permutation Problems:**
    *   Permutation in String (Check if `P` is a permutation of any substring of `S`).
    *   Find All Anagrams in a String (Find all occurrences of anagrams of `P` in `S`).
*   **Problems with specific counts/frequencies within a window.**

## 8. Related Concepts / Variations

*   **Two Pointers:** The Sliding Window is a direct application of the Two Pointers pattern, where both pointers move in the same direction.
*   **Hash Maps (`dict`, `Counter`, `defaultdict`):** Almost always used in conjunction with Sliding Window to efficiently track the state of elements within the window (e.g., frequencies).
*   **Queues (especially `collections.deque`):** Used in problems like "Sliding Window Maximum" to maintain a monotonic queue of elements within the window.
*   **Prefix Sums:** An alternative technique for some array sum problems, but typically for non-contiguous queries or where the window concept doesn't naturally fit.

## 9. LeetCode Problem Examples

*   [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) (Medium) - Classic dynamic window.
*   [209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/) (Medium) - Dynamic window for minimum length.
*   [424. Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/) (Medium) - Dynamic window with a constraint on character changes.
*   [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) (Hard) - Complex dynamic window, often considered a benchmark.
*   [567. Permutation in String](https://leetcode.com/problems/permutation-in-string/) (Medium) - Fixed-size window with character frequency checks.
*   [438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/) (Medium) - Variation of permutation in string.
*   [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) (Hard) - Combines sliding window with a monotonic deque.

## 10. Further Reading / Resources

*   [GeeksforGeeks - Sliding Window Technique](https://www.geeksforgeeks.org/window-sliding-technique/) - Provides various examples and explanations.
*   [Educative.io - Sliding Window Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/gx2yZmZEWGx) - Excellent structured course on this pattern.
*   [NeetCode.io - Sliding Window](https://neetcode.io/roadmap) - Look under the "Two Pointers" or "Sliding Window" section for video explanations and problem lists.
*   [Baeldung - Introduction to Sliding Window Algorithm](https://www.baeldung.com/cs/sliding-window-algorithm) - Another good resource for understanding the basics.
