---
layout: post
title: Maximum Subarray Study
category: Algorithms and Data Structures
tags: [leetcode, algorithm, clrs, recursive, dynamic programming, divide and conquer]
---

In computer science, the maximum subarray problem is the task of finding the
contiguous subarray within a one-dimensional array of numbers which has the
largest sum.

Chapter 4 of the CLRS text uses the maximum subarray problem as an example of an
algorithm that can be solved using the divide and conquer paradigm. It turns the maximum
subarray problem into a recurrence almost identical to that of merge sort being of $O(n\log(n))$
time complexity.

In this post I'm going to talk briefly about two $O(n)$ dynamic programming solutions and some of the
intuition behind them, then touch on some of the performance tests I ran between three solutions:

 - Naive $O(n^2)$ time complexity
 - Divide and conquer $O(n\log(n))$ time complexity
 - Dynamic programming $O(n)$ time complexity

# Dynamic Programming Approach

As per the norm with just about any dynamic programming problem we want to
define the state of the subproblem we'll be looking at. This will help us figure
out exactly how we can store and reuse data from previously calculated problems to
aid us in larger ones. In this case we eventually want to return the sum of
the largest subarray within $A[0 \ldots n]$ so we need to figure out to frame
our subproblem.

### Common DP Approaches

A common dynamic programming approach is to keep some array $dp$ of optimized sub
problem answers and try to extend them as we come across larger problem instances.
A natural format for a subproblem is a straight recursive definition. This means we
want to figure out if we can relate the maximum subarray of $A[0 \ldots i-1]$ to the
maximum subarray of $A[0 \ldots i]$. Our dp array would contain values of the maximum
subarray up until point $i \; \forall \; i \in \\{ 0 \ldots n-1 \\}$ such that $dp[i]$
gives us the value of the maximum subarray of $A[0 \ldots i]$. The dp array would then
be some non-decreasing series with the answer to our original problem sitting at the end.
Unfortunately we can show right now that our chosen subproblem doesn't quite relate to other
instances of a larger size. For example if $A[0 \ldots i-1]$ was the following array:

$$[-16, 100, 200, -1300, -1400]$$

the maximum subarray of $A[0 \ldots i-1] = 300$. This is true no matter how many values
negating the maximum subarray come after the end of the *actual* maximum subarray $[100, 200]$
inside $A[0 \ldots i-1]$. This prevents us from extending our subproblem's answer to larger a
instance because it does not account for the actual boundaries of the maximum subarray.

This should be enough information to show us that the subproblem we want to work with cannot
be the same problem we're trying to solve on a smaller scale.

--------