---
layout: post
title: CLRS Exercise 2.2-3
category: Algorithms and Data Structures
tags: [induction, proofs, clrs, algorithm]
---

2.2-3 Mathematical Induction

Use mathematical induction to show that when $n$ is a power of $2$ (namely $n = 2^k$), the following recurrence:

$$
        T(n) =
        \begin{cases}
          2           & \text{if } n = 2, \\\
          2T(n/2) + n & \text{if } n = 2^k, \text{for } k > 1
        \end{cases}
$$

$T(n) = n\log_2(n)$

We're going to want to treat this problem like a regular proof of induction and use the following outline:

 - Base case
 - Assumption
 - Inductive proof

#Base case

First off, we want to establish that our base case works.

$$T(2^1) = T(2) = 2\log_2(2) = 2 \quad \boxed{\checkmark}$$

#Assumption

Now that we've worked out our base case, let's assume that the recurrence holds true for all values of $n = 2^k$ such that

$$T(2^k) = 2^k\log_2(2^k) = 2^k(k)$$

#Inductive step

Next, we'll move onto proving that it works for $n = 2^{k+1}$ such that

$$T(2^{k+1}) = 2^{k+1}\log_2(2^{k+1}) = 2^{k+1}(k+1)$$

Proof:

$$
  \begin{align}
    T(n) = T(2^{k+1}) \\\
    & = 2(T(2^k)) + 2^{k+1} \\\
    & = 2(2^k\log_2(2^k)) + 2^{k+1} \\\
    & = 2(2^k\(k)) + 2^{k+1} \\\
    & = 2^{k+1}\cdot k + 2^{k+1} \\\
    & = 2^{k+1}(k+1) \text{ where } 2^{k+1} = n \quad \boxed{\checkmark} \\\
  \end{align}
$$

Technically, we could stop here because we've already established

$$T(2^k) = 2^x\log_2(2^x) = 2^x(x)$$

However, only one trivial step is required to put the solution back in terms of $n$

$$k+1 = \log_2(2^{k+1})$$

which leaves us with terms of only $2^{k+1}$ which are equivalent to $n$

$$
  2^{k+1}\log_2(2^{k+1})
$$

$
  = n\log_2(n)
$
