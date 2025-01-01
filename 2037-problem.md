---
title: 2037 - Count Number of Teams
tags: studies, programação, leetcode
use: Problem
date: 2024-12-31
difficulty: Easy
languages: Python
dependences: NULL
time: 16:20
---

[2037. Minimum Number of Moves to Seat Everyone](https://leetcode.com/problems/minimum-number-of-moves-to-seat-everyone/description/)

There are `n` **availabe** seats and `n` students **standing** in a room. You are given an array `seats` of length `n`, where `seats[i]` is the position of the `i<sup>th</sup>` seat. You are also given the array `students` of length `n`, where `students[j]` is the position of the `j<sup>th</sup>` student.

You may perform the following move any number of times:

-   Increase or decrease the position of the `i<sup>th</sup>` student by `1` (i.e., moving the `i<sup>th</sup>` student from position `x` to `x + 1` or `x - 1`)

Return _the **minimum number of moves** required to move each student to a seat_ _such that no two students are in the same seat._

Note that there may be **multiple** seats or students in the **same** position at the beginning.

**Example 1:**

```
<strong>Input:</strong> seats = [3,1,5], students = [2,7,4]
<strong>Output:</strong> 4
<strong>Explanation:</strong> The students are moved as follows:
- The first student is moved from position 2 to position 1 using 1 move.
- The second student is moved from position 7 to position 5 using 2 moves.
- The third student is moved from position 4 to position 3 using 1 move.
In total, 1 + 2 + 1 = 4 moves were used.
```

**Example 2:**

```
<strong>Input:</strong> seats = [4,1,5,9], students = [1,3,2,6]
<strong>Output:</strong> 7
<strong>Explanation:</strong> The students are moved as follows:
- The first student is not moved.
- The second student is moved from position 3 to position 4 using 1 move.
- The third student is moved from position 2 to position 5 using 3 moves.
- The fourth student is moved from position 6 to position 9 using 3 moves.
In total, 0 + 1 + 3 + 3 = 7 moves were used.
```

**Example 3:**

```
<strong>Input:</strong> seats = [2,2,6,6], students = [1,3,2,6]
<strong>Output:</strong> 4
<strong>Explanation:</strong> Note that there are two seats at position 2 and two seats at position 6.
The students are moved as follows:
- The first student is moved from position 1 to position 2 using 1 move.
- The second student is moved from position 3 to position 6 using 3 moves.
- The third student is not moved.
- The fourth student is not moved.
In total, 1 + 3 + 0 + 0 = 4 moves were used.
```

**Constraints:**

-   `n == seats.length == students.length`
-   `1 <= n <= 100`
-   `1 <= seats[i], students[j] <= 100`

# Solution

The initial problem was to simply understant that the correlation between the order describled at the examples and the order in the lists (`seats` and `students`), and that's why I decided to sort the both lists (also I got confused between the `sorted()` function of sets and the `.sort()` method of lists).

After that was only to decide the range of the loop, at first I've setted for `range(0, 100)` but got the `Index Error` so I've switched to a value based on the length of the availabe seats.

Another small detail was to calculate the moves based either the position of the seats or the position of the students getting the absolute value of the needed moves to be performed.

- Runtime: 1ms (Beats 63.08%)
- Memory: 17.86MB (Beats 8.56%)

A possible improvement is to store the result of the `abs()` at the same varialbe of the return and as the `else` block does virtually nothing to replace the conditional to a branchless approach.

```python
for n in range(0, len(seats)):
    seats_to_move += abs(students[n] - seats[n]) * (students[n] != seats[n]) 
return seats_to_move
```

The runtime drops to 0ms but the memmory usage is still at the 17.8*MB...

---
