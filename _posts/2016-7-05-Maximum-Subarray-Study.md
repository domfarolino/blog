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

As per the norm with just about any dynamic programming problem we want
to maintain some structure of optimized subproblem answers to help us
solve larger problem instances. First we need to define the state of the
subproblem. Then we need to forge a relationship between the original
problem and the subproblem so we can see how we'll use our dp structure.
Eventually we want to return the sum of the maximum subarray within $A[0 \ldots n]$.

### Common DP Patterns

A natural approach for this problem is to define the subproblem as a straight
recursive definition. This means we want to figure out if we can relate the
sum of the maximum subarray within $A[0 \ldots i]$ to the sum of the maximum
subarray within $A[0 \ldots i-1]$. Our dp array would contain sum of the maximum
subarray within $A[0 \ldots k] \forall \; k \in \\{ 0 \ldots n-1 \\}$. The dp
array would then be some non-decreasing series with the answer to our original
problem sitting at the end. This is a fairly common dp pattern, unfortunately
we can quickly show that our subproblem doesn't easily relate to instances of
a larger size. For example if $A[0 \ldots i-1]$ was the following array:

$$[-16, 100, 200, -1300, -500]$$

our dp array would look like:

$$[-16, 100, 300, \;\; 300 \;, \;\; 300]$$

The sum of the maximum subarray within $A[0 \ldots i-1] = 300$. This is true no
matter how many values negating the sum come after the end of the *actual* maximum
subarray $[100, 200]$ lying somewhere inside $A[0 \ldots i-1]$. Consequently we
cannot easily extend our subproblem's answer to a larger instance because we don't
know how many detrimental numbers we may be forced to accept in order to bridge the
gap between the end of the true maximum subarray, and some newly introduced number.
Put differently, there's no guarantee that the last number of the subproblem is
contained in it's maximum subarray.

In order to see how a previous subproblem can be extended, we need to know how the
last element is going to affect the element we're introducing. We need to know the
sum of the maximum subarray ending with element $A[i-1]$. Then we can introduce $A[i]$
and decide whether it is beneficial to tack on $A[i]$ to the subarray or if we'd be
better off keeping $A[i]$ separate (if previous subarray sum is negative). This
relation between subproblems and larger instances becomes more clear and our dp array
will be used to store the sum of the maximum subarray ending with element $k \; \forall \; k \in \\{ 0 \ldots n-1 \\}$.
The dp array for the above example would look like this:

$$[-16, 100, 300, -1000, -500]$$

Then when we introduce $A[i]$ we know we don't want to concatenate it to
our previous maximum subarray because whatever it's value is automatically
beats its value plus a negative number. Our dp array will then consist of
peaks and valleys. The answer to our original problem is the largest of these
peaks. We can maintain a variable whose value only gets updated when we see a
new maximum value in the dp array so we can return it right when we get to the
end.

--------