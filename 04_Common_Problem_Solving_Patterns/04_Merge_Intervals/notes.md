# Merge Intervals

## 1. Introduction / Concept

Imagine you have a list of meeting times, where each meeting has a start and an end time. If some meetings overlap, you might want to consolidate them into a single, longer time slot. Or, you might want to find out where all the overlapping times are.

The **Merge Intervals pattern** is a common technique used to solve problems that involve a list of intervals, where each interval is typically represented by a start and an end point (e.g., `[start, end]`). The primary goal is often to **merge overlapping intervals** into a single, consolidated interval, or to perform other operations like finding intersections or busy/free time slots based on their relationships.

This pattern is highly effective because it provides a systematic and efficient way to process interval data, particularly when intervals are sorted.

## 2. Core Principles / Theory

The fundamental principle for almost all interval problems is to **sort the intervals based on their start times**. This sorting step is crucial because it ensures that when you iterate through the intervals, you are always considering intervals in chronological order. This simplifies the logic for checking overlaps.

Once sorted, you can iterate through the intervals, comparing each with the one that precedes it (or the currently merged interval) to determine if they overlap and how to combine or process them.

### Key Concepts:

1.  **Sorting:** The very first step is to sort the input list of intervals. The primary sorting key is the `start` time of each interval. If two intervals have the same `start` time, you can sort them by their `end` times (though this tie-breaking is often not strictly necessary for basic merging).
    *   *(Imagine a timeline where intervals are placed. Sorting them by their start points makes it easy to move chronologically.)*

2.  **Overlap Condition:** Two intervals `[a, b]` and `[c, d]` overlap if:
    *   The start of one is before the end of the other, AND the start of the other is before the end of the first.
    *   More simply, after sorting by start time (`a <= c` is implicitly true for adjacent intervals):
        *   They overlap if `c <= b`. (The start of the second interval is less than or equal to the end of the first interval).
        *   *(Imagine `[1, 5]` and `[3, 7]`. Here, `3 <= 5`, so they overlap.)*
        *   *(Imagine `[1, 5]` and `[6, 8]`. Here, `6 > 5`, so they do NOT overlap.)*

3.  **Merging Logic:** If two intervals, say the `last_merged_interval` `[current_start, current_end]` and the `next_interval` `[next_start, next_end]`, are found to overlap:
    *   The new merged interval will start at `current_start`.
    *   The new merged interval will end at `max(current_end, next_end)`. You take the maximum of the end times because the merged interval must encompass both original intervals.
    *   *(Imagine `[1, 5]` and `[3, 7]`. They merge to `[1, max(5, 7)]` which is `[1, 7]`.)*

4.  **Non-Overlapping Logic:** If two intervals do not overlap:
    *   The `next_interval` cannot be merged with the `last_merged_interval`.
    *   The `last_merged_interval` is finalized and added to the result list.
    *   The `next_interval` then becomes the new `last_merged_interval` (or the start of a new merged segment).

## 3. Operations / Methods

The core "operations" involve sorting and then a linear scan with comparison and updates:

*   **Sorting:** `intervals.sort(key=lambda x: x[0])` in Python.
*   **Iteration:** A `for` loop (or `while` loop) through the sorted intervals.
*   **Accessing Last Merged Interval:** `merged_intervals[-1]` is used to get the most recently merged interval for comparison.
*   **Comparison:** `if current_interval[0] <= last_merged_interval[1]:` (checking for overlap).
*   **Updating End Point:** `last_merged_interval[1] = max(last_merged_interval[1], current_interval[1])`.
*   **Appending New Interval:** `merged_intervals.append(current_interval)`.

## 4. Python Implementation Details / Considerations

*   **Representation of Intervals:** Intervals are typically represented as lists `[start, end]` or tuples `(start, end)` in Python. Lists are mutable, which can be convenient for modifying the end point of the `last_merged_interval` directly. Tuples are immutable, so you'd have to create new ones.
*   **`list.sort()` with `lambda`:** Python's built-in `sort()` method or `sorted()` function combined with a `lambda` function for the `key` argument is the most Pythonic way to sort the intervals efficiently.
*   **Result List:** A new list is used to store the merged intervals.
*   **Edge Cases:**
    *   **Empty input list:** Return an empty list.
    *   **Single interval:** No merging needed, return the interval itself.
    *   **Non-overlapping intervals:** Each interval ends up as its own merged interval.
    *   **Completely contained intervals:** E.g., `[[1, 10], [2, 5]]` should merge to `[[1, 10]]`. The `max(last_merged_interval[1], current_interval[1])` handles this correctly.

### Example: Merge Overlapping Intervals (LeetCode 56)

```python
def merge_intervals(intervals: list[list[int]]) -> list[list[int]]:
    # Edge case: If the input list is empty, there's nothing to merge.
    if not intervals:
        return []

    # Step 1: Sort the intervals by their start times.
    # This is crucial for the linear scan to work correctly.
    # If start times are equal, the order doesn't strictly matter for basic merging,
    # but Python's sort is stable and will maintain relative order for equal keys.
    intervals.sort(key=lambda x: x[0])

    merged_intervals = []
    # Step 2: Add the first interval to our result list.
    # This will be the first interval we try to merge others into.
    merged_intervals.append(intervals[0])

    # Step 3: Iterate through the rest of the sorted intervals.
    for i in range(1, len(intervals)):
        current_interval = intervals[i]
        
        # Get the last interval that was added to our 'merged_intervals' list.
        # This is the interval we will try to merge the current_interval with.
        last_merged = merged_intervals[-1]

        # Check for overlap:
        # An overlap occurs if the start of the current_interval is less than or equal to
        # the end of the last_merged interval.
        if current_interval[0] <= last_merged[1]:
            # Overlap found! Merge the intervals.
            # The new end time of the 'last_merged' interval will be the maximum
            # of its current end time and the current_interval's end time.
            last_merged[1] = max(last_merged[1], current_interval[1])
        else:
            # No overlap: The current_interval does not overlap with the last_merged interval.
            # So, the last_merged interval is final, and the current_interval starts a new merged segment.
            merged_intervals.append(current_interval)
            
    return merged_intervals

# Edge Cases and Examples:
print(f"Merge [[1,3],[2,6],[8,10],[15,18]]: {merge_intervals([[1,3],[2,6],[8,10],[15,18]])}") # Output: [[1,6],[8,10],[15,18]]
print(f"Merge [[1,4],[4,5]]: {merge_intervals([[1,4],[4,5]])}")       # Output: [[1,5]] (Adjacent intervals touch and merge)
print(f"Merge [[1,4],[0,4]]: {merge_intervals([[1,4],[0,4]])}")       # Output: [[0,4]] (Interval starting earlier gets merged correctly)
print(f"Merge [[1,5],[2,3]]: {merge_intervals([[1,5],[2,3]])}")       # Output: [[1,5]] (Contained interval merges correctly)
print(f"Merge [[1,2],[3,4],[5,6]]: {merge_intervals([[1,2],[3,4],[5,6]])}") # Output: [[1,2],[3,4],[5,6]] (No overlaps)
print(f"Merge []: {merge_intervals([])}")                             # Output: [] (Empty input)
print(f"Merge [[1,5]]: {merge_intervals([[1,5]])}")                   # Output: [[1,5]] (Single interval)
```
*   **Edge Cases Handled:** Empty list, single interval, intervals that just touch (`[1,4],[4,5]`), contained intervals (`[1,5],[2,3]`).

## 5. Time and Space Complexity Analysis

The efficiency of the Merge Intervals pattern depends heavily on the initial sorting step.

*   **Time Complexity:**
    *   **O(N log N)**: Where N is the number of intervals in the input list.
    *   **Justification:** The dominant factor is the initial sorting of the intervals, which takes O(N log N) time. The subsequent linear scan to merge intervals takes O(N) time, as each interval is visited exactly once for comparison and potential merging. Since O(N) is less than O(N log N) for N > 1, the sorting time determines the overall complexity.
*   **Space Complexity:**
    *   **O(N)**: For storing the merged intervals in a new list. In the worst case (e.g., no intervals overlap), the `merged_intervals` list will contain all N original intervals.
    *   **Justification:** A new list is created to store the results. If the sorting algorithm used by `list.sort()` requires auxiliary space (like Timsort, which can use O(N) in worst-case), that would also contribute. However, for the pattern itself, the output list is the primary space consumer.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Systematic and Reliable:** Provides a robust, step-by-step method for handling complex interval relationships.
*   **Efficient:** The sorting + linear scan approach is optimal for many problems involving intervals, especially for merging.
*   **Handles Various Overlap Scenarios:** The `max(current_end, next_end)` logic correctly consolidates intervals that partially overlap or are fully contained within others.

### Cons:
*   **Mandatory Sorting Step:** The O(N log N) sorting step is usually unavoidable. If the intervals are already sorted, or if there's a highly specialized data structure that could solve the problem faster without sorting (less common), then this pattern might not be the absolute fastest.
*   **Specific Problem Domain:** This pattern is highly specialized for problems expressed in terms of intervals (time ranges, numerical ranges, etc.). It's not a general-purpose algorithm.
*   **Variations Require Adaptation:** While the core merging logic is consistent, slight changes in problem requirements (e.g., finding intersections instead of unions, or counting active intervals) require careful adaptation of the comparison and state update logic.

## 7. Common Use Cases / Applications

The Merge Intervals pattern is frequently applied in various domains, especially scheduling and resource allocation:

*   **Consolidating Meeting Schedules:** As in the introduction, merging overlapping appointments.
*   **Merging Time Slots:** Combining available/unavailable time ranges.
*   **Finding Busy/Free Time:** Determining periods when a resource is occupied or available.
*   **Interval List Intersections:** Finding the common time periods between two lists of intervals.
*   **Minimum Number of Meeting Rooms:** Given a list of meeting time intervals, find the minimum number of conference rooms required so that all meetings can be attended. This is a common extension that leverages sorting by start time and often a min-heap for managing active meetings.
*   **Employee Free Time:** Given a list of employee schedules, find their common free time.

## 8. Related Concepts / Variations

*   **Sorting Algorithms:** Fundamental, as the pattern relies on pre-sorted intervals.
*   **Greedy Algorithms:** The approach of processing intervals sequentially after sorting often aligns with the greedy paradigm.
*   **Heaps (Priority Queues):** Can be used in conjunction with intervals, especially for problems like "Meeting Rooms II" where you need to efficiently track the end times of active meetings to find the minimum number of rooms.
*   **Sweep Line Algorithm (or Plane Sweep Algorithm):** A more advanced geometric algorithm that shares similarities with interval problems. It involves processing "events" (like start and end points of intervals) in sorted order along a line.

## 9. LeetCode Problem Examples

*   [56. Merge Intervals](https://leetcode.com/problems/merge-intervals/) (Medium) - The canonical problem for this pattern.
*   [57. Insert Interval](https://leetcode.com/problems/insert-interval/) (Medium) - Inserting a new interval into an already sorted and merged list.
*   [986. Interval List Intersections](https://leetcode.com/problems/interval-list-intersections/) (Medium) - Finding common parts between two lists of intervals.
*   [252. Meeting Rooms](https://leetcode.com/problems/meeting-rooms/) (Easy - often premium) - Determine if a person can attend all meetings (check for any overlap).
*   [253. Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/) (Medium - often premium) - Find minimum rooms needed (classic application with a min-heap).
*   [759. Employee Free Time](https://leetcode.com/problems/employee-free-time/) (Hard - often premium) - Merge all employee schedules, then find the gaps.

## 10. Further Reading / Resources

*   [GeeksforGeeks - Merge Overlapping Intervals](https://www.geeksforgeeks.org/merging-overlapping-intervals/) - A good starting point with a clear explanation.
*   [Educative.io - Merge Intervals Pattern (from Grokking the Coding Interview)](https://www.educative.io/courses/grokking-the-coding-interview/m2y00JZAPPD) - Provides a very detailed, structured learning experience for this pattern.
*   [NeetCode.io - Intervals](https://neetcode.io/roadmap) - Check his video explanations for interval-related problems.
*   [Medium - The Art of Intervals: A Practical Guide](https://medium.com/@devfire/the-art-of-intervals-a-practical-guide-to-solving-interval-problems-in-coding-interviews-9b375d65426b) - Good article exploring various interval problems.
