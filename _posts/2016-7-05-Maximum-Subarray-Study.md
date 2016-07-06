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

# Dynamic Programming

As per the norm with just about any dynamic programming problem we want to
define the state of the subproblem we'll be looking at so we can figure out
how exactly we can reuse previously calculated answers. It is common to keep
some dp structure of optimized sub problem answers to try solve larger instances
with. We then need to forge a relationship between the original problem and
a subproblem so we can see how we'll use our dp structure. In this case we
eventually want to return the sum of the maximum subarray within
$A[0 \ldots n]$.

### Common DP Patterns

A natural approach for this problem is to define the subproblem as a straight
recursive definition. This means we want to figure out if we can relate the
sum of the maximum subarray within $A[0 \ldots i]$ to the sum of the maximum
subarray within $A[0 \ldots i-]$. Our dp array would contain values of the maximum
subarray up until point $i \; \forall \; i \in \\{ 0 \ldots n-1 \\}$ such that
$dp[i]$ gives us the sum of the maximum subarray within $A[0 \ldots i]$. The dp
array would then be some non-decreasing series with the answer to our original
problem sitting at the end. This is a fairly common dp pattern, unfortunately
we can show right now that our chosen subproblem doesn't easily relate to
instances of a larger size. For example if $A[0 \ldots i-1]$ was the following
array:

$$[-16, 100, 200, -1300, -1400]$$

our dp array would look like:

$$[-16, 100, 300, \;\; 300 \;, \;\; 300]$$

The sum of the maximum subarray within $A[0 \ldots i-1] = 300$. This is true no
matter how many values negating the sum come after the end of the *actual* maximum
subarray $[100, 200]$ lying somewhere inside $A[0 \ldots i-1]$. The
problem is we cannot extend our subproblem's answer to a larger instance because
we don't know how many detrimental numbers in between the end of the true maximum
subarray and some new number we would be forced to accept by bridging the gap. 

Knowing the sum of the maximum subarray **inside** some boundaries doesn't cut it. In
order  to see how a previous subproblem can be extended, we need to know the sum of the
maximum subarray ending with element $A[i-1]$. Then when we introduce element $i$ we can
decide whether it is beneficial to tack on $A[i]$ to the current subarray or if $A[i]$ alone
is bigger than the sum of maximum subarray ending in $A[i-1]$. The relation between subproblems
and larger instances becomes more clear and our dp array will be used to store the sum of the
maximum subarray ending in $i \; \forall \; i \in \\{ 0 \ldots n-1 \\}$. The dp array
like this:

--------