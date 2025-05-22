# Two Pointers

## 1. Introduction / Concept

Imagine you have a long list of numbers, and you need to find two numbers that add up to a specific target. A simple approach would be to pick one number, then check every other number in the list to see if they form the sum. This is like using two nested loops, which can be slow for very long lists.

The **Two Pointers pattern** is a clever technique that often helps solve such problems much more efficiently. Instead of two independent loops, you use two pointers (variables that store indices or references) that move through the data structure in a coordinated way. Think of them as two explorers on a path, strategically moving to find something. This coordinated movement allows you to reduce the time it takes to find a solution, often from a quadratic time (like O(N^2)) to a linear time (O(N)).

This pattern is particularly powerful and frequently used when working with **sorted arrays or linked lists**, or when you need to process elements from both ends of a sequence.

## 2. Core Principles / Theory

The power of the Two Pointers pattern comes from its ability to efficiently prune (eliminate) large parts of the search space with each step. There are two primary ways these pointers are typically moved:

### 2.1. Pointers Moving Towards Each Other (Converging Pointers)

*   **Setup:** You initialize one pointer, let's call it `left`, at the very beginning of the data structure (index 0 for an array). The other pointer, `right`, is initialized at the very end of the data structure (last index for an array).
*   **Movement:** In each iteration, you compare the elements pointed to by `left` and `right` against a condition. Based on this comparison, you decide whether to move `left` one step to the right, `right` one step to the left, or both. The goal is to gradually narrow the "window" between `left` and `right` until they meet or cross.
*   **Applicability:** This approach is most effective when the underlying data structure is **sorted**. The sorted property allows you to make informed decisions about moving pointers. If the sum of elements at `left` and `right` is too small, you know you need a larger number, so you increment `left`. If it's too large, you decrement `right`.

    *(Imagine a horizontal line representing a sorted array. A pointer `L` is on the far left, and a pointer `R` is on the far right. They move inwards, narrowing the segment they are examining.)*

### 2.2. Pointers Moving in the Same Direction (Sliding Window or Fast & Slow Pointers)

*   **Setup:** Both pointers, `left` and `right` (or `slow` and `fast`), start at or near the beginning of the data structure.
*   **Movement:**
    *   One pointer (often `right` or `fast`) advances first, effectively **expanding** a window or traversing ahead.
    *   The second pointer (often `left` or `slow`) advances later, either to **shrink** the window (as in Sliding Window) or to maintain a specific distance/relationship with the first pointer (as in Fast & Slow Pointers for linked lists).
*   **Applicability:**
    *   **Sliding Window:** This is a specific type of two-pointer pattern where the `left` and `right` pointers define a contiguous "window" of elements. The `right` pointer expands the window, and `left` pointer shrinks it to satisfy certain conditions (e.g., maximum sum subarray, longest substring without repeating characters).
    *   **Fast & Slow Pointers:** Here, the pointers move at different speeds (e.g., `fast` moves twice as fast as `slow`). This is primarily used in linked lists to detect cycles, find the middle of a list, or identify the start of a cycle.

    *(Imagine two runners on a track. For Sliding Window, one runner expands the loop, and the other occasionally catches up to keep the loop size constrained. For Fast & Slow, one runner is much faster, allowing them to eventually meet if the track is circular.)*

## 3. Operations / Methods

The "operations" of the Two Pointers pattern are the controlled movements of these pointers based on the problem's conditions.

*   `left += 1`: Advance the left pointer one step to the right.
*   `right -= 1`: Advance the right pointer one step to the left.
*   `slow = slow.next`: Advance the slow pointer one step (in linked lists).
*   `fast = fast.next.next`: Advance the fast pointer two steps (in linked lists).
*   **Comparison:** The core logic involves comparing the values at `arr[left]` and `arr[right]` (or `slow.val` and `fast.val`, etc.) to guide pointer movement.

## 4. Python Implementation Details / Considerations

Python's inherent features make implementing Two Pointers quite elegant:

*   **Lists for Arrays:** Python `list` objects behave like dynamic arrays, making it easy to access elements by index.
*   **`while` Loops:** The `while` loop is the natural construct for Two Pointers, as the iteration continues as long as the pointers haven't met or crossed a certain boundary.
*   **Tuple Assignment for Swapping:** Python's `a, b = b, a` syntax is very convenient for swapping elements in-place, which is often needed in array-based two-pointer problems (e.g., reversing an array).
*   **Linked List Nodes:** For linked list problems, you'll work with `ListNode` objects (or similar custom classes) and their `next` attributes.

### Example 1: Two Pointers (Converging) - Check if a string is a palindrome

A palindrome reads the same forwards and backward (e.g., "madam", "racecar").

```python
def is_palindrome(s: str) -> bool:
    # Convert string to lowercase and filter out non-alphanumeric characters
    # This prepares the string for a pure palindrome check.
    cleaned_s = "".join(char.lower() for char in s if char.isalnum())
    
    left = 0
    right = len(cleaned_s) - 1

    while left < right: # Pointers move towards each other
        if cleaned_s[left] != cleaned_s[right]:
            return False # Mismatch found
        left += 1
        right -= 1
    
    return True # All characters matched

# Edge Cases and Examples:
print(f"\"madam\" is palindrome: {is_palindrome('madam')}")        # Output: True
print(f"\"racecar\" is palindrome: {is_palindrome('racecar')}")    # Output: True
print(f"\"hello\" is palindrome: {is_palindrome('hello')}")        # Output: False
print(f"\"A man, a plan, a canal: Panama\" is palindrome: {is_palindrome('A man, a plan, a canal: Panama')}") # Output: True
print(f"\"\" (empty string) is palindrome: {is_palindrome('')}")   # Output: True (Edge case: empty string is a palindrome)
print(f"\"a\" (single char) is palindrome: {is_palindrome('a')}")  # Output: True (Edge case: single character string is a palindrome)
```
*   **Edge Cases Handled:** Empty string and single-character strings. The `while left < right` ensures correct termination.

### Example 2: Two Pointers (Converging) - Find a pair with a target sum in a sorted array

This is a classic problem demonstrating the power of converging pointers on sorted data.

```python
def find_pair_sum(arr: list[int], target_sum: int) -> list[int]:
    left = 0
    right = len(arr) - 1

    while left < right:
        current_sum = arr[left] + arr[right]
        if current_sum == target_sum:
            # Found the pair. Return indices or values as per problem.
            # Returning values here.
            return [arr[left], arr[right]] 
        elif current_sum < target_sum:
            # Sum is too small, need a larger number. Move left pointer to increase sum.
            left += 1
        else: # current_sum > target_sum
            # Sum is too large, need a smaller number. Move right pointer to decrease sum.
            right -= 1
    
    return [] # No such pair found

# Edge Cases and Examples:
print(f"Pair in [1,2,3,4,6] for sum 6: {find_pair_sum([1, 2, 3, 4, 6], 6)}") # Output: [2, 4]
print(f"Pair in [1,2,3,4,6] for sum 10: {find_pair_sum([1, 2, 3, 4, 6], 10)}") # Output: []
print(f"Pair in [1,1,1,1] for sum 2: {find_pair_sum([1, 1, 1, 1], 2)}") # Output: [1, 1] (Handles duplicates correctly due to logic)
print(f"Pair in [] for sum 5: {find_pair_sum([], 5)}") # Output: [] (Edge case: empty array)
print(f"Pair in [7] for sum 7: {find_pair_sum([7], 7)}") # Output: [] (Edge case: single element array)
```
*   **Edge Cases Handled:** Empty array, single-element array. The `while left < right` prevents `left` and `right` from being the same index.

## 5. Time and Space Complexity Analysis

The Two Pointers pattern is highly efficient, especially compared to brute-force nested loops.

*   **Time Complexity:**
    *   **O(N)**: Where N is the number of elements in the input array or linked list.
    *   **Justification:** In both converging and same-direction two-pointer patterns, each pointer traverses the data structure at most once. This means the total number of operations is directly proportional to the size of the input. If the input needed to be sorted first, the sorting time (e.g., O(N log N)) would dominate the overall complexity.
*   **Space Complexity:**
    *   **O(1)**: (Constant space).
    *   **Justification:** The pattern typically only requires a few extra variables to store the pointers themselves, and possibly a few other variables for calculations or results. This space requirement does not grow with the input size.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Exceptional Efficiency:** Reduces the time complexity for many problems from O(N^2) (brute-force) to O(N) (linear), making solutions feasible for large inputs.
*   **Optimal Space Usage:** Achieves O(1) auxiliary space, which is the most memory-efficient possible. This is particularly valuable in environments with strict memory constraints.
*   **Simplicity and Readability:** Once understood, the pattern is relatively straightforward to implement and the resulting code is often clean and easy to follow.
*   **Avoids Redundant Work:** By strategically moving pointers, it avoids re-evaluating sub-problems that have already been implicitly considered.

### Cons:
*   **Limited Applicability:** Not a universal pattern. It is primarily (though not exclusively) useful for problems involving sorted arrays/lists, or sequences where there's a clear monotonic relationship that allows for efficient pruning of the search space.
*   **Requires Sorted Data (often):** For converging pointers, the input data *must* be sorted for the logic to work correctly. If the data is unsorted, you'd need an initial sorting step, which adds O(N log N) to the time complexity.
*   **Careful Logic:** Determining *when* to move which pointer requires careful thought and understanding of the problem's conditions and the desired outcome. Off-by-one errors or incorrect pointer movements are common pitfalls for beginners.

## 7. Common Use Cases / Applications

The Two Pointers pattern is highly versatile and appears in many variations:

*   **Finding Pairs/Triplets/Quadruplets with a Sum:**
    *   Example: Given a sorted array, find two numbers that sum to a target (as shown in Example 2).
    *   Extensions: 3Sum, 4Sum (often involve fixing one or two numbers, then using two pointers on the remainder of the sorted array).
*   **Removing Duplicates from Sorted Arrays/Lists:**
    *   Example: Move all unique elements to the beginning of the array.
    *   *(Imagine a diagram with one pointer `i` iterating through the array and another pointer `j` only advancing when a unique element is found, copying it to `arr[j]`.)*
*   **Checking for Palindromes:**
    *   Example: Check if a string is a palindrome by comparing characters from both ends (as shown in Example 1).
*   **Reversing Arrays/Strings (in-place):**
    *   Example: Swap elements at `left` and `right` until `left >= right`.
    *   *(Imagine pointers `L` and `R` swapping elements and moving inwards.)*
*   **Squaring a Sorted Array:**
    *   Example: Given a sorted array of integers, return the squares of each number sorted in non-decreasing order. This uses two pointers from the ends, placing the larger square in the correct position from the end of the result array.
*   **Comparing Two Sequences:**
    *   Example: Check if one string is a subsequence of another.
*   **Finding Intersections or Differences of Sorted Lists:**
    *   If two sorted lists, `arr1` and `arr2`, are given, use two pointers (`i` for `arr1`, `j` for `arr2`) to find common elements or elements unique to each.

## 8. Related Concepts / Variations

*   **Sliding Window:** As discussed, this is a specialized application where both pointers move in the same direction, defining a contiguous subsegment.
*   **Fast & Slow Pointers (Tortoise and Hare):** A specific sub-pattern primarily for linked lists and cycle detection, where pointers move at different speeds.
*   **Binary Search:** While using only one pointer, Binary Search also relies on repeatedly eliminating half of a sorted search space, sharing the efficiency principle with two pointers.
*   **Greedy Algorithms:** Many two-pointer solutions can be seen as greedy, as they make the locally optimal choice (moving one pointer) at each step, leading to a global optimum.

## 9. LeetCode Problem Examples

*   [167. Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) (Medium) - Classic converging pointers.
*   [26. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) (Easy) - Same direction pointers, in-place modification.
*   [125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/) (Easy) - Converging pointers on a string.
*   [977. Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/) (Easy) - Converging pointers to fill a new array from the end.
*   [15. 3Sum](https://leetcode.com/problems/3sum/) (Medium) - Fix one element, then use two pointers on the rest.
*   [18. 4Sum](https://leetcode.com/problems/4sum/) (Medium) - Fix two elements, then use two pointers on the rest.
*   [344. Reverse String](https://leetcode.com/problems/reverse-string/) (Easy) - Converging pointers for in-place reversal.

## 10. Further Reading / Resources

*   [GeeksforGeeks - Two Pointer Technique](https://www.geeksforgeeks.org/two-pointers-technique/) - Provides a good overview with various examples.
*   [Educative.io - Two Pointers Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/YQgy9Z2kpwq) - Part of a popular course, offers a structured approach.
*   [NeetCode.io - Two Pointers](https://neetcode.io/roadmap) - Look under the "Arrays & Hashing" or "Two Pointers" section for video explanations and problem lists.
*   [Stack Overflow - Explain Two Pointers Algorithm](https://stackoverflow.com/questions/11100913/explain-two-pointers-algorithm) - Community explanations for different perspectives.
