# Top 'K' Elements

## 1. Introduction / Concept
The Top 'K' Elements pattern is used to find the K largest, K smallest, K most frequent, or generally the K elements that satisfy some extreme condition from a given set of data. This pattern often leverages **heaps (priority queues)** for efficient retrieval and maintenance of the K elements.

## 2. Core Principles / Theory
The fundamental idea is to maintain a heap of size K that stores the "candidate" top K elements.

*   **For K Largest Elements:**
    *   Use a **Min-Heap** of size K.
    *   Iterate through all elements in the input.
    *   For each element:
        *   Push it onto the min-heap.
        *   If the heap's size exceeds K, pop the smallest element (which is the root of the min-heap).
    *   After processing all elements, the min-heap will contain the K largest elements. The smallest of these (the Kth largest overall) will be at the root.

*   **For K Smallest Elements:**
    *   Use a **Max-Heap** of size K.
    *   Iterate through all elements in the input.
    *   For each element:
        *   Push it onto the max-heap.
        *   If the heap's size exceeds K, pop the largest element (which is the root of the max-heap).
    *   After processing all elements, the max-heap will contain the K smallest elements. The largest of these (the Kth smallest overall) will be at the root.

*   **For K Most Frequent Elements:**
    1.  First, use a hash map (Python `dict` or `collections.Counter`) to count the frequency of all elements.
    2.  Then, use a Min-Heap of size K. Instead of just elements, store tuples of `(frequency, element)`. If using a min-heap to find *most* frequent, you'd store `(frequency, element)` and pop if size exceeds K, so the heap always holds the K *most frequent* elements seen so far. If two frequencies are equal, secondary sorting criteria might apply (e.g., value).

## 3. Operations / Methods
*   **`heapq.heappush(heap, item)`:** Adds an item to the heap.
*   **`heapq.heappop(heap)`:** Removes and returns the smallest item (for min-heap).
*   **Python Max-Heap Simulation:** Store `(-item)` for a max-heap. For `(frequency, element)` tuples, store `(-frequency, element)` to ensure highest frequency is popped first.
*   **`collections.Counter`:** For efficient frequency counting.

## 4. Python Implementation Details / Considerations
*   The `heapq` module is central to this pattern.
*   Remember the negative sign trick for max-heap behavior.
*   When storing tuples in a heap, Python compares them lexicographically (first element, then second, etc.). This is important for tie-breaking.

**Example: Kth Largest Element in an Array**

```python
import heapq

def find_kth_largest(nums, k):
    min_heap = [] # We use a min-heap to find the K largest elements

    for num in nums:
        heapq.heappush(min_heap, num)
        if len(min_heap) > k:
            heapq.heappop(min_heap) # Remove the smallest element if heap size exceeds k

    # The root of the min-heap will be the Kth largest element
    return min_heap[0]

# Example usage:
# print(find_kth_largest([3, 2, 1, 5, 6, 4], 2)) # Output: 5
# print(find_kth_largest([3, 2, 3, 1, 2, 4, 5, 5, 6], 4)) # Output: 4
```

## 5. Time and Space Complexity Analysis
*   **Time Complexity:**
    *   **O(N log K)**: N elements are processed. For each element, a heap operation (push/pop) takes O(log K) time since the heap size is limited to K.
    *   If frequency counting is involved (e.g., Top K Frequent Elements), an initial O(N) pass for counting is needed.
*   **Space Complexity:** **O(K)**: To store the K elements in the heap. If frequency counting is involved, add O(D) where D is the number of distinct elements (up to O(N)).

## 6. Pros and Cons / Trade-offs
**Pros:**
*   **Efficiency:** Much more efficient than full sorting (O(N log N)) when you only need a specific number of extreme elements (K << N).
*   **Dynamic Data:** Suitable for streams of data where elements arrive one by one.

**Cons:**
*   **Not O(1) space:** Requires O(K) space.
*   **More Complex than Simple Sort:** Involves understanding heap properties and operations.
*   **Quickselect Alternative:** For a *single* Kth element query on a static array, Quickselect (average O(N) time, O(1) space) can be faster, but it modifies the input array.

## 7. Common Use Cases / Applications
*   **Kth Largest/Smallest Element:** (e.g., LeetCode 215. Kth Largest Element in an Array).
*   **Top K Frequent Elements:** (e.g., LeetCode 347. Top K Frequent Elements).
*   **K Closest Points to Origin:** (e.g., LeetCode 973. K Closest Points to Origin).
*   **Smallest Number of Disappeared Elements.**
*   **Finding the Median of a Data Stream:** (This uses the Two Heaps pattern, which is a specialized case of Top K elements).

## 8. Related Concepts / Variations
*   **Heaps (Priority Queues):** The core data structure.
*   **Hash Maps (`collections.Counter`):** Often used in conjunction with heaps for frequency-based problems.
*   **Quickselect:** An alternative partitioning algorithm for finding the Kth element in O(N) average time (but modifies array).

## 9. LeetCode Problem Examples
*   [215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/) (Medium)
*   [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) (Medium)
*   [973. K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/) (Medium)
*   [703. Kth Largest Element in a Stream](https://leetcode.com/problems/kth-largest-element-in-a-stream/) (Easy)
*   [692. Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/) (Medium)

## 10. Further Reading / Resources
*   [GeeksforGeeks - K Largest(or Smallest) Elements in an array](https://www.geeksforgeeks.org/k-largestor-smallest-elements-in-an-array/)
*   [Educative.io - Top 'K' Elements Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/gkpz5gqj91x)
