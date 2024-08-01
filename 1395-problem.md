---
title: 1395 - Count Number of Teams
tags: studies, programação, leetcode
use: Problem
date: 2021-07-29
difficulty: Medium
languages: Python
dependences: NULL
---

[1395\. Count Number of Teams](https://leetcode.com/problems/count-number-of-teams/)

There are `n` soldiers standing in a line. Each soldier is assigned a **unique** `rating` value.

You have to form a team of 3 soldiers amongst them under the following rules:

-   Choose 3 soldiers with index (`i`, `j`, `k`) with rating (`rating[i]`, `rating[j]`, `rating[k]`).
-   A team is valid if: (`rating[i] < rating[j] < rating[k]`) or (`rating[i] > rating[j] > rating[k]`) where (`0 <= i < j < k < n`).

Return the number of teams you can form given the conditions. (soldiers can be part of multiple teams).

**Example 1:**

```
Input: rating = [2,5,3,4,1]
Output: 3
Explanation: We can form three teams given the conditions. (2,3,4), (5,4,1), (5,3,1). 
```

**Example 2:**

```
Input: rating = [2,1,3]
Output: 0
Explanation: We can't form any team given the conditions.
```

**Example 3:**

```
Input: rating = [1,2,3,4]
Output: 4
```

**Constraints:**

-   `n == rating.length`
-   `3 <= n <= 1000`
-   `1 <= rating[i] <= 10<sup>5</sup>`
-   All the integers in `rating` are **unique**.

# Solution

I was trying to implement a consecutive search in the elements of the array:

```python
result = 0
if not len(rating) >= 3 and len(rating) <= 1000:
    return 0 # constraint 
       
rating.sort()
# check limits constraint
if rating[1] < 1 or rating[-1] > 100000:
    return 0
# calculate the possible teams
for idx in range(len(rating) -3, -1, -1):
    if rating[idx] < rating[idx+1] < rating[idx+2]:
        result += 1
    if rating[idx] > rating[idx+1] > rating[idx+2]:
        result += 1
return result
```

Here are the problems:
-   **Length check**: The length check logic `if not len(rating) >= 3 and len(rating) <= 1000` should use an `or` instead of `and` to correctly handle the constraints.
-   **Constraints on limits**: Sorting and then checking if `rating[1] < 1` or `rating[-1] > 100000` is unnecessary because the problem constraints ensure that the elements fall within the valid range.
-   **Logic for counting teams**: The loop that tries to count valid teams is flawed because it only checks consecutive elements. You need to check all possible triplets (i, j, k) where `i<j<k` to correctly count valid teams.
    

## Editorial

### Approach 1: Dynamic Programming (Memoization)

#### Intuition

The brute force approach to solving this problem involves checking all possible combinations of `rating` and counting those that meet our conditions. However, such an approach would have a time complexity of O(n3), which would not satisfy the given constraints.

Instead of using nested loops to examine all possible combinations of soldiers, we can simplify the problem by breaking it down into smaller sub-problems. The core idea is to determine how many teams each soldier can join and then sum these totals to get the final answer.

We can achieve this by employing recursion. Specifically, we define a function called `countIncreasingTeams` that takes two parameters: an index from the `rating` array and the number of members currently in the team. This function will return the total number of valid teams that can be formed starting from the given index with the current team size.

For each soldier, the function will explore all potential next soldiers who can be added to the team, provided they satisfy the rating condition. It will then recursively count the number of valid teams that can be formed from this new state. The recursion will terminate when the team has reached the maximum size of three members. At each step, the function accumulates the number of valid teams and returns this count.

During the recursion, we might encounter the same sub-problem multiple times at different stages, which are known as overlapping sub-problems. To optimize our solution and avoid redundant computations, we use memoization. This technique involves storing the result of each sub-problem the first time it is computed so that when we encounter the same sub-problem again, we can retrieve the result from a cache rather than recomputing it.

Since we need to count both increasing and decreasing teams, we set up two separate recursive functions, each with its cache. Each sub-problem is uniquely identified by two states: the current index and the size of the team already formed.

To get the final result, we initiate our recursive functions starting from each index in the `rating` array. By summing the number of teams returned by each recursion, we obtain the total number of valid teams.

#### Algorithm

Main method `numTeams`:

-   Initialize:
    -   `n` as the length of `rating`.
    -   `teams` to store the total number of possible teams.
    -   two arrays `increasingCache` and `decreasingCache` of size n×4 to serve as cache for the memoization.
-   Loop over the array `rating`. For each index `startIndex`:
    -   Call `countIncreasingTeams` and `countDecreasingTeams` with `startIndex`. Add their results to `teams`.
-   Return `teams`.

Helper method `countIncreasingTeams`:

-   Define a method `countIncreasingTeams` with parameters: `rating`, `currentIndex`, `teamSize` and the cache `increasingCache`.
-   Initialize `n` as the length of `rating`.
-   If `currentIndex` is equal to `n`, return `0`.
-   If `teamSize` is equal to `3`, return `1`.
-   If `increasingCache` already contains an entry with the current state, return it.
-   Initialize a variable `validTeams` to `0`.
-   Loop over all indices from `currentIndex + 1` to the end of the array. For each index `nextIndex`:
    -   If `rating[nextIndex]` is greater than `rating[currentIndex]`, call `countIncreasingTeams` with `nextIndex` and `teamSize` incremented by `1`.
-   Cache `validTeams` with the current state in `increasingCache` and return it.

Helper method `countDecreasingTeams`:

-   Define a method `countDecreasingTeams` with parameters: `rating`, `currentIndex`, `teamSize` and the cache `decreasingCache`.
-   This method is exactly the same as `countIncreasingTeams` except for majorly one thing:
    -   We check whether `rating[nextIndex]` is less than `rating[currentIndex]` to call `countDecreasingTeams`.

#### Implementation
```python
class Solution:
    def numTeams(self, rating: List[int]) -> int:
        n = len(rating)
        teams = 0
        increasing_cache = [[-1] * 4 for _ in range(n)]
        decreasing_cache = [[-1] * 4 for _ in range(n)]

        # Calculate total teams by considering each soldier as a starting point
        for start_index in range(n):
            teams += self._count_increasing_teams(
                rating, start_index, 1, increasing_cache
            ) + self._count_decreasing_teams(
                rating, start_index, 1, decreasing_cache
            )

        return teams

    def _count_increasing_teams(
        self,
        rating: List[int],
        current_index: int,
        team_size: int,
        increasing_cache: List[List[int]],
    ) -> int:
        n = len(rating)

        # Base case: reached end of array
        if current_index == n:
            return 0

        # Base case: found a valid team of size 3
        if team_size == 3:
            return 1

        # Return cached result if available
        if increasing_cache[current_index][team_size] != -1:
            return increasing_cache[current_index][team_size]

        valid_teams = 0

        # Recursively count teams with increasing ratings
        for next_index in range(current_index + 1, n):
            if rating[next_index] > rating[current_index]:
                valid_teams += self._count_increasing_teams(
                    rating, next_index, team_size + 1, increasing_cache
                )

        # Cache and return the result
        increasing_cache[current_index][team_size] = valid_teams
        return valid_teams

    def _count_decreasing_teams(
        self,
        rating: List[int],
        current_index: int,
        team_size: int,
        decreasing_cache: List[List[int]],
    ) -> int:
        n = len(rating)

        # Base case: reached end of array
        if current_index == n:
            return 0

        # Base case: found a valid team of size 3
        if team_size == 3:
            return 1

        # Return cached result if available
        if decreasing_cache[current_index][team_size] != -1:
            return decreasing_cache[current_index][team_size]

        valid_teams = 0

        # Recursively count teams with decreasing ratings
        for next_index in range(current_index + 1, n):
            if rating[next_index] < rating[current_index]:
                valid_teams += self._count_decreasing_teams(
                    rating, next_index, team_size + 1, decreasing_cache
                )

        # Cache and return the result
        decreasing_cache[current_index][team_size] = valid_teams
        return valid_teams
```

### [Approach 2: Dynamic Programming (Tabulation)](https://leetcode.com/problems/count-number-of-teams/editorial/?envType=daily-question&envId=2024-07-29#approach-2-dynamic-programming-tabulation)

#### Intuition

The main drawback of using the memoization approach is the significant space consumed by the recursion stack. To address this issue, we can refine the algorithm to reduce space usage by eliminating recursion.

In the memoization approach, the recursive function solves each sub-problem and stores the results in a cache. Since the solution to the main problem is derived from all its sub-problems, the final answer ends up being stored in the cache. Therefore, if we can construct the cache without recursion, we can avoid the overhead associated with the recursion stack.

To achieve this, we’ll use two 2D arrays: `increasingTeams` and `decreasingTeams`. Each array will have the position `(i, j)` representing the number of teams of size `j` that end with the `i`th member of the `rating` array. Initially, we populate these arrays with the base case: for all positions `(i, j)` where `j=1`, the number of ways to form a team is `1`, as the `i`th soldier alone constitutes a single-member team.

We then use three nested loops to consider all combinations of team lengths 2 and 3. For each position `(i, j)`, if we can append `rating[j]` to the sequence ending with `rating[i]`, we update the count of teams at `j` by adding the count of teams of the previous length at `i`.

Finally, to find the total number of teams of length 3, we iterate over both arrays and sum up the counts for all positions where `j` equals `3`. This accumulated total represents the number of teams formed, providing the answer to the problem without incurring recursion space overhead.

#### Algorithm

-   Initialize:
    -   `n` to the length of `rating`.
    -   `teams` to store the required number of teams.
-   Create two 2D arrays `increasingTeams` and `decreasingTeams` of size n×4 to store the count of increasing and decreasing sequences respectively.
-   Fill base case. For all `i` from `0` to `n`:
    -   Set `increasingTeams[i][1]` and `decreasingTeams[i][1]` to `1` (as each soldier forms a sequence of length 1).
-   Use 3 nested loops to fill the tables. The outer loop iterates over sequence lengths 2 and 3. The middle loop sets the middle soldier. The inner loop iterates over all soldiers as potential end points. For each pair of soldiers `i` and `j`:
    -   If `rating[j] > rating[i]`, add `increasingTeams[i][count-1]` to `increasingTeams[j][count]`.
    -   If `rating[j] < rating[i]`, add `decreasingTeams[i][count-1]` to `decreasingTeams[j][count]`.
-   Set `teams` as the sum of all sequences of length 3.
-   Return `teams` as our answer.

#### Implementation
```python
class Solution:
    def numTeams(self, rating: List[int]) -> int:
        n = len(rating)
        teams = 0

        # Tables for increasing and decreasing sequences
        increasing_teams = [[0] * 4 for _ in range(n)]
        decreasing_teams = [[0] * 4 for _ in range(n)]

        # Fill the base cases. (Each soldier is a sequence of length 1)
        for i in range(n):
            increasing_teams[i][1] = 1
            decreasing_teams[i][1] = 1

        # Fill the tables
        for count in range(2, 4):
            for i in range(n):
                for j in range(i + 1, n):
                    if rating[j] > rating[i]:
                        increasing_teams[j][count] += increasing_teams[i][
                            count - 1
                        ]
                    if rating[j] < rating[i]:
                        decreasing_teams[j][count] += decreasing_teams[i][
                            count - 1
                        ]

        # Sum up the results (All sequences of length 3)
        for i in range(n):
            teams += increasing_teams[i][3] + decreasing_teams[i][3]

        return teams
```

#### Complexity Analysis

Let n be the length of the `rating` array.

-   Time complexity: O(n2)
    
    Initializing the `increasingTeams` and `decreasingTeams` arrays take O(n) time.
    
    In the nested loops: the outer loop runs 2 times, the middle loop n times and the inner loop at most n times. Thus, the total complexity of the section is O(2⋅n⋅n), which simplifies to O(n2).
    
    The final summation loop runs in linear time.
    
    Thus, the total time complexity of the algorithm is dominated by the nested loops, resulting in O(n2).
    
-   Space complexity: O(n)
    
    The two 2D arrays `increasingTeams` and `decreasingTeams` each take n×4 space, which give them a total space complexity of O(2⋅4⋅n)\=O(n).
    
    All other elements take constant space, so the space complexity of the algorithm is O(n).
    

___

### [Approach 3: Dynamic Programming (Optimized)](https://leetcode.com/problems/count-number-of-teams/editorial/?envType=daily-question&envId=2024-07-29#approach-3-dynamic-programming-optimized)

#### Intuition

Having explored team formations by fixing either the starting or ending points, let's now consider an alternative approach: focusing on the middle member of each team.

The key insight here is to examine each soldier as a potential middle member. For each such soldier, we need to count:

-   How many soldiers to their left have lower ratings
-   How many soldiers to their right have higher ratings

We apply the same logic for descending teams:

-   How many soldiers to their left have higher ratings
-   How many soldiers to their right have lower ratings

For ascending teams, the number of valid teams for each index is the product of the number of smaller-rated soldiers to the left and larger-rated soldiers to the right. This is because each soldier with a lower rating to the left can be paired with each soldier with a higher rating to the right to form a valid team with the middle soldier.

The same principle applies to descending teams, where we multiply the count of higher-rated soldiers to the left by the count of lower-rated soldiers to the right.

To obtain the final result, we sum the number of teams formed for each potential middle soldier.

#### Algorithm

-   Initialize variables:
    -   `n`: length of the `rating` array.
    -   `teams`: to store the total count of valid teams.
-   Iterate through `rating`. For each soldier `mid`:
    -   Set `leftSmaller` and `rightLarger` counters to `0`.
    -   Count smaller rating to the left of `mid` and store it in `leftSmaller`.
    -   Count larger rating to the right of `mid` and store it in `rightLarger`.
    -   Calculate the number of ascending rating teams:
        -   Multiply `leftSmaller` by `rightLarger` and add it to `teams`.
    -   Calculate the number of descending rating teams:
        -   Set `leftLarger` as the total soldiers on left - `leftSmaller`.
        -   Set `rightSmaller` as the total soldiers on right - `rightLarger`.
        -   Multiply `leftLarger` by `rightSmaller` and add to `teams`.
-   Return `teams` as our answer.

#### Implementation
```python
class Solution:
    def numTeams(self, rating: List[int]) -> int:
        n = len(rating)
        teams = 0

        # Iterate through each soldier as the middle soldier
        for mid in range(n):
            left_smaller = 0
            right_larger = 0

            # Count soldiers with smaller ratings on the left side of the current soldier
            for left in range(mid - 1, -1, -1):
                if rating[left] < rating[mid]:
                    left_smaller += 1

            # Count soldiers with larger ratings on the right side of the current soldier
            for right in range(mid + 1, n):
                if rating[right] > rating[mid]:
                    right_larger += 1

            # Calculate and add the number of ascending rating teams (small-mid-large)
            teams += left_smaller * right_larger

            # Calculate soldiers with larger ratings on the left and smaller ratings on the right
            left_larger = mid - left_smaller
            right_smaller = n - mid - 1 - right_larger

            # Calculate and add the number of descending rating teams (large-mid-small)
            teams += left_larger * right_smaller

        # Return the total number of valid teams
        return teams
```

#### Complexity Analysis

Let n be the length of the `rating` array.

-   Time complexity: O(n2)
    
    The main loop iterates through the `rating` array, which takes linear time. In each iteration, the two inner loops compare n−1 elements in total. Thus, the overall time complexity is O(n⋅(n−1)), which simplifies to O(n2).
    
-   Space complexity: O(1)
    
    The space complexity is constant since no additional data structures dependent on the length of the input space are used.
    

___

### [Approach 4: Binary Indexed Tree (Fenwick Tree)](https://leetcode.com/problems/count-number-of-teams/editorial/?envType=daily-question&envId=2024-07-29#approach-4-binary-indexed-tree-fenwick-tree)


#### Intuition

In our previous approach, we performed a linear scan of elements to the left and right of each middle soldier, which contributed an O(n) factor to our overall complexity. To enhance efficiency, we need to explore a more advanced approach.

One such optimization involves querying the total count of smaller soldiers on either side of each soldier. This type of query can be optimized to O(logn) time using a data structure known as a Binary Indexed Tree (BIT) or Fenwick Tree. While a comprehensive explanation of how a BIT operates is beyond the scope of this article, interested readers can refer to [this discussion](https://cs.stackexchange.com/questions/10538/bit-what-is-the-intuition-behind-a-binary-indexed-tree-and-how-was-it-thought-a) for a deeper understanding. For hands-on practice, consider tackling these problems:

1.  [Range Sum Query - Mutable](https://leetcode.com/problems/range-sum-query-mutable/description/)
2.  [Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/description/)

Our improved solution utilizes two BITs: one to manage the left side and another for the right side of the current soldier. Each `BIT` stores frequency counts of ratings within a specific range. For instance, `BIT[5]` keeps track of the number of soldiers with a rating of `5`, while `BIT[6]` aggregates counts for ratings of `5` and `6`.

To implement our algorithm, we start by populating the right `BIT` with all the soldier ratings. As we process each soldier, we remove their rating from the right `BIT` and consider them the middle soldier. To count increasing sequences, we query the number of soldiers with lower ratings in the left `BIT` and the number of soldiers with higher ratings in the right `BIT`. The product of these two counts gives the total number of increasing teams with the current soldier positioned in the middle. Similarly, we perform this process to calculate the number of decreasing sequence teams. After processing, the current soldier's rating is added to the left `BIT`, and we continue with the next iteration.

#### Algorithm

Main method `numTeams`:

-   Set `maxRating` to the maximum rating in the `rating` array.
-   Initialize two binary indexed trees `leftBIT` and `rightBIT`, each of size `maxRating + 1`.
-   Populate `rightBIT` with all ratings initially using the `updateBIT` method.
-   Initialize `teams` to `0` to store the count of valid teams.
-   Iterate through each `rating` in the input array:
    -   Remove the current `rating` from `rightBIT`.
    -   Count `smallerRatingsLeft` using `getPrefixSum` on `leftBIT`.
    -   Count `smallerRatingsRight` using `getPrefixSum` on `rightBIT`.
    -   Set `largerRatingsLeft` as (all ratings) - (the ratings at and below the current `rating`) on `leftBIT`.
    -   Set `largerRatingsRight` as (all ratings) - (the ratings at or below the current `rating`) on `rightBIT`.
    -   Add to `teams`:
        -   Product of `smallerRatingsLeft` and `largerRatingsRight` (increasing sequences).
        -   Product of `largerRatingsLeft` and `smallerRatingsRight` (decreasing sequences).
    -   Add the current `rating` to the `leftBIT`.
-   Returns `teams` as the total number of teams possible.

Helper method `updateBIT`:

-   Define a method `updateBIT` with parameters: `BIT`, `index` and `value`.
-   While `index` is within the bounds of `BIT`:
    -   Add the given `value` to the current `index`.
    -   Move to the next node in the `BIT` by adding `index & (-index)` to `index`.

Helper method `getPrefixSum`:

-   Define a method `getPrefixSum` with parameters: `BIT` and `index`.
-   Initialize a variable `sum` to `0`.
-   While `index` is greater than `0`:
    -   Add the value at the current `index` in the `BIT` to `sum`.
    -   Move to the parent node in the `BIT` by subtracting `index & (-index)` from `index`.
-   Return `sum`.

#### Implementation
```python
class Solution:
    def numTeams(self, rating: List[int]) -> int:
        # Find the maximum rating
        max_rating = 0
        for r in rating:
            max_rating = max(max_rating, r)

        # Initialize Binary Indexed Trees for left and right sides
        left_BIT = [0] * (max_rating + 1)
        right_BIT = [0] * (max_rating + 1)

        # Populate the right BIT with all ratings initially
        for r in rating:
            self._update_BIT(right_BIT, r, 1)

        teams = 0
        for current_rating in rating:
            # Remove current rating from right BIT
            self._update_BIT(right_BIT, current_rating, -1)

            # Count soldiers with smaller and larger ratings on both sides
            smaller_ratings_left = self._get_prefix_sum(
                left_BIT, current_rating - 1
            )
            smaller_ratings_right = self._get_prefix_sum(
                right_BIT, current_rating - 1
            )
            larger_ratings_left = self._get_prefix_sum(
                left_BIT, max_rating
            ) - self._get_prefix_sum(left_BIT, current_rating)
            larger_ratings_right = self._get_prefix_sum(
                right_BIT, max_rating
            ) - self._get_prefix_sum(right_BIT, current_rating)

            # Count increasing and decreasing sequences
            teams += smaller_ratings_left * larger_ratings_right
            teams += larger_ratings_left * smaller_ratings_right

            # Add current rating to left BIT
            self._update_BIT(left_BIT, current_rating, 1)

        return teams

    # Update the Binary Indexed Tree
    def _update_BIT(self, BIT: List[int], index: int, value: int) -> None:
        while index < len(BIT):
            BIT[index] += value
            index += index & (-index)  # Move to the next relevant index in BIT

    # Get the sum of all elements up to the given index in the BIT
    def _get_prefix_sum(self, BIT: List[int], index: int) -> int:
        sum = 0
        while index > 0:
            sum += BIT[index]
            index -= index & (-index)  # Move to the parent node in BIT
        return sum
```

#### Complexity Analysis

Let n be the length of the `rating` array and maxRating be the maximum rating in `rating`.

-   Time complexity: O(n⋅log(maxRating))
    
    Finding `maxRating` takes linear time.
    
    Initially populating the `rightBIT` takes O(n⋅log(maxRating)) time.
    
    The main loop iterates n times. For each iteration, updating the BIT's have a complexity of O(log(maxRating)) and getting the prefix sums also take O(log(maxRating)) time. Thus, the total for the main loop is O(n⋅log(maxRating)).
    
    Thus, the overall time complexity of the algorithm comes out to be O(n)+O(2⋅n⋅log(maxRating)), which simplifies to O(n⋅log(maxRating)).
    
-   Space complexity: O(maxRating)
    
    The only additional space used are the two arrays for the BIT, each taking O(maxRating) space.
    
    This makes the space complexity of the algorithm O(2⋅maxRating)\=O(maxRating).
