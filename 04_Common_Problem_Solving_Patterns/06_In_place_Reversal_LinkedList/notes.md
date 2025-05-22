# In-place Reversal of a Linked List

## 1. Introduction / Concept

Imagine you have a chain of paper clips, linked one after another, representing a linked list. Each paper clip points to the *next* one in the sequence. To reverse the chain "in-place" means you don't break it into individual clips and then re-form a new chain. Instead, you go through the existing chain, and for each clip, you re-bend its connection to point to the *previous* clip.

The **In-place Reversal of a Linked List pattern** involves changing the direction of pointers within a linked list (or a sub-part of it) **without using any auxiliary data structures** (like arrays, hash maps, or creating new nodes). This means performing the reversal using only a few extra pointer variables, thus achieving O(1) space complexity.

Mastering this pattern is fundamental for linked list manipulation, as it forms the basis for many more complex linked list problems.

## 2. Core Principles / Theory

The fundamental idea is to iterate through the linked list, and at each node, modify its `next` pointer to point to the `previous` node instead of the `next` node in the original sequence. This requires carefully managing three pointers:

1.  **`prev` (Previous Node):** This pointer keeps track of the node that was just processed. After a node's `next` pointer is reversed, it will point to `prev`. Initially, `prev` is `None` because the new head (which was the original tail) will point to `None`.
2.  **`curr` (Current Node):** This pointer points to the node currently being processed. Its `next` pointer will be reversed. Initially, `curr` is the `head` of the list.
3.  **`next_node` (Temporary Storage):** This pointer is crucial. Before `curr.next` is modified (to point to `prev`), we *must* store a reference to the node that `curr.next` originally pointed to. If we don't, we would lose the rest of the list.

### The Iteration Process (Step-by-Step for each `curr` node):

*(Imagine a chain of nodes like `A -> B -> C -> D -> None`. We start with `prev = None`, `curr = A`.)*

1.  **Store `curr.next`:** `next_node = curr.next`
    *   This saves the link to the *rest of the original list* before we overwrite `curr.next`.
    *   (e.g., if `curr` is `A`, `next_node` points to `B`.)

2.  **Reverse the link:** `curr.next = prev`
    *   This is the core reversal step. The `next` pointer of the `curr` node is now pointing backward to `prev`.
    *   (e.g., `A.next` now points to `None` in the first iteration. In the next, `B.next` points to `A`.)

3.  **Move `prev` forward:** `prev = curr`
    *   The `curr` node, having had its pointer reversed, now becomes the `prev` node for the *next* iteration.
    *   (e.g., `prev` becomes `A`.)

4.  **Move `curr` forward:** `curr = next_node`
    *   The `curr` pointer now moves to the next node in the original sequence (which we saved in `next_node`).
    *   (e.g., `curr` becomes `B`.)

This process repeats until `curr` becomes `None`. At the end, `prev` will be pointing to the new head of the reversed list (which was the original tail).

*(Visual Example:
Initial: `None <- prev   A -> B -> C -> None`
Iteration 1:
1. `next_node = B`
2. `A.next = None`
3. `prev = A`
4. `curr = B`
State: `None <- A <- prev   B -> C -> None`

Iteration 2:
1. `next_node = C`
2. `B.next = A`
3. `prev = B`
4. `curr = C`
State: `None <- A <- B <- prev   C -> None`

And so on, until `curr` is `None` and `prev` is `C` (the original tail, now the new head).)*

## 3. Operations / Methods

The core operations are pointer manipulation within a `while` loop:

*   `prev = None` (initialization)
*   `curr = head` (initialization)
*   `while curr:` (loop condition)
*   `next_node = curr.next` (saving next reference)
*   `curr.next = prev` (reversing link)
*   `prev = curr` (advancing prev)
*   `curr = next_node` (advancing curr)

## 4. Python Implementation Details / Considerations

*   **`ListNode` Class:** You'll typically define a simple class for linked list nodes.
    ```python
    class ListNode:
        def __init__(self, val=0, next=None):
            self.val = val
            self.next = next
    ```
*   **Handling Edge Cases:**
    *   **Empty List:** If `head` is `None`, the loop `while curr:` won't run, and `reverse_linked_list(None)` will correctly return `None`.
    *   **Single Node List:** If `head` is `ListNode(val)` and `head.next` is `None`:
        *   `prev = None`, `curr = head`
        *   Loop starts: `curr` is `ListNode(val)`.
        *   `next_node = curr.next` (which is `None`).
        *   `curr.next = prev` (so `head.next` becomes `None`).
        *   `prev = curr` (so `prev` becomes `head`).
        *   `curr = next_node` (so `curr` becomes `None`).
        *   Loop terminates. Returns `prev`, which is `head` (the single node pointing to `None`), correctly reversed.

### Example: Reverse a Singly Linked List (LeetCode 206)

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def reverse_linked_list(head: ListNode) -> ListNode:
    # Initialize pointers
    # `prev` will eventually be the new head of the reversed list
    prev = None 
    # `curr` starts at the head of the original list
    curr = head

    # Iterate through the list until `curr` becomes None (end of original list)
    while curr:
        # Step 1: Temporarily store the next node in the original list.
        # This is critical to avoid losing the rest of the list.
        next_node = curr.next 
        
        # Step 2: Reverse the current node's `next` pointer.
        # Make it point to the `prev` node.
        curr.next = prev      
        
        # Step 3: Move `prev` to the current node.
        # The current node is now the "previous" node for the next iteration.
        prev = curr           
        
        # Step 4: Move `curr` to the next node in the original list.
        # This advances the `curr` pointer to continue traversal.
        curr = next_node      
    
    # When the loop finishes, `curr` is None, and `prev` points to the last
    # node of the original list, which is now the head of the reversed list.
    return prev 

# Helper function to create a linked list from a list
def create_linked_list(arr: list) -> ListNode:
    if not arr:
        return None
    head = ListNode(arr[0])
    current = head
    for val in arr[1:]:
        current.next = ListNode(val)
        current = current.next
    return head

# Helper function to convert a linked list to a list for printing
def linked_list_to_list(head: ListNode) -> list:
    result = []
    current = head
    while current:
        result.append(current.val)
        current = current.next
    return result

# Edge Cases and Examples:
print(f"Original: [1,2,3,4,5], Reversed: {linked_list_to_list(reverse_linked_list(create_linked_list([1,2,3,4,5])))}") # Output: [5, 4, 3, 2, 1]
print(f"Original: [1], Reversed: {linked_list_to_list(reverse_linked_list(create_linked_list([1])))}")             # Output: [1] (Single node)
print(f"Original: [], Reversed: {linked_list_to_list(reverse_linked_list(create_linked_list([])))}")               # Output: [] (Empty list)
print(f"Original: [1,2], Reversed: {linked_list_to_list(reverse_linked_list(create_linked_list([1,2])))}")         # Output: [2, 1]
```
*   **Edge Cases Handled:** Empty list, single-node list. The core `while curr:` loop and `next_node` handling correctly manage these.

## 5. Time and Space Complexity Analysis

In-place linked list reversal is an optimally efficient solution for its task.

*   **Time Complexity:**
    *   **O(N)**: Where N is the number of nodes in the linked list.
    *   **Justification:** Each node in the linked list is visited exactly once. For each node, a constant number of pointer manipulations are performed. Therefore, the total time is directly proportional to the number of nodes.
*   **Space Complexity:**
    *   **O(1)**: (Constant auxiliary space).
    *   **Justification:** The algorithm only uses a fixed number of extra variables (`prev`, `curr`, `next_node`) to store pointers. The space required does not grow with the size of the input list.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Optimal Space Efficiency:** Achieves the task of reversing a linked list using the absolute minimum amount of extra memory. This is its primary advantage.
*   **Fundamental Technique:** Mastering this pattern is crucial for solving many other, more complex linked list problems. Mastering it unlocks the ability to tackle problems like reversing specific sub-sections of a list, checking for palindromes, or reordering lists.
*   **No Recursion Stack Overhead:** The iterative approach avoids the recursion depth limit issues that recursive reversal might face for very long lists in languages like Python.

### Cons:
*   **Tricky Pointer Management:** This is the main challenge. It requires careful thought and a disciplined approach to pointer manipulation. A single incorrect assignment can lead to losing parts of the list or creating infinite loops. Drawing diagrams is highly recommended during practice.
*   **Not a General Purpose Algorithm:** Specific to linked list manipulation.

## 7. Common Use Cases / Applications

*   **Reversing a Whole Linked List:** (e.g., LeetCode 206. Reverse Linked List). This is the base case.
*   **Reversing a Sub-list / Part of a Linked List:**
    *   Instead of reversing the entire list, you reverse only a segment between two given nodes. This involves identifying the nodes before and after the sub-list and re-attaching the reversed sub-list.
    *   (e.g., LeetCode 206. Reverse Linked List II)
*   **Reversing Every K-group of Nodes:**
    *   Similar to reversing a sub-list, but you apply the reversal repeatedly for groups of `k` nodes.
    *   (e.g., LeetCode 25. Reverse Nodes in k-Group)
*   **Checking if a Linked List is a Palindrome:**
    *   A common strategy is to find the middle of the list (using Fast & Slow pointers), reverse the second half of the list (in-place), and then compare the first half with the reversed second half.
    *   (e.g., LeetCode 234. Palindrome Linked List)
*   **Reordering Linked Lists:**
    *   Problems that require reordering nodes (e.g., `L0 -> L1 -> L2 -> L3 -> L4` becomes `L0 -> L4 -> L1 -> L3 -> L2`) often involve splitting the list, reversing one part, and then merging.

## 8. Related Concepts / Variations

*   **Linked Lists:** The core data structure.
*   **Two Pointers:** The `prev` and `curr` pointers in this pattern are a form of two-pointer usage.
*   **Fast & Slow Pointers:** Often used in conjunction with linked list reversal (e.g., finding the middle before reversing the second half for palindrome checks).
*   **Recursion:** Linked list reversal can also be implemented recursively. However, recursive solutions typically use O(N) space due to the recursion call stack, making the iterative in-place method more desirable for space efficiency.

## 9. LeetCode Problem Examples

*   [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) (Easy) - The foundational problem.
*   [206. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/) (Medium)
*   [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/) (Hard)
*   [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/) (Easy)
*   [143. Reorder List](https://leetcode.com/problems/reorder-list/) (Medium) - Involves splitting, reversing, and merging.

## 10. Further Reading / Resources

*   [GeeksforGeeks - Reverse a Linked List](https://www.geeksforgeeks.org/reverse-a-linked-list/) - Clear explanations and diagrams.
*   [Educative.io - In-place Reversal of a Linked List (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/xoy6Z2jZPyx) - Comprehensive coverage of the pattern and its variations.
*   [NeetCode.io - Linked List Reversal](https://neetcode.io/roadmap) - Look for linked list problems on his roadmap that use this pattern.
*   [YouTube - Nick White: Reverse Linked List](https://www.youtube.com/watch?v=D2vI2DNJcR8) - Good video walkthrough.
