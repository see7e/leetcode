---
title: 1436 - Destination City
tags:
  - studies
  - leetcode
  - programming
use: Problem
date: 2024-12-31
difficulty: Easy
languages: Python
dependences: 
time: 7:30
---

# [1436. Destination City](https://leetcode.com/problems/destination-city/description/)
You are given the array `paths`, where `paths[i] = [cityA<sub>i</sub>, cityB<sub>i</sub>]` means there exists a direct path going from `cityA<sub>i</sub>` to `cityB<sub>i</sub>`. _Return the destination city, that is, the city without any path outgoing to another city._

It is guaranteed that the graph of paths forms a line without any loop, therefore, there will be exactly one destination city.

**Example 1:**

```
<strong>Input:</strong> paths = [["London","New York"],["New York","Lima"],["Lima","Sao Paulo"]]
<strong>Output:</strong> "Sao Paulo" 
<strong>Explanation:</strong> Starting at "London" city you will reach "Sao Paulo" city which is the destination city. Your trip consist of: "London" -&gt; "New York" -&gt; "Lima" -&gt; "Sao Paulo".
```

**Example 2:**

```
<strong>Input:</strong> paths = [["B","C"],["D","B"],["C","A"]]
<strong>Output:</strong> "A"
<strong>Explanation:</strong> All possible trips are:&nbsp;
"D" -&gt; "B" -&gt; "C" -&gt; "A".&nbsp;
"B" -&gt; "C" -&gt; "A".&nbsp;
"C" -&gt; "A".&nbsp;
"A".&nbsp;
Clearly the destination city is "A".
```

**Example 3:**

```
<strong>Input:</strong> paths = [["A","Z"]]
<strong>Output:</strong> "Z"
```

**Constraints:**

-   `1 <= paths.length <= 100`
-   `paths[i].length == 2`
-   `1 <= cityA<sub>i</sub>.length, cityB<sub>i</sub>.length <= 10`
-   `cityA<sub>i</sub> != cityB<sub>i</sub>`
-   All strings consist of lowercase and uppercase English letters and the space character.

# Solution
The first approach is to run two loops to check if there's any destination that isn't in the list of origins:

```python
def destCity(self, paths: List[List[str]]) -> str:
        # Iterate over the start and end points of the paths
        origins = []
        for o, _ in paths:
            origins.append(o)
        for _, d in paths:
            if d not in origins:
                return d
```

But this isn't the best optimal solution (4ms: Beats 7.72% | 17.84MB: Beats 11.09%), trying to push that numbers down a little bit, managed to reduce the runtime with list comprehensions, but not the memory though.

The new solution is:

```python
def destCity(self, paths: List[List[str]]) -> str:
    # Iterate over the start and end points of the paths
    origins = [o for o,_ in paths]
    return [d for _, d in paths if not d in origins][0]
```

---

## Editorial

### Approach 2: Hash Set

**Intuition**
In the previous approach, we used an outer for loop to lock in a `candidate`. We then used an inner for loop to check if `candidate` had any outgoing path. This inner for loop is expensive, and we can check a given `candidate` in a much more efficient manner using a hash set.

We will create a hash set `hasOutgoing` that represents all the cities that have an outgoing path. We iterate over `paths` and for each index `i`, add `paths[i][0]` to `hasOutgoing`.

Now, we can iterate over `paths` again and select a `candidate = paths[i][1]` as we did in the previous approach. However, now that we have `hasOutgoing`, we can simply check if `candidate` is in `hasOutgoing` instead of using a nested for loop. If `hasOutgoing` contains `candidate`, then `candidate` cannot be the destination city. We simply check all candidates until we eventually find the destination city.

**Algorithm**
1.  Initialize a hash set `hasOutgoing`.
2.  Iterate `i` over the indices of `paths`:
    -   Add `paths[i][0]` to `hasOutgoing`.
3.  Iterate `i` over the indices of `paths`:
    -   Set `candidate = paths[i][1]`.
    -   If `candidate` is not in `hasOutgoing`, return `candidate`.
4.  The code should never reach this point. Return anything.

**Implementation**

<iframe src="https://leetcode.com/playground/L9TajziQ/shared" width="100%" height="361" name="user-content-L9TajziQ" allowfullscreen="" translate="no"></iframe>

**Complexity Analysis**

Given n as the length of `paths`,

-   Time complexity: O(n)
    We first iterate over `paths` to populate `hasOutgoing`, this costs O(n).
    Next, we iterate over `paths` again to find the answer, checking at each step whether `candidate` is in the hash set, which takes O(1). Thus the iteration costs O(n).
    
-   Space complexity: O(n)
    `hasOutgoing` will grow to a size of O(n).

