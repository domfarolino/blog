---
layout: post
title: CLRS - Problem 2.1 Modified Merge Sort
category: Algorithms and Data Structures
tags: [induction, proofs]
---

2-1 Insertion sort on small arrays in merge sort

> Although merge sort runs in $O(n\log(n))$ worst-case time and insertion sort runs
> in $O(n^2)$ worst-case time, the constant factors in insertion sort can make it faster
> in practice for small problem sizes on many machines. Thus, it makes sense to
> coarsen the leaves of the recursion by using insertion sort within merge sort when
> subproblems become sufficiently small. Consider a modification to merge sort in
> which $\frac{n}{k}$ sublists of length $k$ are sorted using insertion sort and then merged
> using the standard merging mechanism, where $k$ is a value to be determined.

# a.) Show that insertion sort can sort the $\frac{n}{k}$ sublists, each of length $k$, in $O(nk)$.
Insertion sort can sort a $k$ size sublist in $k^2$. We'll be doing this $\frac{n}{k}$ times.

$$(k^2)(\frac{n}{k}) = (nk) = O(nk)$$

# b.) Show how to merge the sublists in $O(n\log(\frac{n}{k}))$ worst-case time.

There are many ways look at this problem. I'll start with a few I found to logically
come about the answer and follow up with a formal proof.

Let's briefly go over why merge sort takes $O(n\log(n))$ time. Here's a table outlining the recursion
tree produced by mergesort given an input array whose length, for simplicity, is a power of two.
(Note this assumption does not change the actual asymptotic complexity).

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

There are always $n$ elements at play, we just have to split them up enough times to get to the trivial case, then
merge them starting at the deepest level of the tree. It may seem like we're not doing any work at the bottom level because the
actual "merging" of elements begins at the next level up, however the merging of 1 element is the base case telling us to stop and
this "test" takes $O(1)$ time. Since the base case is hit $n$ times, the amount of work at this level is directly proportional to $n$.
[Basic knowledge](#levels-induction) of binary trees tells us that a tree with $n$ leaves has $\log(n) + 1$ levels. The point of the
improved merge sort is to not go through **all** levels of recursion. Instead we want to
[stop short](https://www.youtube.com/watch?v=IzkEFWrMVys) when the length of the input array reaches some size $k$.

At this point we'll have $\frac{n}{k}$ subarrays of length $k$. These subarrays will be the final leaves of the recursion tree. We
can again apply our knowledge of binary trees to say there are $\log(\frac{n}{k}) + 1$ levels of recursion if we stop when the
input array is of length $k$. Since we still have to do "$n$" amount of work at each level this gives us $cn(\log(\frac{n}{k})+1)$ total work.
Dropping the lower order term as is customary with big-oh notation gives us the following **merge** complexity:

$$O(n\log(\frac{n}{k}))$$

## Using log rules

We [know](#levels-induction) there are $\log(n) + 1$ levels in an entire binary tree whose input
array is of length $n$. There are $\log(k) + 1$ levels underneath and including the level whose input
array is of length $k$. All of the levels beneath this one will never be touched with our modified
version of merge sort. This means we can take the total number of levels in the tree ($\log(n) + 1$)
and subtract the number of levels we'll never touch ($\log(k)$). This yields the following:

$$\log(n) + 1 - \log(k) = \log(\frac{n}{k}) + 1$$

Dropping the lower order term and accounting for the "$n$" amount of work we do at each level gives us the same complexity
calculated in the previous method.


<a name="levels-induction"></a>
## Inductive proof of number of levels in binary tree with $n$ leaves
We want to prove that the number of levels in a binary tree with $n$ leaves $\log(n) + 1$

### Base case
The base case occurs when a binary tree with one leaf node has only one level.

$$
  \begin{align}
      n = 1 \\\
      & = \\log(n) + 1 \\\
      & = 0 + 1 \text{ level} \\\
  \end{align}
$$

### Assumption
For simplicity, let's assume $n$ is a power of two such that $n = 2^i$, for $i >= 0$.
We now assume that a binary tree with $2^i$ leaf nodes has $\log(2^i) + 1$, or $i+1 levels$.

### Inductive step
Prove that a binary tree with $2^{i+1}$ leaf nodes has one more level than a binary tree with $2^i$ leaf nodes.

$$\log(2^{i+1}) = (i + 1) + 1 = i + 2 \quad\boxed{\checkmark}$$

#c.) Coming soon