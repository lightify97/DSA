# Monotonic Stack / Queue

## 1. Introduction / Concept

Imagine you are walking through a city and you want to find the first building taller than the current one to your right. Or, in a stock market context, you want to find the next day a stock price will be higher than today's. If you iterate through all subsequent buildings/days for each current point, it's slow (O(N^2)).

The **Monotonic Stack (or Queue)** pattern is a powerful and specialized data structure technique used to efficiently solve problems that require finding the "next greater element," "next smaller element," or related queries within a sequence. It involves maintaining a stack (or queue/deque) where the elements are kept in a strictly increasing or strictly decreasing order. This property allows for a linear time (O(N)) solution to problems that might otherwise seem to require quadratic time.

It's called "monotonic" because the elements within the stack/queue always maintain a specific monotonic property (either non-decreasing or non-increasing).

## 2. Core Principles / Theory

The core idea is that when you iterate through the input sequence and consider adding an element `X` to your monotonic stack/queue, you first **pop any elements from the top (or front/back for deque) that violate the desired monotonic property** before pushing `X`.

### 2.1. Monotonic Increasing Stack

*   **Property:** Elements in the stack are in strictly increasing order from bottom to top.
*   **Behavior when pushing `X`:** If `X` is being pushed, and the stack is not empty, pop all elements `Y` from the top of the stack such that `Y >= X`. After popping, push `X`.
*   **Insights Gained:**
    *   For every `Y` that is **popped**, `X` is its **"next smaller element"**. This is because `X` caused `Y` to be popped, meaning `X` is the first element encountered after `Y` that is smaller than or equal to `Y`.
    *   For `X` that is **pushed**, the element now at the **top of the stack (before `X` was pushed)** is its **"previous smaller element"**.
*   **Use Cases:** Finding "next smaller", "previous smaller", "largest rectangle in histogram" (where smaller elements define boundaries).

    *(Imagine building a skyline. As you add a new building, if it's shorter than previous buildings, it means those taller buildings can only form a rectangle up to this shorter one. You pop them and process their rectangles.)*

### 2.2. Monotonic Decreasing Stack

*   **Property:** Elements in the stack are in strictly decreasing order from bottom to top.
*   **Behavior when pushing `X`:** If `X` is being pushed, and the stack is not empty, pop all elements `Y` from the top of the stack such that `Y <= X`. After popping, push `X`.
*   **Insights Gained:**
    *   For every `Y` that is **popped**, `X` is its **"next greater element"**.
    *   For `X` that is **pushed**, the element now at the **top of the stack (before `X` was pushed)** is its **"previous greater element"**.
*   **Use Cases:** Finding "next greater", "previous greater", "trapping rain water" (where taller elements define boundaries).

    *(Imagine an array of daily stock prices. To find the next day with a higher price, you keep a decreasing stack of prices. When a higher price comes, all smaller prices on the stack find their "next greater element".)*

### 2.3. Monotonic Queue / Deque (for Sliding Window Maximum/Minimum)

*   **Property:** Elements in the deque are kept in either strictly increasing or strictly decreasing order.
*   **Behavior:** When a new element comes into the window, you remove elements from the back of the deque that violate the monotonic property. When an element leaves the window (from the front), you check if it's the element at the front of the deque and remove it if it is.
*   **Use Cases:** Efficiently finding the maximum or minimum element in a sliding window in O(N) time.

## 3. Operations / Methods

*   **Stack Operations (Python `list`):**
    *   `stack = []` (empty list for stack)
    *   `stack.append(item)`: Push to top (O(1)).
    *   `item = stack.pop()`: Pop from top (O(1)).
    *   `stack[-1]`: Peek at top (O(1)).
    *   `len(stack)`: Check size (O(1)).
*   **Deque Operations (`collections.deque`):**
    *   `d = deque()` (empty deque)
    *   `d.append(item)`: Add to right (O(1)).
    *   `d.appendleft(item)`: Add to left (O(1)).
    *   `item = d.pop()`: Remove from right (O(1)).
    *   `item = d.popleft()`: Remove from left (O(1)).
    *   `d[0]`, `d[-1]`: Peek at ends (O(1)).
*   **Conditional Removal Loop:** The core logic, typically a `while stack and <condition_based_on_stack_top_and_new_element>:` loop before `append`.

## 4. Python Implementation Details / Considerations

*   **`list` for Stack:** Python's `list` is perfectly suitable for a stack (using `append` and `pop`).
*   **`collections.deque` for Queue/Deque:** For a monotonic queue or when you need O(1) operations from both ends, `collections.deque` is the correct choice.
*   **Storing Indices vs. Values:** Often, you store *indices* in the monotonic stack/queue rather than values themselves. This allows you to easily retrieve the value (`nums[index]`) and calculate distances/spans.
*   **Handling Remaining Elements:** After iterating through the input array, any elements left in the monotonic stack/queue have not found their "next greater/smaller element" (or they are the largest/smallest overall). Their corresponding result might be -1 or a special value, or they might be processed further depending on the problem.
*   **Edge Cases:** Empty input, single element, strictly increasing/decreasing arrays (worst-case for stack size).

### Example 1: Next Greater Element (Simplified - finding NGE for all elements)

This example finds the "next greater element" for each element in an array using a monotonic decreasing stack.

```python
def next_greater_element_array(nums: list[int]) -> list[int]:
    n = len(nums)
    result = [-1] * n # Initialize result array with -1 (meaning no NGE found yet)
    stack = []        # This will be a monotonic decreasing stack (stores indices)

    # Iterate through the array with indices
    for i in range(n):
        current_num = nums[i]
        
        # While the stack is not empty AND the current number is greater than
        # the number at the index on top of the stack:
        # This means `current_num` is the Next Greater Element for `nums[stack[-1]]`.
        while stack and current_num > nums[stack[-1]]:
            popped_index = stack.pop() # Get the index of the element from stack top
            result[popped_index] = current_num # Set its Next Greater Element
        
        # Push the current element's index onto the stack.
        # The stack maintains a decreasing order of values corresponding to the indices.
        stack.append(i)
    
    # After the loop, any indices left in the stack do not have a next greater element
    # to their right. Their corresponding `result` entries remain -1, which is correct.
    return result

# Edge Cases and Examples:
print(f"NGE for [4, 5, 2, 10, 8]: {next_greater_element_array([4, 5, 2, 10, 8])}")
# Output: [5, 10, 10, -1, -1]
# Explanation:
# For 4, next greater is 5.
# For 5, next greater is 10.
# For 2, next greater is 10.
# For 10, no next greater.
# For 8, no next greater.

print(f"NGE for [1, 2, 3, 4, 5]: {next_greater_element_array([1, 2, 3, 4, 5])}")
# Output: [2, 3, 4, 5, -1] (Strictly increasing array)

print(f"NGE for [5, 4, 3, 2, 1]: {next_greater_element_array([5, 4, 3, 2, 1])}")
# Output: [-1, -1, -1, -1, -1] (Strictly decreasing array)

print(f"NGE for [1]: {next_greater_element_array([1])}")
# Output: [-1] (Single element)

print(f"NGE for []: {next_greater_element_array([])}")
# Output: [] (Empty array)
```
*   **Edge Cases Handled:** Empty array, single element, strictly increasing/decreasing arrays. The `-1` initialization and `while` loop logic handle these correctly.

### Example 2: Sliding Window Maximum (Using a Monotonic Deque - LeetCode 239)

This problem requires finding the maximum in every sliding window of size `k`. A monotonic deque is highly efficient for this.

```python
from collections import deque

def sliding_window_maximum(nums: list[int], k: int) -> list[int]:
    result = []
    # Deque stores indices. It maintains a monotonic decreasing order of values.
    # The front of the deque (leftmost element) will always be the index
    # of the maximum element in the current window.
    dq = deque() 

    # Iterate through the array using 'right' pointer for window_end
    for right in range(len(nums)):
        # Step 1: Remove elements from the back of the deque that are smaller
        # than or equal to the current element. They can't be the maximum.
        while dq and nums[dq[-1]] <= nums[right]:
            dq.pop()
        
        # Step 2: Add the current element's index to the back of the deque.
        dq.append(right)
        
        # Step 3: Remove indices from the front of the deque that are outside
        # the current window (i.e., too old).
        if dq[0] <= right - k:
            dq.popleft()
        
        # Step 4: Once the window is formed (right >= k-1),
        # the front of the deque is the maximum of the current window.
        if right >= k - 1:
            result.append(nums[dq[0]])
            
    return result

# Edge Cases and Examples:
print(f"SWM for [1,3,-1,-3,5,3,6,7], k=3: {sliding_window_maximum([1,3,-1,-3,5,3,6,7], 3)}")
# Output: [3, 3, 5, 5, 6, 7]
# Window:     Max
# [1,3,-1]    3
#  [3,-1,-3]  3
#   [-1,-3,5] 5
#    [-3,5,3] 5
#     [5,3,6] 6
#      [3,6,7] 7

print(f"SWM for [1], k=1: {sliding_window_maximum([1], 1)}")
# Output: [1]

print(f"SWM for [7,2,4], k=2: {sliding_window_maximum([7,2,4], 2)}")
# Output: [7, 4]

print(f"SWM for [], k=0: {sliding_window_maximum([], 0)}")
# Output: [] (Handles empty input)

print(f"SWM for [1,2,3], k=1: {sliding_window_maximum([1,2,3], 1)}")
# Output: [1,2,3]
```
*   **Edge Cases Handled:** Empty array, `k=1`, small arrays. The deque's ability to remove from both ends is key.

## 5. Time and Space Complexity Analysis

The Monotonic Stack/Queue pattern provides optimal linear time complexity.

*   **Time Complexity:**
    *   **O(N)**: Where N is the number of elements in the input array.
    *   **Justification:** Despite the nested `while` loop inside a `for` loop, each element of the input array is processed a constant number of times: it is pushed onto the stack/deque at most once, and it is popped from the stack/deque at most once. All stack/deque operations (`append`, `pop`, `popleft`, `appendleft`, peek at ends) are O(1) time complexity. Therefore, the total time complexity is linear.
*   **Space Complexity:**
    *   **O(N)**: In the worst-case scenario.
    *   **Justification:** In the worst case (e.g., a strictly increasing or decreasing array for a stack, or a window of size N for a deque), all elements might end up being stored in the monotonic stack/deque simultaneously. Thus, the space complexity can be proportional to the input size.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Highly Efficient:** Achieves linear time complexity (O(N)) for problems that might otherwise require quadratic time (e.g., brute-force nested loops). This is a significant performance gain.
*   **Optimal Performance:** For problems like "Next Greater Element" or "Sliding Window Maximum," the monotonic stack/queue approach is generally the most optimal solution.
*   **Elegant Solutions:** Once understood, the pattern often leads to clean and concise code, simplifying complex logic.
*   **Dynamic Queries:** Can efficiently answer queries about the "next" or "previous" relevant element without pre-computing all pairs.

### Cons:
*   **Specific Applicability:** This is a specialized pattern. It's only useful for problems that involve finding relationships between elements based on their relative values (greater/smaller) and their positions in a sequence. It's not a general-purpose data structure or algorithm.
*   **Non-Intuitive at First:** Understanding the exact conditions for pushing and popping elements to maintain monotonicity can be challenging initially. It requires careful conceptualization and practice. Drawing diagrams is highly recommended.
*   **Debugging Complexity:** Debugging can be tricky if the monotonic property is not maintained correctly.

## 7. Common Use Cases / Applications

The Monotonic Stack/Queue pattern is a versatile tool for specific classes of problems:

*   **Next Greater Element / Next Smaller Element:**
    *   Finding the first element to the right (or left) that is greater than (or smaller than) the current element.
    *   (e.g., LeetCode 496. Next Greater Element I, 503. Next Greater Element II).
*   **Largest Rectangle in Histogram:**
    *   Given an array of non-negative integers representing the height of bars in a histogram, find the largest rectangle in the histogram. This uses a monotonic stack to find the left and right boundaries of potential rectangles.
    *   (e.g., LeetCode 84. Largest Rectangle in Histogram).
*   **Trapping Rain Water:**
    *   Calculate how much rainwater can be trapped between bars of varying heights. This can be solved by finding the left and right maximums for each bar, which can be optimized with monotonic stacks.
    *   (e.g., LeetCode 42. Trapping Rain Water).
*   **Sliding Window Maximum / Minimum:**
    *   For every contiguous subarray of size `k`, find its maximum or minimum element. This is a classic application of a monotonic deque.
    *   (e.g., LeetCode 239. Sliding Window Maximum).
*   **Remove K Digits / Remove Duplicate Letters:**
    *   Problems that involve constructing the smallest/largest number or lexicographically smallest string by removing elements, where new elements might "invalidate" previous choices.
    *   (e.g., LeetCode 402. Remove K Digits).
*   **Stock Span Problem:** For each day, find the number of consecutive days for which the price was less than or equal to the current day's price.

## 8. Related Concepts / Variations

*   **Stacks / Queues / Deques:** The fundamental data structures used.
*   **Sliding Window:** Often combined with monotonic deques to achieve O(N) complexity for window-based maximum/minimum queries.
*   **Dynamic Programming:** Some problems solvable with monotonic stacks/queues can also be approached with DP, but often less efficiently (e.g., `Trapping Rain Water` can be O(N) with DP or O(N) with Monotonic Stack).
*   **Greedy Algorithms:** Many solutions using monotonic stacks/queues can be seen as greedy because they make optimal local decisions by immediately removing elements that cannot contribute to a better future solution.

## 9. LeetCode Problem Examples

*   [496. Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/) (Easy)
*   [503. Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/) (Medium)
*   [84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/) (Hard)
*   [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) (Hard)
*   [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) (Hard)
*   [402. Remove K Digits](https://leetcode.com/problems/remove-k-digits/) (Medium)

## 10. Further Reading / Resources

*   [GeeksforGeeks - Monotonic Stack](https://www.geeksforgeeks.org/monotonic-stack-data-structure/) - Provides clear explanations with visuals and code examples.
*   [HackerEarth - Monotonic Stack](https://www.hackerearth.com/practice/data-structures/stacks/basics-of-stacks/tutorial/) - Explores the concept from a competitive programming angle.
*   [LeetCode Article - A Comprehensive Overview of Monotonic Stack](https://leetcode.com/discuss/general-discussion/652079/a-comprehensive-overview-of-monotonic-stack) - A popular community post detailing various applications.
*   [YouTube - Nick White: Largest Rectangle in Histogram](https://www.youtube.com/watch?v=Kz48f1_gN_c) - Good video for understanding a complex application of monotonic stack.
