---
layout: post
title: Maximum Subarray Study
category: Algorithms and Data Structures
tags: [leetcode, algorithm, clrs, recursive, dynamic programming, divide and conquer]
---

In computer science, the maximum subarray problem is the task of finding the
contiguous subarray within a one-dimensional array of numbers which has the
largest sum.

In chapter 4, the CLRS text uses the maximum subarray problem as an example of an
algorithm that can be solved using the divide and conquer paradigm. It turns the maximum
subarray problem into a recurrence almost identical to that of merge sort being $O(n\log(n))$.

In this post I'm going to talk briefly about two dynamic programming $O(n)$ solutions and some of the
intuition behind them, then touch on some of the performance tests I ran between the three solutions
(naive $O(n^2)$, divide and conquer $O(n\log(n))$, and dynamic programming $O(n)$).

# Dynamic Programming Approach

As per the norm with just about any dynamic programming problem we want to
define the state of the subproblem we'll be looking at. This will help us figure
out exactly how we can store and reuse data from previously calculated problems to
aid us in larger ones. In this case we want to eventually want to return the sum of
the largest subarray within $A[0 \cdots n]$. At this point it is common to want to
try and relate the largest subarray of $A[0 \cdots i]$ to the largest subarray of an array
consisting of one less element $A[0 \cdots i-1]$. But we can show right now that the two
don't relate. For example if $A[0 \codts i-1]$ was the following array:

$$[-16, 100, 200, -1300, -1400]$$

the maximum subarray of $A[0 \cdots i-1] = 300$. This is true no matter how many negating
values come after the end of the actual max subarray $[100, 200]$.

--------