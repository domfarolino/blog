---
layout: post
title: CLRS - Problem 2.1 Modified Merge Sort
---

2-1 Insertion sort on small arrays in merge sort

> Although merge sort runs in $O(nlog(n))$ worst-case time and insertion sort runs
> in $O(n^2)$ worst-case time, the constant factors in insertion sort can make it faster
> in practice for small problem sizes on many machines. Thus, it makes sense to
> coarsen the leaves of the recursion by using insertion sort within merge sort when
> subproblems become sufficiently small. Consider a modification to merge sort in
> which $\frac{n}{k}$ sublists of length $k$ are sorted using insertion sort and then merged
> using the standard merging mechanism, where $k$ is a value to be determined.

# a.) Show that insertion sort can sort the $\frac{n}{k}$ sublists, each of length $k$, in $O(nk)$.
Insertion sort can sort a $k$ size sublist in $k^2$. We'll be doing this $\frac{n}{k}$ times.

$$(k^2)(\frac{n}{k}) = (nk) = O(nk)$$

# b.) Show how to merge the sublists in $O(nlog(\frac{n}{k}))$ worst-case time.

There are many ways look at this problem. I'll start with a few ways I found to logically
come about the answer and then follow up with a formal proof.

Let's briefly go over why merge sort takes $O(nlog(n))$ time. Here's a quick table outlining the recursion
tree produced by mergesort.

$$
\begin{array}{c||lcr}
\text{# of elements/array} & \text{# of arrays} \\\
\hline
n & 1 \\\
\hline
\frac{n}{2} & 2 \\\
\hline
\frac{n}{4} & 4 \\\
\hline
\dotsb & \dotsb \\\
\hline
1 & n \\\
\end{array}
$$

Note there are always $n$ elements at play, we just have to merge them $log(n)$ times starting at the deepest level
of the tree until we get one unified array. The point of the improved merge sort is to stop us from going all the way to the bottom of
the recursion tree where we have to merge $n$, $1$ element arrays. Instead we want to stop when the input array gets some size $k$, and
merge the $\frac{n}{k}$ arrays after we run insertion sort on them.

The goal is not to go through all $log(n)$ levels of recursion. Instead
we want to [stop short](https://www.youtube.com/watch?v=IzkEFWrMVys) when the length of an input array
reaches some size $k$.

At some point we'll get $\frac{n}{k}$ subarrays of length $k$. These subarrays will be the leaves of the
recursion tree. We need to figure out how many levels

This means all of the levels of recursion in our table that appear under our stopping level will not be necessary. The total number of levels
is $log(n)$ and the total number of levels under *and including* the level we wish to stop at is $log(k)$. This means the number of levels we
wish to ignore is:

$$log(n) - log(k) + 1 = log(\frac{n}{k}) + 1$$