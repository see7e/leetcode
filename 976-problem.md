---
title: 976 - Largest Perimeter Triangle
tags:
  - studies
  - leetcode
  - programming
use: Problem
date: 2021-07-29
difficulty: Easy
status: solved
languages: Python
dependences:
---

[976\. Largest Perimeter Triangle](https://leetcode.com/problems/largest-perimeter-triangle/)

Given an integer array `nums`, return _the largest perimeter of a triangle with a non-zero area, formed from three of these lengths_. If it is impossible to form any triangle of a non-zero area, return `0`.

**Example 1:**

```
Input: nums = [2,1,2]
Output: 5
Explanation: You can form a triangle with three side lengths: 1, 2, and 2.
```

**Example 2:**

```
Input: nums = [1,2,1,10]
Output: 0
Explanation: 
You cannot use the side lengths 1, 1, and 2 to form a triangle.
You cannot use the side lengths 1, 1, and 10 to form a triangle.
You cannot use the side lengths 1, 2, and 10 to form a triangle.
As we cannot use any three side lengths to form a triangle of non-zero area, we return 0.
```

**Constraints:**

-   `3 <= nums.length <= 10<sup>4</sup>`
-   `1 <= nums[i] <= 10<sup>6</sup>`


# Solution

Runtime: 163ms
Beats 23.03%
O(N∗Log(N))

Memory: 12.70MB
Beats 94.55%
O(1)


## Editorial

### Approach 1: Sort

**Intuition**

Without loss of generality, say the sidelengths of the triangle are a≤b≤c. The necessary and sufficient condition for these lengths to form a triangle of non-zero area is a+b\>c.

Say we knew c already. There is no reason not to choose the largest possible a and b from the array. If a+b\>c, then it forms a triangle, otherwise it doesn't.

**Algorithm**

This leads to a simple algorithm: Sort the array. For any c in the array, we choose the largest possible a≤b≤c: these are just the two values adjacent to c. If this forms a triangle, we return the answer.

```python
class Solution(object):
    def largestPerimeter(self, A):
        A.sort()
        for i in xrange(len(A) - 3, -1, -1):
            if A[i] + A[i+1] > A[i+2]:
                return A[i] + A[i+1] + A[i+2]
        return 0
```

**Complexity Analysis**

-   Time Complexity: O(NlogN), where N is the length of `A`.
-   Space Complexity: O(1).

