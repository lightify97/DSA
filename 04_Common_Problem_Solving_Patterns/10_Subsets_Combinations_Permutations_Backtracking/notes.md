# Subsets, Combinations, Permutations (Backtracking Variations)

## 1. Introduction / Concept
These patterns deal with generating all possible arrangements or selections of elements from a given set or sequence, often subject to certain constraints. They are classic examples of problems solved using **Backtracking**, a general algorithmic technique for solving computational problems by trying to build a solution incrementally, one piece at a time, removing those solutions that fail to satisfy the constraints ("backtracking") and continuing to explore other possibilities.

## 2. Core Principles / Theory (Backtracking)
Backtracking is a recursive approach that builds a solution step-by-step. At each step, it explores all possible choices. If a choice leads to a valid partial solution, it continues recursively. If a choice leads to an invalid partial solution, or if it reaches a dead end, it "backtracks" (undoes the choice) and tries another option.

The typical structure of a backtracking algorithm:

```python
def backtrack(current_solution_state, other_parameters):
    # 1. Base Case:
    #    If current_solution_state is a complete/valid solution:
    #        Add it to results
    #        Return

    # 2. Recursive Step (Explore Choices):
    #    For each possible choice from the current state:
    #        a. Make the choice (add to current_solution_state)
    #        b. Recursively call backtrack(updated_solution_state, ...)
    #        c. Unmake the choice (backtrack: remove from current_solution_state) - clean up for next iteration
```

### Specific Variations:

*   **Subsets:** Generate all possible combinations of elements, where the order of elements within a subset doesn't matter. Often involves a choice: "include this element" or "don't include this element."
*   **Combinations:** Similar to subsets, but typically involves selecting a fixed number of elements (`k`) from a larger set (`n`). Order doesn't matter.
*   **Permutations:** Generate all possible ordered arrangements of elements. For `n` distinct elements, there are `n!` permutations.

## 3. Operations / Methods
*   **Recursive function calls:** The most common way to implement backtracking.
*   **List manipulations:** `append()` and `pop()` to add/remove elements from the `current_solution_state` (often a list).
*   **Visited tracking:** For permutations (or graphs), a `visited` array/set might be needed to avoid processing the same element multiple times in a single path.

## 4. Python Implementation Details / Considerations
*   Python's recursion is natural for backtracking.
*   `list.append()` and `list.pop()` are perfect for building and un-building solutions.
*   Handling duplicates often involves sorting the input array and skipping adjacent duplicates to avoid redundant computations (e.g., `if i > start and nums[i] == nums[i-1]: continue`).

**Example: Generating all Subsets of a set**

```python
def find_subsets(nums):
    result = []
    current_subset = []

    def backtrack(index):
        # Base case: we've considered all elements
        if index == len(nums):
            result.append(list(current_subset)) # Append a copy!
            return

        # Choice 1: Include the current element
        current_subset.append(nums[index])
        backtrack(index + 1)
        current_subset.pop() # Backtrack: remove the current element

        # Choice 2: Exclude the current element
        backtrack(index + 1)
    
    backtrack(0)
    return result

# Example usage:
# print(find_subsets([1, 2, 3]))
# Output: [[], [3], [2], [2, 3], [1], [1, 3], [1, 2], [1, 2, 3]] (order may vary)
```

**Example: Generating all Permutations of a list**

```python
def find_permutations(nums):
    result = []
    
    def backtrack(current_permutation):
        # Base case: a permutation is complete when its length matches the input
        if len(current_permutation) == len(nums):
            result.append(list(current_permutation))
            return

        for num in nums:
            if num not in current_permutation: # Check if element is already used in current path
                current_permutation.append(num)
                backtrack(current_permutation)
                current_permutation.pop() # Backtrack
    
    backtrack([])
    return result

# Example usage:
# print(find_permutations([1, 2, 3]))
# Output: [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]
```

## 5. Time and Space Complexity Analysis
The complexities are typically exponential or factorial due to the exhaustive search nature.

*   **Subsets:**
    *   **Time:** O(2^N * N), where 2^N is the number of subsets, and N is the time to copy each subset to the result.
    *   **Space:** O(N) for the recursion stack and temporary `current_subset`.
*   **Permutations:**
    *   **Time:** O(N! * N), where N! is the number of permutations, and N is the time to copy each permutation.
    *   **Space:** O(N) for the recursion stack and temporary `current_permutation`.
*   **Combinations:**
    *   **Time:** O(C(N, K) * K), where C(N, K) is "N choose K" (number of combinations), and K is time to copy each combination.
    *   **Space:** O(K) for recursion stack and temporary `current_combination`.

## 6. Pros and Cons / Trade-offs
**Pros:**
*   **Systematic Exhaustive Search:** Guarantees finding all valid solutions (or determining none exist).
*   **Flexible:** Can incorporate various constraints easily by adding checks in the "make choice" or base case conditions.

**Cons:**
*   **High Time Complexity:** Often exponential or factorial, making it unsuitable for large input sizes.
*   **Memory Usage:** Can consume significant memory due to recursion depth and storing intermediate results.

## 7. Common Use Cases / Applications
*   **Generating Subsets:** (e.g., LeetCode 78. Subsets, 90. Subsets II).
*   **Generating Permutations:** (e.g., LeetCode 46. Permutations, 47. Permutations II).
*   **Generating Combinations:** (e.g., LeetCode 77. Combinations, 39. Combination Sum).
*   **N-Queens Problem:** (e.g., LeetCode 51. N-Queens).
*   **Sudoku Solver:** (e.g., LeetCode 37. Sudoku Solver).
*   **Palindrome Partitioning.**
*   **Finding Paths in a Maze / Grid (if not using BFS).**

## 8. Related Concepts / Variations
*   **Recursion:** The core technique.
*   **Branch and Bound:** An optimization technique for backtracking that prunes branches that are guaranteed not to lead to an optimal solution.
*   **Dynamic Programming:** Sometimes, problems initially solved with backtracking can be optimized using DP if they exhibit overlapping subproblems and optimal substructure.

## 9. LeetCode Problem Examples
*   [78. Subsets](https://leetcode.com/problems/subsets/) (Medium)
*   [90. Subsets II](https://leetcode.com/problems/subsets-ii/) (Medium)
*   [46. Permutations](https://leetcode.com/problems/permutations/) (Medium)
*   [47. Permutations II](https://leetcode.com/problems/permutations-ii/) (Medium)
*   [77. Combinations](https://leetcode.com/problems/combinations/) (Medium)
*   [39. Combination Sum](https://leetcode.com/problems/combination-sum/) (Medium)
*   [51. N-Queens](https://leetcode.com/problems/n-queens/) (Hard)
*   [37. Sudoku Solver](https://leetcode.com/problems/sudoku-solver/) (Hard)

## 10. Further Reading / Resources
*   [GeeksforGeeks - Backtracking](https://www.geeksforgeeks.org/backtracking-algorithms/)
*   [Educative.io - Backtracking for Subsets/Permutations (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/xVlKz9l2Ngj)
