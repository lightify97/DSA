# Bitwise XOR Tricks

## 1. Introduction / Concept

In computer science, numbers are ultimately represented as sequences of binary digits (bits: 0s and 1s). **Bitwise operations** allow you to manipulate these individual bits directly. While they might seem obscure at first, understanding them can unlock highly efficient and elegant solutions for certain types of problems, often achieving O(N) time complexity with O(1) space complexity.

Among the bitwise operators, **XOR (Exclusive OR)** is particularly powerful and frequently used for tricky problems related to finding unique numbers, missing numbers, or swapping values without a temporary variable, due to its unique mathematical properties.

## 2. Core Principles / Theory (XOR)

The XOR operator (`^` in Python) compares two bits. It returns `1` if the corresponding bits are different, and `0` if they are the same.

| Bit A | Bit B | A ^ B |
| :---- | :---- | :---- |
| 0     | 0     | 0     |
| 0     | 1     | 1     |
| 1     | 0     | 1     |
| 1     | 1     | 0     |

This truth table gives rise to several crucial properties that are the foundation of XOR tricks:

1.  **Identity with Zero:** `A ^ 0 = A`
    *   XORing any number `A` with zero results in `A` itself. Zero acts as the identity element for XOR.
    *   *(If you have `1011 ^ 0000`, the result is `1011`)*

2.  **Self-Inverse Property:** `A ^ A = 0`
    *   XORing any number `A` with itself results in zero.
    *   *(If you have `1011 ^ 1011`, the result is `0000`)*

3.  **Commutative Property:** `A ^ B = B ^ A`
    *   The order of operands does not matter.

4.  **Associative Property:** `(A ^ B) ^ C = A ^ (B ^ C)`
    *   The grouping of operations does not matter. This means you can XOR a series of numbers in any order, and the result will be the same.
    *   *(E.g., `1 ^ 2 ^ 3 ^ 1` is the same as `(1 ^ 1) ^ (2 ^ 3)`.)*

5.  **Cancellation Property (derived from above):** `A ^ B ^ A = B`
    *   This is the most powerful property for many coding interview problems. If you XOR a number `A` with some value `B`, and then XOR the result with `A` again, you get `B` back. It's like `A` "cancels itself out."
    *   *(Example: `5 ^ 3 ^ 5`. In binary, `0101 ^ 0011 ^ 0101`. This simplifies to `(0101 ^ 0101) ^ 0011 = 0000 ^ 0011 = 0011` (which is 3).)*

These properties are key to XOR being effective for "canceling out" numbers that appear an even number of times, leaving behind numbers that appear an odd number of times.

### Other Useful Bitwise Operators in Python:

*   **`&` (Bitwise AND):** Sets a bit to `1` if both corresponding bits are `1`. Useful for checking if a bit is set, or for masking.
    *   `5 & 1` (`0101 & 0001` = `0001` = 1) -> Checks if least significant bit is 1 (odd number).
*   **`|` (Bitwise OR):** Sets a bit to `1` if at least one of the corresponding bits is `1`. Useful for setting bits.
    *   `5 | 2` (`0101 | 0010` = `0111` = 7)
*   **`~` (Bitwise NOT):** Inverts all bits (bitwise complement). Note: In Python, this gives `-(x+1)` due to two's complement representation.
    *   `~5` (`~0101` = `...11111010` in 2's complement = -6)
*   **`<<` (Left Shift):** Shifts bits to the left. `x << n` is equivalent to multiplying `x` by `2^n`.
    *   `5 << 1` (`0101 << 1` = `1010` = 10)
    *   `1 << n` is a common way to get a number with only the Nth bit set (e.g., `1 << 3` is `1000` = 8).
*   **`>>` (Right Shift):** Shifts bits to the right. `x >> n` is equivalent to integer division of `x` by `2^n`.
    *   `5 >> 1` (`0101 >> 1` = `0010` = 2)

## 3. Operations / Methods

The core "operations" are simply the direct application of these bitwise operators.

*   `result ^= num`: The most common XOR trick.
*   `num & 1`: Check if a number is odd.
*   `num & (num - 1)`: Clears the least significant set bit. Used for checking powers of 2.
*   `count = 0; while num > 0: num &= (num - 1); count += 1` (Brian Kernighan's algorithm for counting set bits).

## 4. Python Implementation Details / Considerations

*   **Arbitrary Precision Integers:** Python's integers automatically handle arbitrary precision. This means you don't need to worry about integer overflow for very large numbers or explicit bit widths (e.g., 32-bit, 64-bit) as you might in C++ or Java. Python will manage the underlying bit representation as needed.
*   **Negative Numbers:** Bitwise operations on negative numbers in Python work based on two's complement representation, similar to C++/Java. Be aware of how `~` behaves. For `>>` on negative numbers, Python performs arithmetic right shift (fills with sign bit), which is generally what you want for division.
*   **Readability:** While powerful, bitwise solutions can sometimes be less readable than arithmetic ones if the "trick" is not immediately obvious. Add comments!

### Example 1: Finding the Single Number (LeetCode 136)

Given a non-empty array of integers `nums`, every element appears twice except for one. Find that single one.

```python
def find_single_number(nums: list[int]) -> int:
    # Initialize a variable 'single' to 0.
    # Property: X ^ 0 = X (Identity)
    single = 0 

    # Iterate through each number in the array.
    for num in nums:
        # XOR the 'single' accumulator with the current number.
        # Property: X ^ X = 0 (Self-Inverse)
        # Property: X ^ Y ^ X = Y (Cancellation)
        single ^= num
    
    # After XORing all numbers:
    # All numbers that appear twice will cancel each other out (X ^ X = 0).
    # The unique number, XORed with 0 (which is the result of all pairs canceling),
    # will remain unchanged.
    return single

# Edge Cases and Examples:
print(f"Single number in [2, 2, 1]: {find_single_number([2, 2, 1])}")     # Output: 1
print(f"Single number in [4, 1, 2, 1, 2]: {find_single_number([4, 1, 2, 1, 2])}") # Output: 4
print(f"Single number in [7]: {find_single_number([7])}")               # Output: 7 (Single element array)
# print(find_single_number([])) # This would raise an error as problem statement says "non-empty"
```
*   **Edge Cases Handled:** Single element array. The core properties of XOR inherently handle duplicates and the unique number.

### Example 2: Checking if a number is a power of two

A number N is a power of two if and only if N > 0 and `N & (N - 1)` is 0.

```python
def is_power_of_two(n: int) -> bool:
    # Condition 1: Number must be positive. Powers of two are > 0.
    # Condition 2: The bitwise AND of N and (N-1) must be 0.
    # Explanation:
    # A power of two in binary has exactly one bit set (e.g., 1=0001, 2=0010, 4=0100, 8=1000).
    # N-1 will have all bits to the right of that set bit flipped to 1, and that set bit flipped to 0.
    # E.g., N = 8 (1000_2), N-1 = 7 (0111_2) -> 1000 & 0111 = 0000.
    # E.g., N = 6 (0110_2), N-1 = 5 (0101_2) -> 0110 & 0101 = 0100 (not 0).
    return n > 0 and (n & (n - 1) == 0)

# Edge Cases and Examples:
print(f"Is 1 a power of two? {is_power_of_two(1)}")  # Output: True (2^0)
print(f"Is 2 a power of two? {is_power_of_two(2)}")  # Output: True (2^1)
print(f"Is 4 a power of two? {is_power_of_two(4)}")  # Output: True (2^2)
print(f"Is 8 a power of two? {is_power_of_two(8)}")  # Output: True (2^3)
print(f"Is 0 a power of two? {is_power_of_two(0)}")  # Output: False (Edge case: N > 0 check)
print(f"Is 3 a power of two? {is_power_of_two(3)}")  # Output: False
print(f"Is 6 a power of two? {is_power_of_two(6)}")  # Output: False
print(f"Is -2 a power of two? {is_power_of_two(-2)}") # Output: False (Edge case: negative numbers)
```
*   **Edge Cases Handled:** 0, negative numbers, numbers that are not powers of two. The `n > 0` guard is essential.

## 5. Time and Space Complexity Analysis

Bitwise operations are extremely efficient.

*   **Time Complexity:**
    *   Typically **O(1)** or **O(N)** (where N is the number of elements in an array).
    *   **Justification:** Individual bitwise operations are constant time operations. When applied in a loop over an array, the complexity becomes O(N) because each element is processed once with constant time bitwise operations.
*   **Space Complexity:**
    *   Typically **O(1)** (constant space).
    *   **Justification:** Bitwise solutions usually only require a few variables to store intermediate results, irrespective of the input size.

## 6. Pros and Cons / Trade-offs

### Pros:
*   **Exceptional Efficiency:** Achieves the best possible time (O(N) or even O(1)) and space (O(1)) complexity for problems where they are applicable. They directly manipulate hardware-level operations, making them very fast.
*   **Concise and Elegant Solutions:** Can provide very short, clever, and elegant solutions that might be much more verbose or complex with other methods.
*   **No Hash Table Overhead:** For problems like "finding unique number," XOR avoids the space and average-case time overhead of hash tables.

### Cons:
*   **Limited Applicability:** This is a highly specialized pattern. It's only useful for problems where the mathematical properties of bitwise operations (especially XOR's cancellation property, or specific bit manipulation needs) are directly relevant. It is not a general-purpose problem-solving technique.
*   **Less Intuitive:** Can be significantly harder to understand and debug for those who are not familiar with binary representations and bitwise logic. This can make the code less readable for others (or your future self!).
*   **Over-optimization:** Sometimes, a bitwise trick is used for a problem that can be solved just as efficiently (asymptotically) and more readably with standard arithmetic or data structures. Use them when they truly offer a performance advantage or a significantly cleaner solution.

## 7. Common Use Cases / Applications

*   **Finding the Single Number / Unique Elements:**
    *   Find the one element that appears only once, while all others appear twice.
    *   Find the two elements that appear once, while all others appear twice.
    *   (e.g., LeetCode 136. Single Number, 260. Single Number III).
*   **Finding the Missing Number:**
    *   Given an array of `n` distinct numbers in the range `[0, n]`, find the missing number.
    *   *(Strategy: XOR all numbers from `0` to `n` with all numbers in the array. The result will be the missing number.)*
    *   (e.g., LeetCode 268. Missing Number).
*   **Swapping Two Numbers Without a Temporary Variable:**
    *   A classic interview trick: `a = a ^ b; b = a ^ b; a = a ^ b;`.
*   **Checking if a Number is a Power of Two (or Four, etc.).**
*   **Counting Set Bits (Hamming Weight):** Counting the number of `1`s in the binary representation of a number.
*   **Manipulating Bitmasks:** Using integers to represent sets of boolean flags, often used in dynamic programming or representing states.
*   **Detecting Parity:** Checking if a number has an even or odd number of set bits.
*   **Finding the XOR sum of all subarrays (prefix XOR).**
*   **Gray Code Conversion.**

## 8. Related Concepts / Variations

*   **Bit Manipulation:** The broader field of operations on individual bits. This pattern is a subset of bit manipulation.
*   **Bitmasks:** Integers used to store collections of boolean flags. Operations like AND, OR, NOT, and shifts are heavily used with bitmasks.

## 9. LeetCode Problem Examples

*   [136. Single Number](https://leetcode.com/problems/single-number/) (Easy)
*   [260. Single Number III](https://leetcode.com/problems/single-number-iii/) (Medium)
*   [268. Missing Number](https://leetcode.com/problems/missing-number/) (Easy)
*   [191. Number of 1 Bits (Hamming Weight)](https://leetcode.com/problems/number-of-1-bits/) (Easy)
*   [342. Power of Four](https://leetcode.com/problems/power-of-four/) (Easy)
*   [338. Counting Bits](https://leetcode.com/problems/counting-bits/) (Easy)
*   [7. Reverse Integer](https://leetcode.com/problems/reverse-integer/) (Medium) - Can involve bitwise checks for overflow.
*   [201. Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range/) (Medium)

## 10. Further Reading / Resources

*   [GeeksforGeeks - Bitwise Algorithms](https://www.geeksforgeeks.org/bitwise-algorithms/) - Comprehensive guide to various bitwise operations.
*   [HackerEarth - Bit Manipulation](https://www.hackerearth.com/practice/basic-programming/bitwise-operations/basics-of-bitwise-operators/tutorial/) - Explores the basics and some common tricks.
*   [LeetCode - Bit Manipulation Study Plan](https://leetcode.com/study-plan/programming-skills/?progress=TIEU0B2Q) - A dedicated study plan on LeetCode for bit manipulation.
*   [YouTube - Back To Back SWE: Single Number (XOR)](https://www.youtube.com/watch?v=KRXEJh9-4u4) - Video explanation for the Single Number problem using XOR.
