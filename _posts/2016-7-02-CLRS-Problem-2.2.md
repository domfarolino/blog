---
layout: post
title: CLRS Problem 2.2
category: Algorithms and Data Structures
tags: [proofs, clrs, algorithm, sorting]
---

2-2 Correctness of bubblesort

# Correctness of bubblesort

> Bubblesort is a popular, but
> inefficient, sorting algorithm.
> It works by repeatedly swapping
> adjacent elements that are out of
> order.

```
for i = 1 to A.length - 1
  for j = A.length downto i + 1
    if A[j] < A[j-1]
      swap(A[j], A[j-1])
```

> a.) Let $A'$ denote the output of BUBBLESORT(A).
> To prove that BUBBLESORT is correct, we need
> to prove that it terminates and that

$$A'[1] \leq A'[2] \leq \cdots \leq A'[n]$$

> where $n = A.length$. In order to show that
> BUBBLESORT actually sorts, what else do we need to prove?

> b.) State precisely a loop invariant for the for loop in
> lines 2–4, and prove that this loop invariant holds. Your
> proof should use the structure of the loop invariant proof
> presented in this chapter.

> c.) Using the termination condition of the loop invariant
> proved in part (b), state a loop invariant for the for loop
> in lines 1–4 that will allow you to prove inequality (2.3).
> Your proof should use the structure of the loop invariant proof
> presented in this chapter.

> d.) What is the worst-case running time of bubblesort? How does
> it compare to the running time of insertion sort?

# 1.) What else do we need to prove?

We need to prove that

$$A'[1] \leq A'[2] \leq \cdots \leq A'[n]$$

contains the same elements in $A$, but possibly in a different order.

# 2.) Inner loop invariant

At the **beginning** of every inner loop iteration, $A[j]$ will be the
smallest element in the subarray $A[j, \cdots]$ and $A[j, \cdots]$ will
consist of the original elements of $A[j, \cdots]$ possibly in a different order.
Put differently, at the **end** of every inner loop iteration $A[j-1]$ will be the smallest
element in the subarray $A[j-1, \cdots]$ and $A[j-1, \cdots]$ will consist of the original
elements of $A[j-1, \cdots]$, possibly in a different order.

## Initialization

Initialization trivially holds, as the subarray $A[j, \cdots]$ consists only
of a single element (the very last of array $A$). $A[j]$ is therefore the smallest
element of this array.

## Maintenance

Every iteration we introduce $A[j-1]$ to the subarray $A[j, \cdots]$, lengthening
it by $1$. We swap $A[j]$ with $A[j-1]$ if $A[j]$ is the smaller of the two. At the
end of every iteration, $A[j-1]$ consists of the smallest element in the subarray $A[j-1, \cdots]$.

## Termination

The inner loop terminates when $j = i$. This suggests that after the loop terminates, $A[i]$
is the smallest element in the subarray $A[i, \cdots]$ and $A[i, \cdots]$ consists of the original
elements in $A[i, \cdots]$, possibly in a different order.

# 3.) Outer loop invariant

Using the termination condition of the inner loop invariant, state the loop invariant for the outer loop.

At the **beginning** of each loop iteration, the subarray $A[1, \cdots, i-1]$ is in sorted order and consists only
of elements smaller than those in the subarray $A[i, \cdots]$.

## Initialization

Initialization trivially holds, as the subarray $A[1, \cdots, i-1]$ is empty which by definition is in sorted order.

## Maintenance

Due to the inner loop invariant, $A[i]$ becomes the smallest element in the subarray $A[i, \cdots]$ and is less
than or equal to all of the elements in the subarray $A[i+1, \cdots]$.

## Termination

The outer loop terminates when $i = A.length$. This suggests that the subarray
$A[1, \cdots, i-1]$ is in sorted order, where each element is less than or equal to
elements in the subarray $A[i, \cdots]$ which only consists of the final element when
we substitute $A.length \text{ for } i$. Thus, the array $A[1, \cdots, A.length]$ is sorted.

# 4.) Worst case running time of bubblesort

The number of comparisons bubblesort will make is:

$$\sum_{i=1}^{n-1} {n - i} = \frac{n(n-1)}{2}$$

Bubblesort will also make at most this many swaps. This makes the worst case of bubblesort $O(n^2)$, the
same as insertion sort. Ideally, we could make the best case of bubblesort $\Theta(n)$ by creating some boolean
flag that tells us whether we swapped any values after the first iteration of the outer loop. If no swaps occur,
we can duck out early.
