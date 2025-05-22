# Topological Sort Pattern

## 1. Introduction / Concept

Imagine you have a list of university courses, and some courses have prerequisites (e.g., you must take "Calculus I" before "Calculus II"). Or you have a set of tasks in a project, and some tasks must be completed before others. You want to find a valid order in which to take all courses or complete all tasks.

This is precisely what **Topological Sort** (or Topological Ordering) does. It's a linear ordering of vertices (nodes) in a **Directed Acyclic Graph (DAG)** such that for every directed edge `u -> v`, vertex `u` comes before `v` in the ordering.

*   **Directed:** Edges have a specific direction (e.g., A -> B means A must come before B).
*   **Acyclic:** The graph contains no cycles (e.g., A -> B -> C -> A is a cycle, which would make a linear ordering impossible because A depends on C, C depends on B, and B depends on A).

If a graph contains a cycle, a topological sort is impossible. This makes topological sort useful not just for ordering, but also for detecting cycles in dependency graphs.

## 2. Core Principles / Theory

The fundamental idea is that any task that has no prerequisites (or whose prerequisites have all been met) can be performed first. As tasks are completed, they unlock other tasks whose prerequisites are now met.

There are two primary algorithms to perform a topological sort:

### 2.1. Kahn's Algorithm (BFS-based)

This algorithm uses the concept of "in-degree" (the number of incoming edges to a vertex).

1.  **Calculate In-degrees:** For every vertex (node) in the graph, calculate its in-degree. Store these in a dictionary or array.
    *   *(Imagine counting how many arrows point TO each task.)*

2.  **Initialize Queue:** Create a queue and add all vertices that have an in-degree of 0. These are the tasks that have no prerequisites and can be started immediately.
    *   *(These are your starting tasks.)*

3.  **Process Vertices (BFS-like):**
    *   While the queue is not empty:
        a.  **Dequeue a vertex `u`:** Remove a vertex from the front of the queue. This `u` is the next vertex in our topological sort. Add `u` to the result list.
        b.  **Decrement Neighbors' In-degrees:** For each neighbor `v` that `u` points to (i.e., for every edge `u -> v`):
            *   Decrement the in-degree of `v`. This signifies that one of `v`'s prerequisites (`u`) has now been met.
        c.  **Enqueue New Zero In-degree Vertices:** If, after decrementing, `v`'s in-degree becomes 0, it means all of `v`'s prerequisites are now met. Add `v` to the queue.
            *(As you complete a task, you "unlock" other tasks. If an unlocked task has no more unmet prerequisites, you can add it to your list of ready tasks.)*

4.  **Cycle Detection:**
    *   If the total number of vertices added to the `topological_sort_result` list is equal to the total number of vertices in the graph, then a valid topological sort was found.
    *   If `len(topological_sort_result) < total_vertices`, it means there are remaining vertices that could not be processed (their in-degrees never became 0). This indicates that the graph contains a **cycle**, and thus no topological sort is possible.
    *   *(If you run out of tasks to do, but haven't finished all tasks, it means you're stuck in a circular dependency.)*

### 2.2. DFS-based Algorithm

This algorithm uses recursion (implicitly a stack) and tracks the state of nodes during traversal.

1.  **Initialization:** Keep track of three states for each node:
    *   `UNVISITED`: Not yet explored.
    *   `VISITING` (or `GRAY`): Currently in the recursion stack, being explored.
    *   `VISITED` (or `BLACK`): Finished processing all its descendants.
2.  **DFS Traversal:**
    *   Perform a Depth-First Search (DFS) starting from an unvisited node.
    *   When exploring `u`:
        *   Mark `u` as `VISITING`.
        *   For each neighbor `v` of `u`:
            *   If `v` is `VISITING`, a **cycle is detected** (a back-edge).
            *   If `v` is `UNVISITED`, recursively call DFS on `v`. If the recursive call returns `True` (meaning a cycle was found deeper), propagate `True`.
        *   After visiting all neighbors and recursive calls return, mark `u` as `VISITED`.
        *   **Crucially:** Add `u` to the **front** of the topological sort result list (or push it onto a stack and then reverse the stack at the end).
            *(This ensures that a node is added to the result only after all its dependencies are processed. Because DFS goes deep, it processes dependents first, so adding to the *front* ensures the correct order.)*

3.  **Cycle Detection:** A cycle is detected if, during DFS, you encounter a node that is currently in the `VISITING` state (i.e., it's an ancestor in the current recursion path).

## 3. Operations / Methods

*   **Graph Representation:** An **Adjacency List** is almost always preferred for graph problems. A `collections.defaultdict(list)` in Python is excellent for this.
    *   `adj = defaultdict(list)`: `adj[u]` stores a list of neighbors `v` that `u` points to.
*   **In-degree Array/Dictionary:** `in_degree = [0] * num_vertices` or `in_degree = {vertex: 0 for vertex in all_vertices}`.
*   **Queue (`collections.deque`):** For Kahn's (BFS-based) algorithm.
    *   `q = deque()`
    *   `q.append(v)`
    *   `q.popleft()`
*   **Stack (Python `list`):** For DFS-based topological sort (explicit stack or implicit recursion stack).
*   **Traversal Logic:** The core BFS or DFS loops and recursive calls.

## 4. Python Implementation Details / Considerations

*   **`collections.defaultdict`:** Simplifies building the adjacency list, as you don't need to check if a key exists before appending.
*   **`collections.deque`:** Essential for efficient queue operations in Kahn's algorithm.
*   **`sys.setrecursionlimit()`:** If using the recursive DFS-based topological sort for potentially deep graphs, remember to increase Python's recursion limit.
*   **Representing Vertices:** Vertices can be integers (0 to N-1) or strings/objects. Adapt graph representation (list vs. dict for in-degree) accordingly.

### Example: Kahn's Algorithm (BFS-based Topological Sort - LeetCode 210: Course Schedule II)

This problem asks for a possible course order given prerequisites.

```python
from collections import deque, defaultdict

def find_order(num_courses: int, prerequisites: list[list[int]]) -> list[int]:
    # Step 1: Build the Adjacency List and Calculate In-Degrees.
    # adj: Represents the graph. adj[u] will list all courses that have 'u' as a prerequisite.
    #      (i.e., if you take 'u', you can then take these courses).
    #      Edge u -> v means u is a prerequisite for v.
    adj = defaultdict(list)
    
    # in_degree: in_degree[i] will store the number of prerequisites for course 'i'.
    in_degree = [0] * num_courses

    for course, prereq in prerequisites:
        adj[prereq].append(course) # Add edge: prereq -> course
        in_degree[course] += 1     # Increment in-degree of 'course'

    # Step 2: Initialize the queue with all courses that have 0 prerequisites.
    # These are the courses that can be taken first.
    queue = deque()
    for i in range(num_courses):
        if in_degree[i] == 0:
            queue.append(i)

    # List to store the topological order of courses.
    topological_order = []

    # Step 3: Process courses using BFS.
    while queue:
        current_course = queue.popleft() # Dequeue a course with no remaining prerequisites
        topological_order.append(current_course) # Add it to the result

        # For each course that has 'current_course' as a prerequisite:
        for neighbor_course in adj[current_course]:
            in_degree[neighbor_course] -= 1 # Decrement its prerequisite count
            
            # If the neighbor_course now has 0 prerequisites, add it to the queue.
            if in_degree[neighbor_course] == 0:
                queue.append(neighbor_course)
    
    # Step 4: Check for cycles.
    # If the length of the topological order is equal to the total number of courses,
    # it means all courses were successfully ordered (no cycles).
    if len(topological_order) == num_courses:
        return topological_order
    else:
        # If not all courses were included, it implies a cycle exists in the prerequisites,
        # making a valid course order impossible.
        return [] # Return empty list for impossible order

# Edge Cases and Examples:
# Example 1: Standard case
# Courses: 0, 1, 2, 3. Prerequisites: [[1,0],[2,0],[3,1],[3,2]]
# (To take 1, must take 0. To take 2, must take 0. To take 3, must take 1 AND 2)
# Graph: 0 -> 1, 0 -> 2, 1 -> 3, 2 -> 3
print(f"Course order for 4 courses, [[1,0],[2,0],[3,1],[3,2]]: {find_order(4, [[1,0],[2,0],[3,1],[3,2]])}")
# Possible Output: [0, 1, 2, 3] or [0, 2, 1, 3] (topological sort is not unique)

# Example 2: No prerequisites
print(f"Course order for 2 courses, []: {find_order(2, [])}") # Output: [0, 1] or [1, 0]

# Example 3: Cycle detected
# Graph: 0 -> 1, 1 -> 0 (forms a cycle)
print(f"Course order for 2 courses, [[1,0],[0,1]]: {find_order(2, [[1,0],[0,1]])}") # Output: [] (Cycle)

# Example 4: Complex graph
# Graph: 5 -> 2, 5 -> 0, 4 -> 0, 4 -> 1, 2 -> 3, 3 -> 1
print(f"Course order for 6 courses, [[2,5],[0,5],[0,4],[1,4],[3,2],[1,3]]: {find_order(6, [[2,5],[0,5],[0,4],[1,4],[3,2],[1,3]])}")
# Possible Output: [4, 5, 0, 2, 3, 1] (or other valid orderings)
```
*   **Edge Cases Handled:** No prerequisites, simple cycle, graph with disconnected components, various valid orderings.

## 5. Time and Space Complexity Analysis

Topological Sort algorithms are highly efficient, performing in linear time relative to the graph's size.

*   **Time Complexity:**
    *   **O(V + E)**: Where V is the number of vertices (courses) and E is the number of edges (prerequisites) in the graph.
    *   **Justification (Kahn's Algorithm):**
        *   Building the adjacency list and computing initial in-degrees takes O(V + E) time (iterating through all vertices and all edges).
        *   Initializing the queue with zero in-degree vertices takes O(V) time.
        *   The `while` loop: Each vertex is enqueued and dequeued exactly once (O(V)). When a vertex is dequeued, its outgoing edges are traversed. Each edge is processed exactly once (in-degree is decremented) (O(E)).
        *   Therefore, the total time is proportional to the sum of vertices and edges.
*   **Space Complexity:**
    *   **O(V + E)**:
    *   **Justification:**
        *   The adjacency list stores the graph, requiring O(V + E) space.
        *   The `in_degree` array/dictionary requires O(V) space.
        *   The queue (in Kahn's algorithm) or recursion stack (in DFS algorithm) can, in the worst case, store all vertices, requiring O(V) space.
        *   The `topological_order` result list requires O(V) space.
        *   Combined, the space complexity is dominated by O(V + E).

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Dependency Resolution:** It is the go-to algorithm for any problem that involves ordering tasks or items with dependencies.
*   **Cycle Detection:** Provides a straightforward way to detect if a set of dependencies contains a circular dependency (a deadlock). If a topological sort cannot be completed, a cycle exists.
*   **Efficient:** Achieves optimal linear time complexity, making it suitable for large dependency graphs.
*   **Provides Valid Ordering:** Guarantees to find *a* valid ordering if one exists.

### Cons:
*   **Requires Directed Acyclic Graph (DAG):** This is a strict requirement. If the input graph has cycles, topological sort is impossible, and the algorithms will either fail to produce a complete ordering or signal the presence of a cycle.
*   **Not Unique:** For a given DAG, there can be multiple valid topological sort orderings. The algorithm will return just one of them. If the problem requires a specific valid ordering, additional criteria must be applied (e.g., lexicographical order).
*   **Not Applicable to Undirected Graphs:** By definition, topological sort only applies to directed graphs.

## 7. Common Use Cases / Applications

Topological Sort is a foundational algorithm in many real-world systems:

*   **Course Scheduling:** Ordering courses based on prerequisites (e.g., LeetCode Course Schedule problems).
*   **Build Order:** Determining the order in which to compile or build software modules, where modules have dependencies.
*   **Task Scheduling:** Ordering jobs or processes in an operating system or workflow management system based on prerequisites.
*   **Dependency Resolution in Package Managers:** When you install software packages, `apt`, `npm`, `pip`, etc., use topological sort to determine the correct installation order of dependencies.
*   **Data Serialization:** Ordering objects before serialization to ensure dependencies are resolved.
*   **Spreadsheet Recalculation:** Ordering cell recalculations based on cell dependencies.
*   **Evaluating Formulas in a Compiler:** Determining the order of operations in expressions.
*   **Detecting Deadlocks:** In resource allocation graphs, a cycle indicates a potential deadlock.
*   **Pipeline Processing:** Ordering stages in a data processing pipeline.

## 8. Related Concepts / Variations

*   **Graphs:** The fundamental data structure. A solid understanding of graph representations (adjacency list) is crucial.
*   **Directed Acyclic Graphs (DAGs):** The specific type of graph for which topological sort is applicable.
*   **BFS (Breadth-First Search):** Kahn's algorithm is based on BFS.
*   **DFS (Depth-First Search):** The alternative algorithm for topological sort is based on DFS. DFS is also used for general cycle detection in directed graphs.
*   **In-degree / Out-degree:** Key properties of graph vertices used in Kahn's algorithm.
*   **Strongly Connected Components (SCCs):** In a directed graph, if SCCs have more than one vertex, a topological sort is not possible. Contracting each SCC into a single vertex results in a DAG that can be topologically sorted.

## 9. LeetCode Problem Examples

*   [207. Course Schedule](https://leetcode.com/problems/course-schedule/) (Medium) - Checks if a topological sort is possible (i.e., no cycle).
*   [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) (Medium) - Returns a topological sort order if possible.
*   [329. Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/) (Hard) - Can be solved as a topological sort on a DAG implicitly formed by valid moves in the matrix, combined with dynamic programming.
*   [1136. Parallel Courses](https://leetcode.com/problems/parallel-courses/) (Medium - often premium) - Find the minimum number of semesters to take all courses.
*   [269. Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) (Hard - often premium) - Involves building a graph from character dependencies in a list of words and then performing a topological sort.
*   [2115. Find All Possible Recipes from a Given Supply](https://leetcode.com/problems/find-all-possible-recipes-from-a-given-supply/) (Medium) - Another dependency graph problem solvable with topological sort.

## 10. Further Reading / Resources

*   [GeeksforGeeks - Topological Sort](https://www.geeksforgeeks.org/topological-sorting/) - Provides comprehensive explanations of both Kahn's and DFS-based algorithms.
*   [Educative.io - Topological Sort Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/R1X5JDj23K6) - Offers a structured approach with clear visuals and problem examples.
*   [NeetCode.io - Graphs](https://neetcode.io/roadmap) - Look for graph problems related to course schedule and topological sort for video explanations.
*   [Wikipedia - Topological sorting](https://en.wikipedia.org/wiki/Topological_sorting) - Formal definition and algorithms.
*   [YouTube - William Fiset: Topological Sort (Kahn's Algorithm & DFS)](https://www.youtube.com/watch?v=eL-KzG5_X8U) - Detailed video explanation of both algorithms.
