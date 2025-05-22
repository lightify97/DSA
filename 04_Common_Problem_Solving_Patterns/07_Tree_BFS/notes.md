# Tree Breadth-First Search (Tree BFS) / Level-Order Traversal

## 1. Introduction / Concept

Imagine exploring a multi-story building. You wouldn't go to the top floor, then explore all rooms there, then return to the ground and go to the basement. Instead, you'd explore all rooms on the ground floor, then all rooms on the first floor, then all rooms on the second floor, and so on.

This "level-by-level" exploration is precisely what **Breadth-First Search (BFS)** does for tree (and graph) data structures. When applied to trees, it's most commonly called **Level-Order Traversal**. It systematically explores all nodes at the current depth (level) before moving on to any nodes at the next depth level.

BFS is particularly useful for problems where you need to find the shortest path (in terms of number of edges) from a source node, or when you need to process nodes level by level.

## 2. Core Principles / Theory

The core data structure that enables BFS is a **queue**. A queue operates on a First-In, First-Out (FIFO) principle, meaning the first element added is the first one to be removed.

### The General Algorithm for Tree BFS:

1.  **Initialization:**
    *   Create an empty queue.
    *   Add the starting node (usually the `root` of the tree) to the queue.
    *   *(Imagine placing the first floor's blueprint in your "to-do" box.)*

2.  **Iterative Processing:**
    *   While the queue is not empty:
        a.  **Dequeue a node:** Remove the node from the front of the queue. This is the node we are currently "visiting."
        b.  **Process the node:** Perform any required operation on the dequeued node (e.g., print its value, add it to a result list, check for a target value).
        c.  **Enqueue Children:** Add all of the dequeued node's unvisited children (typically left child first, then right child for binary trees) to the *back* of the queue. These children will be processed in a future iteration, after all nodes at the current level have been visited.
            *(Imagine taking a blueprint from the box, looking at its rooms, and then adding blueprints for all connecting rooms on the next floor into the box.)*

### Handling Levels (Level-by-Level Processing):

Many tree BFS problems require processing or collecting nodes *level by level*. To achieve this, you need to know how many nodes are at the current level before you start processing them.

*   Before starting to dequeue nodes for a particular level, record the `current_level_size` (which is `len(queue)` at that moment).
*   Use a `for` loop to dequeue *exactly* `current_level_size` nodes. Any new children enqueued during this inner loop belong to the *next* level.
*   After the inner `for` loop finishes, you have processed all nodes for the current level. You can then perform any level-specific aggregations (e.g., calculate average, store nodes for that level).

    *(Imagine: "Okay, there are 5 rooms on *this* floor. I'll process exactly these 5, and any new rooms I find will be for the *next* floor.")*

## 3. Operations / Methods

The core operations for Tree BFS revolve around queue management:

*   **`collections.deque`:** Python's `collections.deque` is the preferred queue implementation for BFS.
    *   `queue = deque()`: Initialization.
    *   `queue.append(node)`: Enqueue (add to the right end, O(1)).
    *   `node = queue.popleft()`: Dequeue (remove from the left end, O(1)).
*   **Node Structure:** A typical `TreeNode` class for binary trees:
    ```python
    class TreeNode:
        def __init__(self, val=0, left=None, right=None):
            self.val = val
            self.left = left
            self.right = right
    ```
*   **Conditional Child Enqueueing:** `if node.left: queue.append(node.left)` and `if node.right: queue.append(node.right)`.

## 4. Python Implementation Details / Considerations

*   **`collections.deque` vs `list`:** **Always use `collections.deque` for queues in Python.** While a `list` can simulate a queue using `append()` and `pop(0)`, `pop(0)` takes O(N) time because all subsequent elements must be shifted. `deque.popleft()` is O(1).
*   **Handling Empty Tree:** Always check `if not root:` at the beginning to handle empty trees gracefully.
*   **Level Tracking:** When you need level-by-level results, capture `len(queue)` at the start of the outer `while queue:` loop, then use an inner `for _ in range(level_size):` loop.

### Example: Level-Order Traversal of a Binary Tree (LeetCode 102)

This example demonstrates how to collect nodes level by level.

```python
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def level_order_traversal(root: TreeNode) -> list[list[int]]:
    result = []
    # Edge case: if the tree is empty, return an empty list of levels.
    if not root:
        return result

    # Initialize the queue with the root node.
    queue = deque()
    queue.append(root)

    # Continue as long as there are nodes to visit in the queue.
    while queue:
        # Get the number of nodes at the current level.
        # This is crucial for processing nodes level by level correctly.
        level_size = len(queue)
        
        # Create a list to store the values of nodes at the current level.
        current_level_nodes = []

        # Process all nodes that are currently at this level.
        for _ in range(level_size):
            # Dequeue the node from the front of the queue.
            node = queue.popleft() 
            
            # Add the node's value to the current level's list.
            current_level_nodes.append(node.val) 

            # Enqueue the left child if it exists. It belongs to the next level.
            if node.left:
                queue.append(node.left)
            # Enqueue the right child if it exists. It also belongs to the next level.
            if node.right:
                queue.append(node.right)
        
        # After processing all nodes for the current level, add their values
        # (as a list) to the overall result.
        result.append(current_level_nodes) 

    return result

# Example Tree Construction (conceptual, usually handled by LeetCode input):
# Tree structure:
#      3
#     / \
#    9  20
#      /  \
#     15   7

root = TreeNode(3)
root.left = TreeNode(9)
root.right = TreeNode(20)
root.right.left = TreeNode(15)
root.right.right = TreeNode(7)

# Edge Cases and Examples:
print(f"Level Order Traversal for standard tree: {level_order_traversal(root)}") 
# Expected Output: [[3], [9, 20], [15, 7]]

print(f"Level Order Traversal for empty tree: {level_order_traversal(None)}") 
# Expected Output: [] (Edge case: empty tree)

single_node_tree = TreeNode(10)
print(f"Level Order Traversal for single node tree: {level_order_traversal(single_node_tree)}") 
# Expected Output: [[10]] (Edge case: single node)

skewed_tree = TreeNode(1, None, TreeNode(2, None, TreeNode(3)))
print(f"Level Order Traversal for skewed tree: {level_order_traversal(skewed_tree)}") 
# Expected Output: [[1], [2], [3]]
```
*   **Edge Cases Handled:** Empty tree, single-node tree, skewed tree. The `if not root:` check and the `level_size` logic correctly handle various tree shapes.

## 5. Time and Space Complexity Analysis

Tree BFS is very efficient for traversing all nodes.

*   **Time Complexity:**
    *   **O(N)**: Where N is the number of nodes in the tree.
    *   **Justification:** Every node in the tree is processed exactly once: it is enqueued into the queue once, and dequeued from the queue once. Each enqueue/dequeue operation (using `collections.deque`) is O(1). Therefore, the total time complexity is linear with respect to the number of nodes.
*   **Space Complexity:**
    *   **O(W)**: Where W is the maximum width (maximum number of nodes at any single level) of the tree. This space is used by the queue to store nodes that are waiting to be processed.
    *   **Justification:** In the worst-case scenario (e.g., a complete binary tree where the last level contains roughly half of all nodes), the maximum width W can be approximately `N/2`. Therefore, the worst-case space complexity is **O(N)**. For a very skewed tree (like a linked list), the maximum width is O(1), leading to O(1) space. So, the space complexity is bounded by the width of the widest level.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Guaranteed Shortest Path (in unweighted graphs):** When extended to graphs, BFS is guaranteed to find the shortest path in terms of the *number of edges* from a source node to all other reachable nodes. This is because it explores layer by layer.
*   **Level-by-Level Processing:** Ideal for problems that inherently require processing nodes layer by layer or finding characteristics specific to each level (e.g., level averages, nodes at a specific depth, right/left view).
*   **No Recursion Stack Limit Issues:** Since BFS is typically implemented iteratively (using a queue), it avoids the recursion depth limit issues that recursive DFS solutions might encounter in languages like Python for very deep trees.

### Cons:
*   **Higher Space Usage (for wide trees):** Can consume more memory than DFS in the worst case (for wide trees), as it must store all nodes at the widest level in the queue simultaneously.
*   **Not Ideal for Deep Paths:** If the problem requires finding a path to a very deep node, DFS might find it faster if it happens to explore the correct path first. BFS would have to explore all shallower levels first.
*   **Less Intuitive for Path Reconstruction:** While it finds the shortest path length, reconstructing the actual path itself from a BFS traversal often requires storing parent pointers or performing a second traversal.

## 7. Common Use Cases / Applications

Tree BFS (Level-Order Traversal) is a fundamental traversal technique with many applications:

*   **Binary Tree Level Order Traversal:** (e.g., LeetCode 102).
*   **Finding the Minimum Depth of a Binary Tree:** The first time BFS finds a leaf node, its depth is the minimum depth.
*   **Connecting Nodes at the Same Level (Next Right Pointers):** You can use BFS to process nodes level by level and establish connections between them.
*   **Calculating Level Averages:** Sum up node values at each level and divide by the count of nodes.
*   **Right/Left Side View of a Binary Tree:** For each level, the first or last node added to `current_level_nodes` is the one you need.
*   **Zigzag Level Order Traversal:** A variation where levels are traversed alternately from left-to-right and right-to-left.
*   **Checking if a Tree is Complete:** BFS can be used to efficiently determine if a binary tree is complete.
*   **Used as the foundation for Breadth-First Search on Graphs:** The core logic extends directly.

## 8. Related Concepts / Variations

*   **Queues:** The fundamental data structure used. `collections.deque` is key in Python.
*   **Graph BFS:** The principles and implementation techniques for Tree BFS are directly transferable to BFS for general graphs, which is used for shortest paths in unweighted graphs, connectivity, and more.
*   **Tree DFS:** The alternative traversal method, which explores branches deeply before backtracking. DFS typically uses recursion (implicitly a stack) or an explicit stack.
*   **Tree Traversal Algorithms:** BFS is one of the primary ways to traverse trees, alongside Pre-order, In-order, and Post-order (which are DFS variants).

## 9. LeetCode Problem Examples

*   [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/) (Medium) - The most direct application.
*   [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) (Easy - can be solved with BFS)
*   [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/) (Easy) - BFS is generally preferred for minimum depth.
*   [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/) (Medium)
*   [107. Binary Tree Level Order Traversal II](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/) (Easy) - Level order from bottom to top.
*   [103. Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/) (Medium)
*   [637. Average of Levels in Binary Tree](https://leetcode.com/problems/average-of-levels-in-binary-tree/) (Easy)

## 10. Further Reading / Resources

*   [GeeksforGeeks - Level Order Tree Traversal](https://www.geeksforgeeks.org/level-order-tree-traversal/) - Good visual and code examples.
*   [Educative.io - Tree BFS Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/YQyPzQjZW8D) - Offers a very structured and problem-oriented approach to the pattern.
*   [NeetCode.io - BFS](https://neetcode.io/roadmap) - Look under "Trees" and "Graphs" for BFS videos.
*   [Khan Academy - Breadth-first search for trees](https://www.khanacademy.org/computing/computer-science/algorithms/breadth-first-search/a/bfs-for-trees) - Simple, intuitive explanation.
