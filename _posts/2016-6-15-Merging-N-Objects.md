---
layout: post
title: Combinatorics - Merging N Objects
category: Algorithms and Data Structures
tags: [combinatorics, induction, proofs]
---

I got this problem from [The Algorithm Design Manual](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.471.4772&rep=rep1&type=pdf) by Steven Skiena.
It's a fairly simple exercise in Chapter 2 that I felt had a neat symmetry, so I've decided to write a bit about it.

The problem is as states:

> Suppose you have given N companies, and we want to eventually merge
> them into one big company. How many ways are there to merge them?

Let's simplify the problem into its true state:

> How many ways can you merge N items?

Let's look at this from the ground up with N increasing as we go along:

#Two items

You can only merge $2$ items $1$ way so that $a + b = ab$

#Three items

The question here is, how many ways can we get three items merged into only
two items, since we know exactly how many ways we can merge two items. Working this
out reveals the following possibilities.

$$a + b + c = ab + c$$
$$a + b + c = ac + b$$
$$a + b + c = bc + a$$

We stopped when we got down to two items because we know there is only one way to merge two items.

#Expanding to $N$

Let's look at how many possibilities there are to reduce $N$ items down to $N-1$. We need to merge $2$ items
from our list of $N$ items, to leave us with $N-1$ items. The number of different pairs of items we can merge from $N$
is $\binom{N}{2}$ or $\frac{N(N-1)}{2}$. For each $\binom{N}{2}$, we must merge the remaining $N-1$ items out to get our sum total.
This gives us a neat little definition:

$$Merge(N) = \binom{N}{2}Merge(N-1)$$

which can more formally be written:

$$
  Merge(N) =
          \begin{cases}
            1                    & \text{if } N = 1, \\\
            \binom{N}{2}Merge(N-1)  & \forall N > 1
          \end{cases}
$$

Expanding it out gives us the following "[tail recursive](http://stackoverflow.com/a/33930/3947332)" pattern:

$$Merge(N) = \binom{N}{2}\binom{N-1}{2}\binom{N-2}{2}\dotsb\binom{2}{2}$$

This can be iteratively written down as:

$$\prod_{i=2}^N {\binom{i}{2}}$$

Alternatively, to get a $O(1)$ answer we could write it out as:

$$\prod_{i=2}^N {\frac{i(i-1)}{2}} = \frac{N!(N-1)!}{2^{n-1}}$$
