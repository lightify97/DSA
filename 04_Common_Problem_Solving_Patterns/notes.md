# Common Problem Solving Patterns in DSA

## Introduction

Problem-solving patterns are recurring approaches that can be applied to solve specific categories of algorithmic challenges. Recognizing these patterns is crucial for efficient problem-solving in technical interviews and competitive programming.

## Core Patterns Overview

### 1. Two Pointers

- **Concept**: Uses two pointers that move through an array or string in a coordinated way
- **When to use**: Problems involving sorted arrays, finding pairs, or subarrays with specific properties
- **Time Complexity**: Usually O(n) where n is the size of the input
- **Space Complexity**: Usually O(1) as only pointers are used
- **Example Problems**: Two Sum, Remove Duplicates, Container With Most Water

### 2. Sliding Window

- **Concept**: Maintains a "window" of elements and slides it through the data
- **When to use**: Problems involving contiguous subarrays/substrings with certain properties
- **Time Complexity**: Usually O(n) where n is the size of the input
- **Space Complexity**: Usually O(1) or O(k) where k is the window size
- **Example Problems**: Maximum Sum Subarray of Size K, Longest Substring Without Repeating Characters

### 3. Fast & Slow Pointers

- **Concept**: Two pointers moving at different speeds
- **When to use**: Cycle detection, finding middle elements, or linked list problems
- **Time Complexity**: Usually O(n) where n is the size of the input
- **Space Complexity**: Usually O(1)
- **Example Problems**: Linked List Cycle, Middle of the Linked List, Palindrome Linked List

### 4. Merge Intervals

- **Concept**: Sort and merge overlapping intervals
- **When to use**: Problems involving intervals, scheduling, or time ranges
- **Time Complexity**: Usually O(n log n) due to sorting
- **Space Complexity**: Usually O(n) for storing the result
- **Example Problems**: Merge Intervals, Insert Interval, Meeting Rooms

### 5. Cyclic Sort

- **Concept**: Places each element at its correct position in-place
- **When to use**: Problems involving arrays containing numbers in a given range
- **Time Complexity**: O(n) where n is the size of the input
- **Space Complexity**: O(1)
- **Example Problems**: Find Missing Number, Find All Duplicates in an Array

### 6. In-Place Reversal of Linked List

- **Concept**: Reverse parts or all of a linked list without using extra space
- **When to use**: Problems involving linked list manipulation
- **Time Complexity**: O(n) where n is the size of the linked list
- **Space Complexity**: O(1)
- **Example Problems**: Reverse Linked List, Reverse Nodes in k-Group

### 7. Tree BFS (Breadth-First Search)

- **Concept**: Explores a tree level by level
- **When to use**: Level-order traversal, finding shortest paths in unweighted graphs
- **Time Complexity**: O(n) where n is the number of nodes
- **Space Complexity**: O(n) for the queue
- **Example Problems**: Level Order Traversal, Binary Tree Right Side View

### 8. Tree DFS (Depth-First Search)

- **Concept**: Explores a tree branch by branch to its leaf before backtracking
- **When to use**: Problems requiring path exploration or recursive tree traversal
- **Time Complexity**: O(n) where n is the number of nodes
- **Space Complexity**: O(h) where h is the height of the tree (can be O(n) in worst case)
- **Example Problems**: Path Sum, Validate Binary Search Tree

### 9. Two Heaps

- **Concept**: Uses two heaps (usually min and max) to track elements
- **When to use**: Problems requiring tracking the median or partitioning elements
- **Time Complexity**: O(log n) for heap operations
- **Space Complexity**: O(n) for storing elements in heaps
- **Example Problems**: Find Median from Data Stream, Sliding Window Median

### 10. Subsets, Combinations & Permutations (Backtracking)

- **Concept**: Explores all possible combinations through recursion and backtracking
- **When to use**: Problems requiring enumeration of all possible solutions
- **Time Complexity**: Can be exponential, O(2^n) for subsets, O(n!) for permutations
- **Space Complexity**: Varies based on number of valid solutions
- **Example Problems**: Subsets, Combinations, Letter Combinations of a Phone Number

### 11. Modified Binary Search

- **Concept**: Adapts binary search for various scenarios beyond simple lookup
- **When to use**: Searching in sorted or partially sorted arrays with variations
- **Time Complexity**: O(log n)
- **Space Complexity**: O(1) iterative, O(log n) recursive
- **Example Problems**: Search in Rotated Sorted Array, Find Peak Element

### 12. Top K Elements

- **Concept**: Uses heap to find/track K elements with specific properties
- **When to use**: Finding top K frequent/largest/smallest elements
- **Time Complexity**: O(n log k) where n is input size and k is the parameter
- **Space Complexity**: O(k) for the heap
- **Example Problems**: Top K Frequent Elements, Kth Largest Element

### 13. K-Way Merge

- **Concept**: Merges K sorted lists into one sorted list
- **When to use**: Problems involving multiple sorted arrays/lists
- **Time Complexity**: O(N log k) where N is total elements and k is number of lists
- **Space Complexity**: O(k)
- **Example Problems**: Merge K Sorted Lists, K Pairs with Smallest Sums

### 14. Monotonic Stack/Queue

- **Concept**: Maintains a monotonically increasing/decreasing order of elements
- **When to use**: Problems involving finding next greater/smaller element
- **Time Complexity**: Usually O(n)
- **Space Complexity**: O(n) in worst case
- **Example Problems**: Next Greater Element, Largest Rectangle in Histogram

### 15. Bitwise XOR Tricks

- **Concept**: Uses XOR properties for efficient solutions
- **When to use**: Problems involving finding unique elements or bit manipulation
- **Time Complexity**: Usually O(n)
- **Space Complexity**: Usually O(1)
- **Example Problems**: Single Number, Missing Number (bit manipulation approach)

### 16. Topological Sort

- **Concept**: Linear ordering of vertices in a directed graph
- **When to use**: Problems involving dependencies or prerequisites
- **Time Complexity**: O(V+E) where V is vertices and E is edges
- **Space Complexity**: O(V+E)
- **Example Problems**: Course Schedule, Alien Dictionary

## Additional Pattern

### 17. Meet in the Middle

- **Concept**: Splits the input in half, solves each half separately, then combines results
- **When to use**: Problems with large search spaces that can be divided
- **Time Complexity**: Typically reduces exponential complexity by half (e.g., from O(2^n) to O(2^(n/2)))
- **Space Complexity**: Usually exponential in half the input size
- **Example Problems**: Subset Sum with large constraints, Partition Equal Subset Sum

## How to Approach Problems

1. **Identify the pattern**: Based on problem description and constraints
2. **Apply the pattern template**: Most patterns have standard implementation approaches
3. **Adapt the template**: Modify the standard approach to fit the specific problem
4. **Optimize if needed**: Consider edge cases and potential optimizations
5. **Test your solution**: Verify with examples and edge cases

## Common Mistakes to Avoid

- Applying the wrong pattern due to superficial problem similarities
- Overlooking edge cases specific to the problem
- Not considering time and space complexity constraints
- Overcomplicating solutions when a simpler pattern would suffice

## Visual Representations

For visual learners, consider drawing:

- Two pointers as arrows moving along an array
- Sliding window as a highlighted subsection of an array that expands/contracts
- Tree traversals with arrows showing the path of exploration
- Backtracking as a decision tree with branches

## Practice Strategy

1. Master one pattern at a time
2. Solve multiple problems of the same pattern
3. Identify pattern variations and how they affect the solution
4. Progress to more complex problems combining multiple patterns
