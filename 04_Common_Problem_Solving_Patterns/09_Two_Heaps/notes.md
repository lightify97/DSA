# Two Heaps

## 1. Introduction / Concept

Imagine you are receiving a continuous stream of numbers and, at any point, you need to quickly tell what the **median** of all numbers received so far is. Or perhaps you need to find the **Kth smallest** or **Kth largest** element efficiently as new data arrives. Keeping the entire list sorted or re-sorting it every time would be too slow.

The **Two Heaps pattern** (also known as the Min-Max Heap pattern) is a clever technique that uses a combination of two heap data structures – typically a **Min-Heap** and a **Max-Heap** – to efficiently maintain an ordered set of elements. It allows for quick queries (O(1) or O(log N)) about median or other ranked elements from dynamically changing data.

This pattern is especially useful when dealing with data streams where you need to keep track of the "middle" or "extreme" elements without storing or sorting everything.

## 2. Core Principles / Theory

The core idea is to logically divide the incoming stream of numbers into two halves and maintain them using two heaps:

1.  **Max-Heap (or `small_numbers_heap`):** This heap stores the **smaller half** of the numbers. Since it's a max-heap, its root (top element) will always be the largest number among the smaller half.
    *   All elements in the Max-Heap are `_less than or equal to_` all elements in the Min-Heap.
    *   *(Imagine this heap holding numbers like [1, 2, 3, 4, 5] with 5 at the top.)*
2.  **Min-Heap (or `large_numbers_heap`):** This heap stores the **larger half** of the numbers. Since it's a min-heap, its root (top element) will always be the smallest number among the larger half.
    *   All elements in the Min-Heap are `_greater than or equal to_` all elements in the Max-Heap.
    *   *(Imagine this heap holding numbers like [6, 7, 8, 9, 10] with 6 at the top.)*

### Key Invariants to Maintain:

For the Two Heaps pattern to work correctly, two crucial invariants must be continuously maintained:

1.  **Ordering Invariant:** `Max-Heap.top() <= Min-Heap.top()`
    *   The largest element in the `max_heap` (representing the upper bound of the smaller half) must always be less than or equal to the smallest element in the `min_heap` (representing the lower bound of the larger half). This ensures the "split" is correct.
    *   *(If `max_heap.top()` were greater than `min_heap.top()`, it means a number from the "smaller half" is actually larger than a number from the "larger half", which is wrong. We'd need to swap them.)*
2.  **Size Balance Invariant:** The sizes of the two heaps should be balanced, typically differing by at most 1 element.
    *   If the total number of elements processed so far (N) is even, both heaps should ideally have `N/2` elements.
    *   If N is odd, one heap (usually the one that contains the median, often the Max-Heap for problems asking for the lower median) will have `(N/2) + 1` elements, and the other will have `N/2` elements.
    *   *(This balance is what keeps the "median" or Kth element at or near the top of the heaps.)*

### Algorithm Steps for Adding a New Number:

When a new number `num` arrives:

1.  **Placement:**
    *   If `num` is less than or equal to the root of the `max_heap` (or if `max_heap` is empty), push `num` into the `max_heap`.
    *   Otherwise, push `num` into the `min_heap`.
    *(This step tries to maintain the ordering invariant, placing the new number in its correct "half".)*

2.  **Rebalancing (Ordering):**
    *   After adding, immediately check if the ordering invariant (`max_heap.top() <= min_heap.top()`) is violated. If `max_heap.top() > min_heap.top()` (i.e., largest in smaller half is greater than smallest in larger half), pop from `max_heap` and push to `min_heap`, and vice-versa.
    *(This ensures correct segregation of smaller/larger numbers.)*

3.  **Rebalancing (Size):**
    *   After ensuring ordering, check the size balance.
    *   If `max_heap` has more than `min_heap` + 1 elements, move the root of `max_heap` to `min_heap`.
    *   If `min_heap` has more than `max_heap`, move the root of `min_heap` to `max_heap`.
    *(This step keeps the heaps roughly equal in size, ensuring the median is at or near the roots.)*

### Retrieving Median / Kth Element:

*   **Median:**
    *   If heap sizes are equal, the median is `(max_heap.top() + min_heap.top()) / 2`.
    *   If `max_heap` has one more element (N is odd), the median is `max_heap.top()`.
    *   (Or `min_heap.top()` if `min_heap` is designated to hold the extra element for odd N).
*   **Kth Element:** Depends on how the heaps are set up. If finding the Kth smallest, it might be the top of the max-heap, etc.

## 3. Operations / Methods

The pattern relies heavily on heap operations:

*   **`heapq.heappush(heap, item)`:** Pushes an item onto the heap. Time: O(log H), where H is the heap size.
*   **`heapq.heappop(heap)`:** Pops and returns the smallest item from the heap (for a min-heap). Time: O(log H).
*   **`heap[0]`:** Accesses the smallest element (root) of a min-heap in O(1) time.

## 4. Python Implementation Details / Considerations

*   **`heapq` Module:** Python's built-in `heapq` module is essential. It provides a standard implementation of a **min-heap**.
*   **Simulating a Max-Heap:** Since `heapq` is only a min-heap, to get max-heap behavior, you must store the **negation** of the numbers. When you pop a value, negate it back to get the actual largest number.
    *   To add `X` to max-heap: `heapq.heappush(max_heap, -X)`
    *   To get `top` of max-heap: `-max_heap[0]`
    *   To pop from max-heap: `-heapq.heappop(max_heap)`
*   **Tuples in Heaps:** For problems involving (value, frequency) or (distance, point) pairs, `heapq` works correctly with tuples, comparing them lexicographically (first element, then second, etc.). If values can be duplicated, you might need a tie-breaker like `(value, unique_id, data)` to prevent Python from trying to compare non-comparable objects if value tie occurs.

### Example: Find Median from Data Stream (LeetCode 295)

This is the canonical problem for the Two Heaps pattern. We need to maintain a running median.

```python
import heapq

class MedianFinder:
    def __init__(self):
        # max_heap (self.max_heap): stores the smaller half of the numbers.
        # Python's heapq is a min-heap, so we store negative values to simulate a max-heap.
        # Top of this heap is the largest number in the smaller half.
        self.max_heap = [] 
        
        # min_heap (self.min_heap): stores the larger half of the numbers.
        # Top of this heap is the smallest number in the larger half.
        self.min_heap = []

    def addNum(self, num: int) -> None:
        # Step 1: Add the new number to the appropriate heap based on its value.
        # If the max_heap is empty, or the number is smaller than or equal to the
        # largest element in the max_heap (which is -self.max_heap[0]), add to max_heap.
        # Otherwise, add to min_heap.
        if not self.max_heap or num <= -self.max_heap[0]:
            heapq.heappush(self.max_heap, -num)
        else:
            heapq.heappush(self.min_heap, num)

        # Step 2: Rebalance the heaps to maintain size invariant.
        # The max_heap (smaller half) can have at most one more element than the min_heap (larger half).
        # This arrangement ensures that for an odd number of elements, the median is always in the max_heap.

        # If max_heap is too large: move its root to min_heap.
        if len(self.max_heap) > len(self.min_heap) + 1:
            val = -heapq.heappop(self.max_heap) # Pop from max_heap (smallest negative -> largest actual)
            heapq.heappush(self.min_heap, val)
        # If min_heap is too large: move its root to max_heap.
        elif len(self.min_heap) > len(self.max_heap):
            val = heapq.heappop(self.min_heap) # Pop from min_heap (smallest actual)
            heapq.heappush(self.max_heap, -val) # Push to max_heap (as negative)

    def findMedian(self) -> float:
        # If total elements are even, median is average of roots of both heaps.
        if len(self.max_heap) == len(self.min_heap):
            return (-self.max_heap[0] + self.min_heap[0]) / 2.0
        # If total elements are odd, median is the root of the max_heap (which holds the extra element).
        else:
            return -self.max_heap[0] # Negate to get actual value

# Edge Cases and Examples:
mf = MedianFinder()

mf.addNum(1) # Stream: [1] -> max_heap: [-1], min_heap: []
print(f"Add 1, Median: {mf.findMedian()}") # Output: 1.0

mf.addNum(2) # Stream: [1, 2] -> max_heap: [-1], min_heap: [2]
print(f"Add 2, Median: {mf.findMedian()}") # Output: 1.5

mf.addNum(3) # Stream: [1, 2, 3] -> max_heap: [-2,-1], min_heap:[3] (balanced after adding)
             # Tracing addNum(3) with refined logic:
             # 1. Push -3 to max_heap: max_heap=[-3,-1], min_heap=[2]
             # 2. Check ordering: -max_heap[0](3) > min_heap[0](2). Pop -3 from max_heap, push 3 to min_heap.
             #    max_heap=[-1], min_heap=[2,3]
             # 3. Balance sizes: len(min_heap)(2) > len(max_heap)(1). Pop 2 from min_heap, push -2 to max_heap.
             #    max_heap=[-2,-1], min_heap=[3]
print(f"Add 3, Median: {mf.findMedian()}") # Output: 2.0

mf.addNum(4) # Stream: [1, 2, 3, 4]
             # Tracing addNum(4) with refined logic:
             # 1. Push -4 to max_heap: max_heap=[-4,-2,-1], min_heap=[3]
             # 2. Check ordering: -max_heap[0](4) > min_heap[0](3). Pop -4 from max_heap, push 4 to min_heap.
             #    max_heap=[-2,-1], min_heap=[3,4]
             # 3. Balance sizes: len(min_heap)(2) == len(max_heap)(2). No rebalance.
print(f"Add 4, Median: {mf.findMedian()}") # Output: 2.5

mf.addNum(0) # Stream: [0, 1, 2, 3, 4]
             # Tracing addNum(0) with refined logic:
             # 1. Push -0 to max_heap: max_heap=[-2,-1,0], min_heap=[3,4]
             # 2. Check ordering: -max_heap[0](2) < min_heap[0](3). No ordering rebalance needed.
             # 3. Balance sizes: len(max_heap)(3) > len(min_heap)(2)+1 (false). len(min_heap)(2) > len(max_heap)(3) (false).
             #    Oh, max_heap has 3, min_heap has 2. It's balanced for odd.
print(f"Add 0, Median: {mf.findMedian()}") # Output: 2.0 (Correct median for [0,1,2,3,4] is 2)
```
*   **Edge Cases Handled:** Empty stream, single element, even/odd number of elements, ensuring `max_heap` (smaller half) is always either equal in size or one larger than `min_heap` (larger half). The logic handles various numerical inputs correctly.

## 5. Time and Space Complexity Analysis

The Two Heaps pattern offers excellent performance for dynamic median/Kth element queries.

*   **Time Complexity:**
    *   **O(log N)**: For each `addNum` operation, where N is the total number of elements currently stored in the data structure.
    *   **Justification:** Heap `push` (`heapq.heappush`) and `pop` (`heapq.heappop`) operations take logarithmic time relative to the heap's size. Since the two heaps are always kept balanced (their sizes are roughly N/2), any operation on either heap is O(log(N/2)), which simplifies to O(log N). This makes inserting and maintaining the ordered structure very efficient.
    *   **O(1)**: For `findMedian` or similar queries, as it only involves accessing the root (top element) of one or both heaps, which is an constant time operation.
*   **Space Complexity:**
    *   **O(N)**: Where N is the total number of elements that have been added to the data structure and are currently being stored across both heaps.
    *   **Justification:** The algorithm needs to store all numbers that have arrived in the stream to calculate the current median/Kth element. Therefore, the space complexity grows linearly with the number of elements.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Highly Efficient for Dynamic Data:** Superb for problems where numbers arrive as a stream, and you need to query the median, or other Kth smallest/largest elements frequently without having to sort the entire dataset repeatedly.
*   **Optimal for Median/Kth Element Queries:** Provides a highly efficient and often near-optimal solution for maintaining ranked statistics in a dynamic context, outperforming brute-force (re-sort) or less sophisticated methods.
*   **Real-time Applications:** Well-suited for scenarios like real-time analytics, dashboards, or monitoring systems where quick access to median, percentiles, or top values of streaming data is needed.

### Cons:
*   **Increased Implementation Complexity:** Compared to simpler data structures or full sorting, setting up and correctly managing two balanced heaps with precise invariants (ordering and size) can be intricate. The "max-heap via negation" in Python adds a layer of conceptual complexity that requires careful attention.
*   **Memory Usage:** Requires storing all elements in the heaps, so it uses O(N) space. For extremely massive datasets that don't fit in memory (e.g., truly unbounded streams where elements are never removed), this might be a limitation, potentially requiring external sorting or approximate algorithms.
*   **Specific Scope:** Primarily useful for finding medians, percentiles, or Kth ranked elements. It is not a general-purpose solution for all dynamic data problems.

## 7. Common Use Cases / Applications

The Two Heaps pattern is a specialized, but highly valuable, technique:

*   **Finding the Median of a Data Stream:** (e.g., LeetCode 295). This is its most direct and common application, as demonstrated in the example.
*   **Sliding Window Median:** This combines the Sliding Window pattern with the Two Heaps pattern. For a sliding window of size `K`, you need to efficiently add/remove elements and query the median. This often involves additional logic for "lazy deletion" from heaps (marking elements for deletion without immediately popping them) or a custom data structure.
*   **Top K Frequent Elements (Advanced Dynamic Variations):** While `collections.Counter` and a single min-heap are common for `Top K Frequent`, more complex variations involving dynamic updates (elements being added/removed from counts) might benefit from a Two Heaps approach.
*   **Any problem where you need to maintain a dynamically ordered set (or subset) and query its middle or extreme values in sub-linear time (O(log N)).**

## 8. Related Concepts / Variations

*   **Heaps (Min-Heap, Max-Heap):** These are the fundamental data structures upon which the Two Heaps pattern is built. Understanding their properties (heap property, complete binary tree property) is essential.
*   **Priority Queues:** Heaps are used to implement priority queues, which this pattern leverages for efficient selection of minimum/maximum elements.
*   **Sliding Window:** Often combined with Two Heaps for problems like "Sliding Window Median," where elements enter and leave the window dynamically.
*   **Quickselect:** An alternative algorithm for finding the Kth smallest/largest element in a *static* array in average O(N) time and O(1) space. It's often faster than Two Heaps for a single query on a static dataset but cannot efficiently handle data streams.
*   **Order Statistics Tree (Augmented Self-Balancing BSTs):** More advanced data structures like augmented AVL Trees or Red-Black Trees can also support finding Kth smallest/largest elements in O(log N) time and dynamic insertions/deletions. However, they are significantly more complex to implement than heaps.

## 9. LeetCode Problem Examples

*   [295. Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/) (Hard) - The quintessential problem for this pattern.
*   [480. Sliding Window Median](https://leetcode.com/problems/sliding-window-median/) (Hard) - A challenging problem combining two patterns (Sliding Window + Two Heaps).
*   [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/) (Hard) - While often solved with a single min-heap of size K, it demonstrates the efficient use of `heapq` for merging sorted streams.
*   [632. Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) (Hard) - Another problem that uses a min-heap to keep track of the minimum across K lists, conceptually related to managing multiple sorted sequences.

## 10. Further Reading / Resources

*   [GeeksforGeeks - Find median in a stream of integers (running integers)](https://www.geeksforgeeks.org/find-median-given-stream-numbers/) - Provides a detailed explanation and code.
*   [Educative.io - Two Heaps Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/xVlKz9l2Ngj) - Offers a structured learning path with problem examples focusing on this pattern.
*   [NeetCode.io - Heaps / Priority Queues](https://neetcode.io/roadmap) - While not explicitly "Two Heaps" in name, his videos for problems like "Find Median from Data Stream" directly apply this pattern.
*   [YouTube - Nick White: Find Median from Data Stream](https://www.youtube.com/watch?v=yv6hYfE_05A) - A helpful video walkthrough of the core problem.
