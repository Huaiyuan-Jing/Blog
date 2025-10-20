---
title: Get Longest Common Subsequence (LCS) in O(NlogN)
tags: 
    - Algorithm
math: true
abbrlink: e609e3dd
date: 2025-09-08 02:36:31
index_img: https://github.com/user-attachments/assets/5266616f-79c1-4ff8-8b59-36896af7655c
banner_img: https://github.com/user-attachments/assets/5266616f-79c1-4ff8-8b59-36896af7655c
author: HackThink
---

## Intro: Get LCS (Longest Common Sunsequence) in O(NM)

Let me beriefly explain the common way to get LCS in textbook. Let `A` and `B` are strings, consider using dynamic programming, `dp[i][j]` represents LCS in `A[0..i]` and `B[0..j]`. Then we can get the following dp formula:

```Rust
dp[i][j] = if A[i] == B[j] {
    dp[i - 1][j - 1] + 1
} else {
    max(dp[i - 1][j], dp[i][j - 1])
}
```

It takes O(NM) (where N, M are length of A and B) to get LCS. However, when N and M get to $10^5$, it is not acceptable in competitive programming.

## Prerequisite: Get LIS (Longest Increasing Subsequence) in O(NlogN)

One more step before we get into the title. Because in the end we will transform LCS problems to LIS problems in a linear time t reach `O(NlogN)`, so I have to remind you of LIS in `O(NlogN)` before we really get started:

The key idea to get LIS in this way is Greedy: it maintains an ordered array `tails[]` which `tails[i]` represent __the smallest end among ends of all the increasing subsequences with length i__ (array indexed by 1), like the following example:

A = [5, 1, 3, 5, 1]

+ A[1] = 5, tails = [5]
+ A[2] = 1, tails = [1]
+ A[3] = 3, tails = [1, 3]
+ A[4] = 5, tails = [1, 3, 5]
+ A[5] = 1, tails = [1, 3, 5]

We can observe that `tails[]` is ordered because if `tails[i] > tails[j]` with `i < j`, then we can update `tails[i]` with `tails[j]`. Because we can get an increasing subsequence with length `i` end in `tails[j]` by remove the first `j - i` elements in the increasing subsequence with length `j` end in `tails[j]`, which causes a contradiction. 

The exact steps we maintain `tails[]` during each iteration of `A[i]` is:

+ Find the first element in `tails[]` which is larger than or equal to `A[i]` and replace it. 
+ If `A[i]` is larger than all the elements in `tails[]`, push `A[i]` to the end of `tails[]`.

After iterate all the elements, we can see the length of `tails[]` is the length of LIS.

And the time complexity of this algorithm is `O(NlogN)`, if we use binary search in the step of find the first element larger than `A[i]`

## Key: LCS <=> LIS

### Steps:

Consider `A = [3, 9, 7, 10, 3]` and `B = [5, 3, 7, 3]`, we want to find the LCS between them.

1. Build a `map[][]` to record indexs of every element in `A[i]`, like:

+ map[3] = [1, 5]
+ map[9] = [2]
+ map[7] = [3]
+ map[10] = [4]

2. Replace every elements in `B[]` with the index recorded in map with reverse order:

+ B[1] = 5 -> map[5] = [] -> []
+ B[2] = 3 -> map[3] = [1, 5] -> [5, 1]
+ B[3] = 7 -> map[7] = [3] -> [3]
+ B[4] = 3 -> map[3] = [1, 5] -> [5, 1]

3. Combine the indexs and form a new array called `C[]`:

+ C = [5, 1, 3, 5, 1]

4. Calculate the length of LIS in `C[]`, which is equal to the length of LCS of `A[]` and `B[]`

### Prove 1: LCS -> LIS

Assume one of the longest LCS among `A[]` and `B[]` is `S[]`, then `S[]` satisfy the following properties:

1. All elements in `S[]` appear in both `A[]` and `B[]`
2. The relative order in `S[]` is same with both `A[]` and `B[]`

And because of these properties, if we map all `S[]` elements on `C[]`, it is an increasing subsequence with the same length.

### Prove 2: LIS -> LCS

Assume one of the longest LIS in `C[]` is `S'[]`, then `S'[]` satisfy the following properties:

1. The map of `S'[]` in `A[]` is a subsequence of `A[]`, because the value of each element `S'[]` is increasing and unique, so one element in `S'[]` match exactly one element in `A[]` with increasing index.
2. The map of `S'[]` in `B[]` is a subsequence of `B[]`, because each element in `B[]` match a list of its index in `A[]` with decreasing order in `C[]`, but `S'[]` elements' value is increasing, so it's impossible that two different elements in `S'[]` match to the same element in `B[]`. In the other word, one element in `S'[]` match exactly one element in `B[]` with increasing index. 

So for each LIS in `C[]`, we can find a common subsequence with the same length in both `A[]` and `B[]`.


## Conclusion

Based on the prove of both directions, we can conclude all LCS problems can transform to the matched LIS problem without bias. And because LIS can be obtained in O(NlogN), in most cases we can get an improvement in time complexity.

## In The End: WARNING: Worst Case Scenario and When we should use it?

It is obvious that the performance of this algorithm is highly relate to the length of transformed array `C[]`, which we define as `N`. And if we define length of `A`, `B` are `P`, `Q`. Can we build a test case to make this algorithm even slower than the original way with `O(PQ)`?

Definitely we can, consider the following `A` and `B`:

A = [1, 1, 1, 1, 1]
B = [1, 1, 1, 1, 1]

And by the previous steps, we can get `C`:

C = [5, 4, 3, 2, 1, 5, 4, 3, 2, 1, 5, 4, 3, 2, 1, 5, 4, 3, 2, 1, 5, 4, 3, 2, 1]

We can see in this worst case, length of `C` reaches `P` * `Q` and the total time complexity of algorithnm reaches `O(PQlog(PQ))` which is worse than the DP one. This tell us the algorithm may not get an improvement when there are a lot of repeated elements in `A[]` and `B[]`. Be careful about this point when considering your test cases.

## Try It

[Sample Problem on Kattis](https://open.kattis.com/problems/doubledeck)