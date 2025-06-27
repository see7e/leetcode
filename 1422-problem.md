---
title: 1422 - Maximum Score After Splitting a String
tags:
  - studies
  - leetcode
  - programming
use: Problem
date: 2024-12-31
difficulty: Easy
languages: Python
dependences: 
time:
---

# [1422. Maximum Score After Splitting a String](https://leetcode.com/problems/maximum-score-after-splitting-a-string/description/?envType=daily-question&envId=2025-01-01)
Given a string `s` of zeros and ones, _return the maximum score after splitting the string into two **non-empty** substrings_ (i.e. **left** substring and **right** substring).

The score after splitting a string is the number of **zeros** in the **left** substring plus the number of **ones** in the **right** substring.

**Example 1:**

```
<strong>Input:</strong> s = "011101"
<strong>Output:</strong> 5 
<strong>Explanation:</strong> 
All possible ways of splitting s into two non-empty substrings are:
left = "0" and right = "11101", score = 1 + 4 = 5 
left = "01" and right = "1101", score = 1 + 3 = 4 
left = "011" and right = "101", score = 1 + 2 = 3 
left = "0111" and right = "01", score = 1 + 1 = 2 
left = "01110" and right = "1", score = 2 + 1 = 3
```

**Example 2:**

```
<strong>Input:</strong> s = "00111"
<strong>Output:</strong> 5
<strong>Explanation:</strong> When left = "00" and right = "111", we get the maximum score = 2 + 3 = 5
```

**Example 3:**

```
<strong>Input:</strong> s = "1111"
<strong>Output:</strong> 3
```

**Constraints:**

-   `2 <= s.length <= 500`
-   The string `s` consists of characters `'0'` and `'1'` only.

# Solution
The first thought that crossed my mind was to implement two slices of a [list](GAB/Estudos-Trabalhos/PROGRAMAÇÃO/programming-studies/Languages/Python/README.md#lists)) and iterate over it's length to get the left and right elements, and then sum which portion got the right numbers.
Having the left and right slices, and the score of each one, I've just needed to compare the maximum with the previous score (initialy 0).
The results were not the best neither the wrost:

- Runtime: 19ms (Beats 8,72%) `O(n)`
- Memory: 17,76MB (Beats 15,52%) `O(1)`

---

## Editorial

> The best two (time related) approaches answers at the editorial have the same behaviour as mine (`O(n)` and `O(1)`).

### Approach 2: Count Left Zeros and Right Ones

**Intuition**
We can improve on the previous solution by noticing that between a split at index `i` and index `i + 1`, we are only changing one character (more specifically, moving it from the right substring to the left substring), leaving the other characters unchanged. Instead of iterating over the entire string for each split, we only need to check the moved character and calculate the score for the new split based on the previous split.

We start by counting how many times `1` occurs in `s`. Let's store this value in a variable `ones`. We will also have a variable `zeros` that represents how many `0` are in the left part. Initially, our variables `ones` and `zeros` are set as if the left part is empty and the right part is the entire string.

Now, we iterate `i` in the same manner as the previous approach: each index `i` represents the final index of the left part. At each iteration `i`, we remove `s[i]` from the right part and add it to the left part.

![example](https://leetcode.com/problems/maximum-score-after-splitting-a-string/editorial/?envType=daily-question&envId=2025-01-01../Figures/1422/1.png)  
  

There are two possibilities for each index `i`:

-   If `s[i] == '1'`: this `1` was in the right part, but it is now joining the left part. Thus, we lose `1` score since the right part is losing a `1`. Decrement `ones`.
-   If `s[i] == '0'`, this `0` was in the right part, but it is now joining the left part. Thus, we gain `1` score since the left part is gaining a `0`. Increment `zeros`.

We update the answer with `zeros + ones` at each iteration if it is larger.

**Algorithm**
1.  Initialize `ones` as the number of times `1` occurs in `s`.
2.  Initialize `zeros = 0` and the answer `ans = 0`.
3.  Iterate `i` from `0` until `s.length - 1`:
    -   If `s[i] == '1'`, decrement `ones`.
    -   Otherwise, increment `zeros`.
    -   Update `ans` with `zeros + ones` if it is larger.
4.  Return `ans`.

**Implementation**

```python
class Solution:
    def maxScore(self, s: str) -> int:
        ones = s.count("1")
        zeros = 0
        ans = 0 

        for i in range(len(s) - 1):
            if s[i] == "1":
                ones -= 1
            else:
                zeros += 1
        
            ans = max(ans, zeros + ones)
        
        return ans
```

**Complexity Analysis**

Given n as the length of `nums`,

-   Time complexity: O(n)
    
    We start by finding the frequency of `1`, which costs O(n). Next, we iterate over the string once, performing O(1) work at each iteration. Thus, our time complexity is O(2n)\=O(n).
    
-   Space complexity: O(1)
    
    We aren't using any extra space other than a few integers.

___

### Approach 3: One Pass

**Intuition**

In the previous approach, we used two passes over the input string: once to calculate `ones`, and another time to calculate `ans`. We can further optimize the algorithm to only use one pass!

The answer to our problem is the maximum score for all valid splits, as represented by the following equation:

score\=ZL+OR, where ZL is the number of zeros in the left substring and OR is the number of ones in the right substring.

We can express OR as OT−OL, where OT is the total number of ones in `s`, and OL is the number of ones in the left substring.

Using the above expression, our first equation can be represented as:

score\=ZL+OT−OL

In the above equation, OT is a constant, we need to find the maximum value of ZL−OL for all valid splits. Notice that both of these values depend solely on the left substring. Therefore, we don't need to consider the right substring, which saves the need for the first traversal in the previous solution.

In the code, we will use the variable `zeros` to represent ZL and the variables `ones` to represent OL. As `zeros - ones` may be negative, we initialize an integer `best` to a very small value, like negative infinity. Here, `best` represents the largest value of `zeros - ones` we have seen so far.

We now iterate `i` in the same manner as the first two approaches: at each iteration, `i` represents the final index of the left part. On each iteration, we are adding `s[i]` to the left part. Thus, if `s[i] = '1'` we increment `ones`, otherwise `s[i] = '0'` and we increment `zeros`. Then, we update `best` with `zeros - ones` if it is larger.

Recall that we don't iterate `i` over the final index since it would mean having an empty right part. Once we are done iterating over `s`, we will check the final index to see if it is a `1`. If it is, we increment `ones`.

The reason we explicitly check the final index for `1` is that we want `ones` to represent OT in the end, but when we calculate `ones`, we don't iterate over the last index, so we need to account for it. Now, we have `best` as the maximum of all ZL−OL and `ones` represents OT, we can return `best + ones` as the answer.

**Algorithm**

1.  Initialize `ones = 0`, `zeros = 0`, and `best` to a very small value like negative infinity.
2.  Iterate `i` from `0` until `s.length - 1`:
    -   If `s[i] == '1'`, increment `ones`.
    -   Otherwise, increment `zeros`.
    -   Update `best` with `zeros - ones` if it is larger.
3.  If the final character of `s` is equal to `'1'`, increment `ones`.
4.  Return `best + ones`.

**Implementation**

```python
class Solution:
    def maxScore(self, s: str) -> int:
        ones = 0
        zeros = 0
        best = -inf

        for i in range(len(s) - 1):
            if s[i] == "1":
                ones += 1
            else:
                zeros += 1
            
            best = max(best, zeros - ones)
            
        if s[-1] == "1":
            ones += 1
        
        return best + ones
```

**Complexity Analysis**

Given n as the length of `nums`,

-   Time complexity: O(n)
    
    We make one pass over `nums`, performing O(1) work at each iteration.
    
-   Space complexity: O(1)
    
    We aren't using any extra space other than a few integers.
