# K-way Merge

## 1. Introduction / Concept

Imagine you have many separate, already sorted lists of numbers (e.g., from different sensors, databases, or previously sorted chunks of a very large file). Your goal is to combine all these `K` sorted lists into a single, comprehensive sorted list. A naive approach would be to concatenate them all and then sort the giant list, which could be very inefficient.

The **K-way Merge pattern** is an efficient technique used to merge 'K' sorted arrays, lists, or streams into a single sorted output. It is a generalization of the classic merge operation found in Merge Sort, where instead of merging just two sorted lists, you efficiently merge an arbitrary number `K` of sorted lists.

This pattern is fundamental in external sorting algorithms (when data doesn't fit in memory) and other scenarios involving multiple sorted inputs.

## 2. Core Principles / Theory

The key to efficient K-way merging is to always pick the smallest available element from among the `K` lists without repeatedly scanning all lists. A **Min-Heap (Priority Queue)** is the perfect data structure for this.

### The Algorithm using a Min-Heap:

1.  **Initialization:**
    *   Create an empty Min-Heap.
    *   For each of the `K` input sorted lists:
        *   If the list is not empty, add its **first element** to the min-heap.
        *   When adding to the heap, it's crucial to store not just the value, but also information about **which list** the element came from, and potentially its **index** within that list (so you know which element to add next from that list). A common way to store this is as a tuple: `(value, list_index, element_index_in_list)` or `(value, list_index, Node_object)` for linked lists.
            *(Imagine having `K` pointers, one at the start of each list. You take the smallest of the K values currently pointed to, add it to your result, and then advance the pointer in *that specific list*.)*

2.  **Iterative Merging:**
    *   While the min-heap is not empty:
        a.  **Extract Smallest:** `(value, list_index, next_element_source)` = `heapq.heappop(min_heap)`
            *   Extract the smallest element from the root of the min-heap. This `value` is the next element in the final merged sorted output.
        b.  **Add to Result:** Append this `value` to your `merged_output_list`.
        c.  **Advance and Push:**
            *   Determine which list the extracted `value` came from using `list_index`.
            *   Check if there is a `next_element` available in that specific list (e.g., `if next_element_source.next` for linked lists, or `if element_index_in_list + 1 < len(list_from_which_it_came)` for arrays).
            *   If there is a `next_element`, add it to the min-heap, along with its `list_index` and updated `element_index_in_list`.
            *(You always put the *next* available element from the list that just contributed its smallest element back into consideration in the heap.)*

3.  **Completion:** The loop continues until the min-heap is empty, at which point all elements from all `K` lists have been processed and added to the `merged_output_list` in sorted order.

## 3. Operations / Methods

*   **`heapq` Module:** The core of this pattern in Python.
    *   `heapq.heappush(heap, item)`: Adds an item to the heap (O(log H)).
    *   `heapq.heappop(heap)`: Removes and returns the smallest item (root) from the heap (O(log H)).
*   **Tuple Storage:** Storing elements as tuples like `(value, list_identifier, next_reference)` is common. Python's `heapq` naturally sorts tuples lexicographically, first by `value`. The `list_identifier` or `next_reference` is usually needed to correctly identify which list to pull the next element from. A unique ID as a tie-breaker (e.g., `(value, unique_counter, node_object)`) might be added to avoid comparing arbitrary objects if values are identical.

## 4. Python Implementation Details / Considerations

*   **`heapq` Usage:** Direct application of `heapq` for min-heap behavior.
*   **List vs. Linked List Input:**
    *   **Arrays/Lists:** If input is `list[list[int]]`, you'd store `(value, list_idx, element_idx_in_list)` in the heap.
    *   **Linked Lists:** If input is `list[ListNode]`, you'd store `(node.val, list_idx, node)` in the heap. Be careful with Python's object comparison if you directly put `ListNode` objects in the tuple without a tie-breaker, as they might not be directly comparable if `__lt__` is not defined for `ListNode`. Adding a unique `list_idx` as a tie-breaker works well: `(node.val, list_idx, node)`.
*   **Dummy Head Node:** For merging linked lists, using a `dummy_head` node for the merged list simplifies appending the first element and avoids special casing.

### Example: Merge K Sorted Lists (LeetCode 23)

This is the classic problem for K-way Merge using a min-heap.

```python
import heapq

# Define a simple ListNode class, usually provided by LeetCode or your environment.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def merge_k_lists(lists: list[ListNode]) -> ListNode:
    # Initialize a min-heap. This heap will store tuples of (value, list_index, ListNode object).
    # The 'list_index' serves as a tie-breaker for elements with the same value,
    # preventing Python from attempting to compare ListNode objects directly when values are equal.
    min_heap = [] 
    
    # Step 1: Initialize the min-heap with the first node from each non-empty list.
    for i, head_node in enumerate(lists):
        if head_node: # Only add if the list is not empty
            heapq.heappush(min_heap, (head_node.val, i, head_node))

    # Create a dummy head for the merged linked list.
    # This simplifies handling the first node of the merged list.
    dummy_head = ListNode(0)
    current_merged_node = dummy_head # Pointer to the last node added to the merged list

    # Step 2: Continuously extract the smallest element from the heap and add its successor.
    while min_heap:
        # Extract the smallest element from the heap.
        # It's a tuple: (value, original_list_index, actual_node_object)
        val, list_idx, node = heapq.heappop(min_heap)
        
        # Append the extracted node to the merged list.
        current_merged_node.next = node
        current_merged_node = current_merged_node.next

        # If the extracted node has a next element in its original list,
        # add that next element to the heap.
        if node.next:
            heapq.heappush(min_heap, (node.next.val, list_idx, node.next))
            
    # The merged list starts from dummy_head.next.
    return dummy_head.next

# Helper function to create a linked list from a list (for testing)
def create_linked_list_from_list(arr: list) -> ListNode:
    if not arr:
        return None
    head = ListNode(arr[0])
    current = head
    for val in arr[1:]:
        current.next = ListNode(val)
        current = current.next
    return head

# Helper function to convert a linked list to a list (for printing)
def linked_list_to_list(head: ListNode) -> list:
    result = []
    current = head
    while current:
        result.append(current.val)
        current = current.next
    return result

# Edge Cases and Examples:
# Example 1: Standard case
list1 = create_linked_list_from_list([1, 4, 5])
list2 = create_linked_list_from_list([1, 3, 4])
list3 = create_linked_list_from_list([2, 6])
merged_head = merge_k_lists([list1, list2, list3])
print(f"Merged lists [[1,4,5],[1,3,4],[2,6]]: {linked_list_to_list(merged_head)}") 
# Output: [1, 1, 2, 3, 4, 4, 5, 6]

# Example 2: Empty input list of lists
print(f"Merged []: {linked_list_to_list(merge_k_lists([]))}") 
# Output: []

# Example 3: List of empty lists
print(f"Merged [[]]: {linked_list_to_list(merge_k_lists([None]))}") 
# Output: [] (A list containing a None/empty list)
print(f"Merged [[],[]]: {linked_list_to_list(merge_k_lists([None, None]))}")
# Output: []

# Example 4: Single non-empty list
list_single = create_linked_list_from_list([7, 8, 9])
print(f"Merged [[7,8,9]]: {linked_list_to_list(merge_k_lists([list_single]))}") 
# Output: [7, 8, 9]

# Example 5: Lists with varying lengths and duplicates
list_a = create_linked_list_from_list([1, 1, 10])
list_b = create_linked_list_from_list([2, 5, 9])
list_c = create_linked_list_from_list([3, 6, 8])
merged_head_2 = merge_k_lists([list_a, list_b, list_c])
print(f"Merged lists with duplicates: {linked_list_to_list(merged_head_2)}")
# Output: [1, 2, 3, 5, 6, 8, 9, 10] (1,1,2,3,5,6,8,9,10, but the example has one 1 in list_a so 1,1,10)
# Correct output for [1,1,10], [2,5,9], [3,6,8]: [1,1,2,3,5,6,8,9,10]
```
*   **Edge Cases Handled:** Empty input list, list of empty lists, single non-empty list, lists with varying lengths and duplicates. The use of `(value, list_idx, node)` in the heap and a `dummy_head` handles many complexities cleanly.

## 5. Time and Space Complexity Analysis

The K-way Merge pattern provides an efficient approach that scales well with the number of lists.

*   **Time Complexity:**
    *   **O(N log K)**: Where N is the total number of elements across all `K` input lists, and `K` is the number of lists.
    *   **Justification:**
        *   **Initialization:** Adding the first element from each of the `K` lists to the min-heap takes O(K log K) time (K pushes, each O(log K) as heap size is K).
        *   **Main Merging Loop:** There are a total of `N` elements across all lists that need to be extracted and added to the merged output. For each element extracted:
            *   `heapq.heappop()` takes O(log K) time (heap size is at most K).
            *   `heapq.heappush()` (for the next element from the same list) also takes O(log K) time.
        *   Thus, `N` elements undergo O(log K) operations each, leading to O(N log K) for the main loop.
        *   Since `N log K` is typically greater than `K log K` for large `N`, the overall time complexity is dominated by **O(N log K)**.
*   **Space Complexity:**
    *   **O(K)**: For storing the `K` elements (at most one from each list) in the min-heap.
    *   **Justification:** The heap's size is bounded by the number of input lists, `K`.
    *   Additionally, O(N) space is used for the final `merged_output_list` if it's constructed anew.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **High Efficiency:** Significantly more efficient than simply concatenating all lists and then sorting the entire combined list (which would be O(N log N), where N is the total elements, possibly worse if K is small relative to N). It also beats repeatedly merging two lists at a time.
*   **Scalability:** Works very well even when `K` (the number of lists) is large, as the heap operations depend on `log K` rather than `K`.
*   **Generalizable:** Applicable to merging any sorted data streams, whether they are arrays, linked lists, or files.
*   **Handles Different Lengths:** Naturally handles input lists of varying lengths, including empty ones.

### Cons:
*   **Increased Complexity:** More complex to implement and manage correctly than a simple two-list merge. Requires understanding of heaps and careful handling of the tuple structure for heap elements.
*   **Memory Overhead (for K):** While efficient for large N, it requires O(K) extra space for the heap. If `K` is very large and memory is constrained, this could be a factor.
*   **Not Always Necessary:** If `K` is very small (e.g., K=2), a simple two-pointer merge (O(N) time, O(1) space) is faster and simpler. This pattern shines when `K > 2`.

## 7. Common Use Cases / Applications

The K-way Merge pattern is a powerful technique with several important applications:

*   **Merging K Sorted Lists/Arrays:** (e.g., LeetCode 23). This is the direct application.
*   **Finding the Smallest Range Covering Elements from K Lists:** Given `K` sorted lists, find the smallest range `[a, b]` that contains at least one element from each of the `K` lists. This problem often uses a min-heap to keep track of the minimum current element across all lists and a mechanism to track the maximum element.
*   **Finding the Kth Smallest Element in M Sorted Arrays:** Similar to merging, but you stop once you have extracted the Kth element.
*   **External Sort:** When a dataset is too large to fit into main memory, it is typically divided into smaller chunks, each of which is sorted independently (e.g., using Merge Sort). These sorted chunks are then merged using a K-way merge process. This is fundamental for big data processing.
*   **Database Merges:** Combining sorted results from different database queries or partitions.

## 8. Related Concepts / Variations

*   **Heaps (Priority Queues):** The fundamental data structure for this pattern. A strong understanding of heap operations is necessary.
*   **Merge Sort:** The inspiration for the "merge" operation. K-way merge can be seen as an extension of the final merging step of Merge Sort.
*   **Divide and Conquer:** An alternative approach for merging K sorted lists is to use a divide and conquer strategy (e.g., merge `lists[0]` and `lists[1]`, then `lists[2]` and `lists[3]`, then merge their results, and so on). This also results in O(N log K) time complexity but might have different constant factors and recursive overhead. For K-way merge, the heap-based approach is often preferred for its iterative nature and clearer logic.
*   **Two Pointers:** For merging just two sorted lists, a two-pointer approach is more efficient (O(N) time, O(1) space) than a heap.

## 9. LeetCode Problem Examples

*   [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/) (Hard) - The quintessential problem for this pattern.
*   [632. Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) (Hard) - A more advanced problem combining K-way merge concepts with range tracking.
*   [378. Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/) (Medium) - Can be solved by viewing each row (or column) as a sorted list and applying K-way merge logic.
*   [373. Find K Pairs with Smallest Sums](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/) (Medium) - Uses a min-heap and conceptually similar to K-way merge.

## 10. Further Reading / Resources

*   [GeeksforGeeks - Merge K Sorted Arrays](https://www.geeksforgeeks.org/merge-k-sorted-arrays/) - Provides a good overview with explanations and diagrams.
*   [Educative.io - K-way Merge Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/xokm6R1k05z) - Offers a structured approach with examples and common variations.
*   [NeetCode.io - K Sorted Lists (for Merge k Sorted Lists)](https://neetcode.io/roadmap) - Watch his video solution for problem 23.
*   [Wikipedia - K-way merge algorithm](https://en.wikipedia.org/wiki/K-way_merge_algorithm) - Provides a formal definition and context.
