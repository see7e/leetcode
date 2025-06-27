---
title: 290 - Word Pattern
tags:
  - studies
  - leetcode
  - programming
use: Problem
date: 2024-12-31
difficulty: Easy
languages: Python
dependences: 
time: 20:03
---

[290. Word Pattern](https://leetcode.com/problems/word-pattern/description/)
Given a `pattern` and a string `s`, find if `s` follows the same pattern.

Here **follow** means a full match, such that there is a bijection between a letter in `pattern` and a **non-empty** word in `s`. Specifically:

-   Each letter in `pattern` maps to **exactly** one unique word in `s`.
-   Each unique word in `s` maps to **exactly** one letter in `pattern`.
-   No two letters map to the same word, and no two words map to the same letter.

**Example 1:**

**Input:** pattern = "abba", s = "dog cat cat dog"

**Output:** true

**Explanation:**

The bijection can be established as:

-   `'a'` maps to `"dog"`.
-   `'b'` maps to `"cat"`.

**Example 2:**

**Input:** pattern = "abba", s = "dog cat cat fish"

**Output:** false

**Example 3:**

**Input:** pattern = "aaaa", s = "dog cat cat dog"

**Output:** false

**Constraints:**

-   `1 <= pattern.length <= 300`
-   `pattern` contains only lower-case English letters.
-   `1 <= s.length <= 3000`
-   `s` contains only lowercase English letters and spaces `' '`.
-   `s` **does not contain** any leading or trailing spaces.
-   All the words in `s` are separated by a **single space**.


# Solution

Firstly I've splited the string at the spaces and compared the lenths of the pattern and the words (list) - didn't knew if was needed.

Struggled a little bit to reduce the amount of loops, but managed to run a single loop based on the greatest length and compare the positions:

- `p in d.keys() and d[p] != w`: if there's already a key mapped with an existing value;
- `p not in d.keys() and w in d.values()`: if the pattern letter wasn't mapped yet but there was a value already stored.

And do make the comparissons possible, the conditional to build the dict:

```python
if pattern[i] not in d.keys() and words[i] not in d.values():
    d[pattern[i]] = words[i]
    continue
```

Managed to get a low runtime, but big relative memory usage, this could be optimized reducing some of the aux. variables but will compromise the readability.

- Runtime: 0ms (Beats 5,74%)
- Memory: 17.98MB (Beats 7.33%)

