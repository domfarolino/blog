---
layout: post
title: Binary Tree Inductive Proofs
category: Algorithms and Data Structures
tags: [induction, proofs, binary tree]
---

The subject of binary trees provides a lot of variation, mainly in the number of ways in which they can be classified.
This in turn provides an array of inductive proofs that can be applied differently dependent on your input data. This
post is intended to cover some of the variations of binary trees and neat proofs relating to their number of nodes ($N$),
number of leaves ($L$), and height ($H$).

# Preface

Before we continue with the specifics of binary trees let's make sure we've got some accurate fundamental definitions.

### Height o`f a node:

> The height of a node is the length of the
> longest downward path to a leaf from that node.
> The height of the root is the height of the tree.

### Depth:

> The depth of a node is the number of edges from
> the node to the tree's root node. A root node will
> have a depth of 0.

Based off of these definitions, we can calculate the height by
counting the number of edges between a given node and its deepest
descendant. Trivial graph theory says if a path has $n$ edges then
it has $n+1$ nodes, making the heigth of a tree with $1$ root node equal
to $0$.

# Categories of Binary Trees

Often binary trees can be classified as one or more of the following

 - Perfect binary tree
 - Complete binary tree
 - Full (sometimes called proper or strictly) binary tree

# Perfect binary tree

Perfect binary trees are the simplest trees, and possess the quality that every single level
of them is completely filled to its max. Every perfect binary tree will look like this:

![perfect binary tree]({{ site.baseurl }}/images/2016-6-29-Binary-Tree-Inductive-Proofs/perfect.png)

## Relationship between $N$ and $H$ of a perfect binary tree

If we try and calculate how many nodes $N$ exist in a perfect binary tree of size $H$ we can tabulate the
following data:

$$
  \boxed{
    \begin{array}{c||cr}
      \text{Height } H & \text{Nodes } N \\\
      \hline
      0 & 1 \\\
      \hline
      1 & 3 \\\
      \hline
      2 & 7 \\\
      \hline
      3 & 15 \\\
    \end{array}
  }
$$

The mathematical relationship we can deduce between the number of nodes in a perfect tree $N$, and
it's height $H$ appears to be:

$$N = 2^{H+1} - 1$$

We can prove this relationship with induction.

### Base case

Trivial, a tree of height $0$ has $2^{0+1} - 1 = 2 - 1 = 1 \text{ node } \blacksquare$

### Assumption

Assume that for all perfect trees of height $H$, $N = 2^{H+1} - 1$.

### Inductive step

Prove that the formula works for a tree of height $H+1$, namely a tree of height $H+1$
should have $2^{H+2} - 1$ total nodes.

We can see that a tree of height $H+1$ will have $2^{H+1}$ more nodes (leaves) than a tree of
height $H$, meaning we can write the number of nodes of a tree of height $H+1$ in terms
of a tree with height $H$ as $2^{H+1} + 2^{H+1} - 1$.

$$
\begin{align}
      2^{H+2} - 1 \\\
      & = 2^{H+1} + 2^{H+1} - 1 \\\
      & = 2\cdot2^{H+1} - 1 \\\
      & = 2^{H+2} - 1 \quad \blacksquare \\\
  \end{align}
$$

This proof can also be done backwards solving for $H$ instead of $N$.

$$H = \log(N+1) - 1 \text{ where } \log \text{ is } \log_2$$

This is intuitive by looking at the table, as we can see at every level we are
one node short of making a perfect power of two. We add one ($N+1$), to create
this perfect power of two, then subtract one from $\log(N+1)$. This appears to
give us our height. 

### Base case

A tree with $1$ node has a height of $\log(1+1) - 1 = 0$.

### Assumption

Assume that for a tree with $N$ nodes, $H = \log(N+1) - 1$.

### Inductive step

To get our perfect binary tree onto the next level, we can make the observation
that we add $N+1$ leaves to our current $N$ nodes. This is part of our inductive
hypothesis and we now need to prove that a tree with $N + (N + 1)$ nodes has a height
of $H+1$.

$$
\begin{align}
      H+1 \\\
      & = \log(N + 1 + N + 1) - 1 \\\
      & = \log(2n + 2) - 1 \\\
      & = \log(2(n + 1)) - 1 \\\
      & = \log(2) + log(n+1) - 1 \\\
      & = 1 + log(n+1) - 1 \\\
      & = 1 + H \quad \blacksquare \\\
  \end{align}
$$

# Complete binary tree

Complete binary tree text here

# Full binary tree

All internal nodes are full. To add a value anywhere you'd be creating a new leaf