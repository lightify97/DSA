# Tree Depth-First Search (Tree DFS)

## 1. Introduction / Concept

Imagine you are in a maze. Instead of exploring every junction one by one (like BFS), you pick a path, go as deep as possible down that path until you hit a dead end, and only then do you backtrack to the last junction and try another path.

This "go deep first" exploration is what **Depth-First Search (DFS)** does for tree (and graph) data structures. It explores as far as possible along each branch before *backtracking* (returning to a previous node) to try another branch. It fully explores one path from the root to a leaf before moving to an adjacent path.

DFS is particularly useful for problems where you need to explore entire paths, find specific paths, or solve problems related to parent-child or ancestor-descendant relationships.

## 2. Core Principles / Theory

The core data structure that implicitly (through recursion) or explicitly enables DFS is a **stack**. A stack operates on a Last-In, First-Out (LIFO) principle, meaning the last element added is the first one to be removed.

### The General Algorithm for Tree DFS (Recursive Approach - Most Common):

The elegance of recursive DFS for trees comes from its natural mapping to the tree's recursive definition.

1.  **Base Case:** If the current node is `None` (or a leaf node, depending on the problem), return.
2.  **Process Node:** Perform any required operation on the current node (e.g., print its value, check a condition). The timing of this processing defines the traversal type:
    *   **Pre-order Traversal (Root, Left, Right):** Process the current node *before* visiting its children.
        1.  Visit current node.
        2.  Recursively call DFS on the left child.
        3.  Recursively call DFS on the right child.
        *   *Applications:* Copying a tree, creating a prefix expression tree, exploring all paths from root.
    *   **In-order Traversal (Left, Root, Right):** Process the current node *between* visiting its left and right children.
        1.  Recursively call DFS on the left child.
        2.  Visit current node.
        3.  Recursively call DFS on the right child.
        *   *Applications:* Retrieving elements of a Binary Search Tree (BST) in sorted order.
    *   **Post-order Traversal (Left, Right, Root):** Process the current node *after* visiting both its children.
        1.  Recursively call DFS on the left child.
        2.  Recursively call DFS on the right child.
        3.  Visit current node.
        *   *Applications:* Deleting a tree (to avoid dangling pointers), evaluating postfix expression trees, calculating tree height/size bottom-up.

### Iterative DFS using an Explicit Stack:

While recursion is common, DFS can also be implemented iteratively using a `list` as a stack.

1.  **Initialization:** Create an empty stack. Push the root node onto the stack.
2.  **Iterative Processing:**
    *   While the stack is not empty:
        a.  **Pop a node:** Remove the node from the top of the stack.
        b.  **Process the node:** (Often done here for pre-order or as part of a more complex state tracking for in/post order).
        c.  **Push Children:** Push its children onto the stack. For pre-order, push right child then left child so that the left child is processed first (LIFO order).
            *(Imagine visiting a room, putting its connected unvisited rooms onto a "to-do" pile, and then taking the top one to visit next.)*

## 3. Operations / Methods

The core operations for Tree DFS depend on whether you use recursion or an explicit stack:

*   **Recursive Function Calls:** `dfs(node.left)`, `dfs(node.right)`.
*   **Stack Operations (for iterative DFS):** Python `list` used as a stack.
    *   `stack = []`
    *   `stack.append(node)` (push to top, O(1))
    *   `node = stack.pop()` (pop from top, O(1))
*   **Node Structure:** A typical `TreeNode` class for binary trees:
    ```python
    class TreeNode:
        def __init__(self, val=0, left=None, right=None):
            self.val = val
            self.left = left
            self.right = right
    ```

## 4. Python Implementation Details / Considerations

*   **Recursion Limit:** Python has a default recursion depth limit (typically 1000 or 3000). For very deep trees, a recursive DFS might hit this limit, resulting in a `RecursionError`. You can increase it with `sys.setrecursionlimit(new_limit)`, but a better solution for extremely deep trees or graphs is often an iterative DFS or BFS.
*   **Handling Empty Tree/Node:** Always check `if not node:` (or `if root is None:`) as the base case for recursive functions.
*   **Auxiliary Variables:** Recursive DFS solutions might need to pass accumulator variables (like current path sum) down the recursion stack, or update global/ nonlocal variables.

### Example: In-Order Traversal of a Binary Tree (Recursive - LeetCode 94)

This example demonstrates the clean syntax of recursive DFS for in-order traversal.

```python
import sys

# Optional: Increase recursion limit for very deep trees
# sys.setrecursionlimit(2000) 

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def inorder_traversal(root: TreeNode) -> list[int]:
    result = [] # List to store the traversal result

    # Define the recursive helper function (often nested for LeetCode solutions)
    def _inorder(node: TreeNode):
        # Base case: if the node is None, we've reached the end of a branch.
        if not node:
            return

        # 1. Recursively traverse the left subtree
        _inorder(node.left)
        
        # 2. Visit the current node (add its value to the result)
        result.append(node.val)
        
        # 3. Recursively traverse the right subtree
        _inorder(node.right)
    
    # Start the traversal from the root
    _inorder(root)
    return result

# Example Tree Construction (conceptual):
# Tree structure:
#       4
#      / \
#     2   5
#    / \
#   1   3

root = TreeNode(4)
root.left = TreeNode(2, TreeNode(1), TreeNode(3))
root.right = TreeNode(5)

# Edge Cases and Examples:
print(f"In-Order Traversal for standard tree: {inorder_traversal(root)}") 
# Expected Output: [1, 2, 3, 4, 5] (Note: For a BST, in-order gives sorted elements)

print(f"In-Order Traversal for empty tree: {inorder_traversal(None)}") 
# Expected Output: [] (Edge case: empty tree)

single_node_tree = TreeNode(10)
print(f"In-Order Traversal for single node tree: {inorder_traversal(single_node_tree)}") 
# Expected Output: [10] (Edge case: single node)

skewed_tree = TreeNode(1, None, TreeNode(2, None, TreeNode(3)))
print(f"In-Order Traversal for skewed tree (right): {inorder_traversal(skewed_tree)}") 
# Expected Output: [1, 2, 3]

skewed_tree_left = TreeNode(3, TreeNode(2, TreeNode(1)))
print(f"In-Order Traversal for skewed tree (left): {inorder_traversal(skewed_tree_left)}")
# Expected Output: [1, 2, 3]
```
*   **Edge Cases Handled:** Empty tree, single-node tree, skewed trees. The base case `if not node:` handles these correctly.

## 5. Time and Space Complexity Analysis

Tree DFS is very efficient for traversing all nodes.

*   **Time Complexity:**
    *   **O(N)**: Where N is the number of nodes in the tree.
    *   **Justification:** Every node in the tree is visited and processed exactly once. For each node, a constant amount of work is done (e.g., recursive calls, appending to a list). Therefore, the total time complexity is linear with respect to the number of nodes.
*   **Space Complexity:**
    *   **O(H)**: Where H is the height of the tree. This space is used by the recursion call stack (for recursive DFS) or the explicit stack (for iterative DFS) to keep track of the path from the root to the current node.
    *   **Justification:** In the worst-case scenario (a skewed tree, resembling a linked list), the height H can be equal to N. Therefore, the worst-case space complexity is **O(N)**. For a balanced tree, the height H is O(log N), so space complexity would be O(log N).

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Simple Implementation (Recursive):** Recursive solutions for DFS are often very elegant, concise, and conceptually easy to map to the tree's structure.
*   **Path-Oriented:** Ideal for problems that involve exploring entire paths from the root to a leaf, or analyzing relationships between ancestors and descendants (e.g., path sum, checking for valid BST, finding lowest common ancestor).
*   **Lower Space Usage (for balanced trees):** For balanced trees, DFS uses O(log N) space, which is more memory-efficient than BFS's O(N) worst-case space.
*   **Faster for Deep/Specific Paths:** If the target node or solution is very deep in a tree and happens to be on the first path explored, DFS might find it faster than BFS.

### Cons:
*   **Recursion Depth Limit:** In Python, recursive DFS can hit the default recursion limit for very deep trees, leading to runtime errors. This necessitates increasing the limit or implementing iterative DFS.
*   **Not Guaranteed Shortest Path (in unweighted graphs):** Unlike BFS, DFS does not guarantee finding the shortest path in terms of the number of edges in unweighted graphs. It will find *a* path, but potentially a very long one.
*   **Order of Processing:** The order of processing depends on the children's order (left/right) and the DFS variant (pre/in/post-order), which needs to be carefully chosen for the specific problem.

## 7. Common Use Cases / Applications

DFS is a versatile traversal technique crucial for many tree and graph problems:

*   **Tree Traversal (Pre-order, In-order, Post-order):** (e.g., LeetCode 94, 144, 145 for binary trees).
*   **Finding the Maximum Depth/Height of a Binary Tree:** DFS naturally explores depth, so it's a common approach.
*   **Checking for Tree Properties:**
    *   Symmetric Tree (e.g., LeetCode 101)
    *   Same Tree (e.g., LeetCode 100)
    *   Balanced Binary Tree (e.g., LeetCode 110)
*   **Path Sum Problems:** Finding if a path exists with a given sum, or finding all such paths.
*   **Lowest Common Ancestor (LCA):** Finding the lowest node that is an ancestor of two given nodes.
*   **Validating a Binary Search Tree (BST):** In-order traversal of a BST should yield sorted elements.
*   **Tree Diameter:** Finding the longest path between any two nodes in a tree.
*   **Used as the foundation for Depth-First Search on Graphs:** The core logic extends directly for connectivity, cycle detection, and topological sort.

## 8. Related Concepts / Variations

*   **Recursion:** The primary mechanism for implementing DFS. Understanding the call stack is vital.
*   **Stacks:** Explicit stacks can be used to implement iterative DFS, useful for avoiding recursion limits.
*   **Graph DFS:** The principles are directly transferable to DFS for general graphs, which is used for connectivity, cycle detection, topological sort, strongly connected components, etc.
*   **Backtracking:** Many backtracking algorithms are essentially a form of DFS on a state-space tree.
*   **Tree Traversal Algorithms:** DFS is one of the primary ways to traverse trees, alongside Level-Order (BFS).

## 9. LeetCode Problem Examples

*   [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) (Easy) - Can be solved with DFS (recursive).
*   [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/) (Easy)
*   [100. Same Tree](https://leetcode.com/problems/same-tree/) (Easy)
*   [112. Path Sum](https://leetcode.com/problems/path-sum/) (Easy) - Check if a root-to-leaf path sum exists.
*   [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) (Easy)
*   [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) (Medium)
*   [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/) (Easy)

## 10. Further Reading / Resources

*   [GeeksforGeeks - Tree Traversal](https://www.geeksforgeeks.org/tree-traversals-inorder-preorder-and-postorder/) - Good visual and code examples.
*   [Educative.io - Tree DFS Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/xVlKz9l2Ngj) - Provides a structured approach to solving problems with DFS.
*   [NeetCode.io - DFS](https://neetcode.io/roadmap) - Look under "Trees" and "Graphs" for DFS videos.
*   [Khan Academy - Depth-first search](https://www.khanacademy.org/computing/computer-science/algorithms/depth-first-search/a/depth-first-search) - Simple, intuitive explanation.
