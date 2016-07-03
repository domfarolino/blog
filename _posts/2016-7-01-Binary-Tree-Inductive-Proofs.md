---
layout: post
title: Binary Tree Inductive Proofs
category: Algorithms and Data Structures
tags: [induction, proofs, binary tree]
---

The subject of binary trees provides a lot of variation, mainly in the number of ways in which they can be classified.
This in turn provides an array of inductive proofs that can be applied differently dependent on your input data. This
post is intended to cover some of the variations of binary trees and neat proofs relating to their number of nodes ($N$),
number of internal nodes ($I$), number of leaves ($L$), and height ($H$).

# Preface

Before we continue with the specifics of binary trees let's make sure we've got some accurate fundamental definitions.

### Height of a node:

> The height of a node is the length of the
> longest downward path from that node to a leaf.
> The height of the root is the height of the tree.

Based off of this definition, we can generalize the
height as the number of edges between a given node
and its deepest descendant. Trivial graph theory says
a path with $n+1$ nodes has $n$ edges. This makes the
height of a tree with $1$ node $H = 0$.

### Depth:

> The depth of a node is the number of edges from
> the node to the tree's root node. A root node will
> have a depth of 0.

# Categories of Binary Trees

Often binary trees can be classified as at least one of the following:

 - Full (sometimes called proper or strictly) binary tree
 - Complete binary tree
 - Perfect binary tree

Full trees are given the following definition:
> a binary tree T is full if
> each node is either a leaf or
> possesses exactly two child nodes

This definition provides fairly loose restrictions on the tree allowing
it to take many shapes. One example of a full tree would be this:

![full binary tree]({{ site.baseurl }}/images/2016-7-01-Binary-Tree-Inductive-Proofs/full.png)

Complete trees are given the following definition:
> a binary tree T with n
> levels is complete if all
> levels except possibly the
> last are completely full, and
> the last level has all its nodes
> to the left side

This definition is more strict than the
definition of a full tree, making all complete
trees, *also* full trees. One example of a complete
tree would be this:

![complete binary tree]({{ site.baseurl }}/images/2016-7-01-Binary-Tree-Inductive-Proofs/complete.png)

Perfect trees are given the following definition:
> A perfect binary tree is a binary
> tree in which all interior nodes
> have two children and all leaves
> have the same depth or same level

This definition provides very rigid restrictions. This
means all perfect trees will be shaped like the following:

![perfect binary tree]({{ site.baseurl }}/images/2016-7-01-Binary-Tree-Inductive-Proofs/perfect.png)

There is some hierarchy here. As you might be able to see,
any perfect also meets the requirements of both complete and full
trees and complete trees meet the requirements of full trees.
Full trees are just full, or sometimes called "strictly" binary trees.

# Full Binary Tree Theorem

Since full trees have loosest restrictions put on them, just about
anything we prove for a full tree is automatically proved for a complete
and perfect tree so we'll start here with the **Full Binary Tree Theorem**.

Luckily for us, the restriction put on full binary trees (that all internal nodes
must have exactly two children) means that no matter what the shape of the tree is,
the relationships between the number of nodes $N$, leaves $L$, and internal nodes $I$
remain the same. If you are given any one of those values, you can easily find the other
two. The following proofs make up the **Full Binary Tree Theorem**.

## 1.) The number of leaves $L$ in a full binary tree is one more than the number of internal nodes $I$

We can prove $L = I + 1$ by induction.

### Base case

A tree with $0$ internal nodes has $1$ leaf node.
A tree with $1$ internal node has $2$ leaf nodes.
These base cases prove the theorem for $I = 0$ & $I = 1$.

### Inductive hypothesis

Let's assume that any full binary tree with $I$ internal nodes has $I+1$ leaves.

### Inductive step

Prove that any full binary tree wtih $I+1$ internal nodes has $I+2$ leaves.

Let $T$ be a full binary tree with $I+1$ internal nodes. $T$
has at least one internal node so it must have at least two leaves.
Select an internal node $i$ whose depth is maximal, let's say $h$. Being
internal $i$ must have two children at depth $h+1$, which is greater than
$h$ so $i$'s children cannot be internal (or else $i$ would not be an internal
node at maximal height) so they must be leaves. Remove $i$'s children.
Node $i$ was an internal node but is now a leaf. The resulting tree is a full binary
tree with $I$ internal nodes which by the inductive hypothesis contains $L = I + 1$ leaves.
Adding $i$'s children back to $i$ makes it an internal node again increasing the number of internal
nodes from $I \Rightarrow I+1$ and the number of leaves from $I+1 \Rightarrow I+2$. This clearly equals
$(I + 1) + 1$ thus proving by induction that the number of leaves for any full binary tree with $I$ internal
nodes is $I+1 \quad \blacksquare$.

## 2.) The total number of nodes $N$ in a full binary tree with $I$ internal nodes is $N = 2I + 1$

Using the previous proof, we intuitively know that the number of leaves in
a tree with $I$ internal nodes is $I+1$, therefore the total number of nodes
$N$ in a tree with $I$ internal nodes is $I + I + 2 = 2I + 1$.

We can also *prove* this by induction without using any other proofs.

### Base case

A tree with $0$ internal nodes has $1$ node altogether.
A tree with $1$ internal node has $3$ nodes altogether.
Base case proves the theorem for $I = 0$ & $I = 1$.

### Inductive hypothesis

Let's assume that any full binary tree with $I$ internal nodes has $2I+1$ total nodes.

### Inductive step

Prove that any full binary tree wtih $I+1$ internal nodes has $2(I + 1) + 1$ leaves.

The following proof will have similar structure to the previous one however I
am using a different method to select an internal node with two child leaves.

Let $T$ be a full binary tree with $I+1$ internal nodes. $T$
has at least one internal node so it must have at least two leaves.
Select a leaf $v$ at maximal depth so that $v$'s sibling is also a leaf
at maximal depth. Remove $v$ and its sibling, and let $T′$ be the resulting
tree. The parent of $v$ and its sibling is an internal node in $T$ but a leaf
in $T'$. $T'$ is a full binary tree with $I$ internal nodes which by the inductive
hypothesis must contain $N = 2I + 1$ total nodes. Adding $v$ and $v$'s sibling back
to their parent gives us $I+1$ internal nodes and $(2I + 1) + 2 = 2I + 3$ total nodes
which clearly equals $2(I+1) + 1$.

## 3.) The total number of nodes $N$ in a full binary tree with $L$ leaves is $N = 2L - 1$

We can prove $N = 2L - 1$ (or $N = L + L - 1$) by induction.

### Base case

A tree with $1$ leaf has $1$ node altogether.
A tree with $2$ leaves has $3$ nodes altogether.
Base case proves the theorem for $L = 1$ & $L = 2$.

### Inductive hypothesis

Let's assume that any full binary tree with $L$ leaves has $N = 2L - 1$ total nodes.

### Inductive step

Prove that all any full binary tree with $L+1$ leaves has $N = 2(L+1) - 1$ total nodes.

Let $T$ be a full binary tree with $L+1$ leaves. Select a leaf $v$ at maximal depth such
that $v$'s sibling is also a leaf at maximal depth. Remove both $v$ and $v$'s sibling and let
$T'$ be the resulting tree. $T'$ is a full binary tree with $L$ leaves which by our inductive
hypothesis must have $2L - 1$ total nodes $N$. Note $v$'s parent is now a leaf in tree $T'$.
Add $v$ and $v$'s sibling back to their parent and notice the parent becomes an internal node
thus the tree loses one leaf, but gains another two (that we added). The number of leaves increased by a total
of $1 \text{ from } L \Rightarrow L+1$ while the number of nodes increased by two $2L - 1 + 2 = 2L + 1$.
This clearly equals $2(L+1) - 1$ thus proving by induction that the number of total nodes $N$ in any full
binary tree with $L$ leaves is $2L - 1 \quad \blacksquare$.

---

We've successfully proved for all full trees, the relationships:

 - The number of leaves $L$ in a full binary tree is one more than the number of internal nodes $I$
 - The total number of nodes $N$ in a full binary tree with $I$ internal nodes is $N = 2I + 1$
 - The total number of nodes $N$ in a full binary tree with $L$ leaves is $N = 2L - 1$

Note that if each of these relationships is true, then solving for the other variable must yield a
true equation. Thus we can create $6$ proofs from these $3$. Also note that my solutions are
not the only way to solve these, some may find splitting a tree at the root node into two
subtrees as a more natural way to show that a tree with some qualities is an extension of a tree with
some smaller qualities. As an added example I'll provide one such proof based on splitting a larger binary tree.

## #3 Alternative proof based on tree splitting

### Base case

A tree with $1$ leaf has $1$ node altogether.
A tree with $2$ leaves has $3$ nodes altogether.
Base case proves the theorem for $L = 1$ & $L = 2$.

### Strong inductive hypothesis

Let's assume that any full binary tree with $L$ leaves has $N = 2L - 1$ total nodes for all $0 \leq L \leq K$.

### Inductive step

Let $T$ be a full binary tree with $K+1$ leaves. Seeing how $T$ is
a full binary tree, its subtrees $T\_{left}$ and $T\_{right}$ must have
at least $1$ leaf each. This means $T\_{left}$ and $T\_{right}$ each have a number
of leaves $< K+1$. Call these numbers $L\_{left}$ and $L\_{right}$ respectively.
By our strong IH, they must have $2(L\_{left}) - 1$ and $2(L\_{right}) - 1$ total
nodes respectively. $T$ therefore has a number of nodes equal to the sum of the
number of nodes in each subtree plus its root which appears outside of either subtree.
Since the only node not appearing in either of $T$'s subtree is an internal node and
not a leaf, the number of leaves $K+1$ in $T$ is equal to $L\_{left} + L\_{right}$.

$$2(L\_{left}) - 1 + 2(L\_{right}) - 1 + 1 = 2(L\_{left} + L\_{right}) - 2 + 1 = 2(K+1) - 1 = 2(L+1) - 1$$

Thus proving by induction that all full binary trees with $L$ leaves have $N = 2L - 1$ total nodes for all $L \quad \blacksquare$.

# Perfect binary tree

A perfect tree which has the tightest restraints on it allows us to
introduce another variable to the relationship pool. This variable is
the height $H$ of the tree.

## Relationship between $N$ and $H$ of a perfect binary tree

If we try and calculate how many nodes $N$ exist in a perfect binary tree of height
$H$ we can tabulate the following data:

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

To get our perfect binary tree to the next height, we can make the observation
that we must add $N+1$ leaves to our current $N$ nodes. This is part of our inductive
hypothesis and we now need to prove that a tree with $N + (N + 1)$ nodes has a height
of $H+1$ or $log(N+1)$.

$$
\begin{align}
      H+1 \\\
      & = \log(N + 1 + N + 1) - 1 \\\
      & = \log(2n + 2) - 1 \\\
      & = \log(2(n + 1)) - 1 \\\
      & = \log(2) + \log(n+1) - 1 \\\
      & = 1 + \log(n+1) - 1 \\\
      & = 1 + H \quad \blacksquare \\\
  \end{align}
$$

## Relationship between $N$ and $L$ of a perfect binary tree

All binary trees have two types of nodes, internal nodes and leaves. Internal
nodes parent at least $1$ child. In perfect binary trees, internal nodes must
parent exactly $2$ children. Leaves are nodes which have exactly $0$ children.

If we try and calculate how many nodes $L$ exist in a perfect binary tree of size
$N$ we can tabulate the following data:

$$
  \boxed{
    \begin{array}{c|c}
      \text{Nodes } N & \text{Leaves } L \\\
      \hline
      1 & 1 \\\
      \hline
      3 & 2\\\
      \hline
      7 & 4\\\
      \hline
      15 & 8\\\
    \end{array}
  }
$$

The mathematical relationship we can deduce between the number of nodes in a perfect tree $N$, and
the number of leaves $L$ appears to be:

$$L = \frac{N+1}{2}$$

This means the number of leaves grows linearly with the number of total nodes. Let's prove this by induction.

## Base case

A tree with $1$ node has $\frac{1+1}{2} = 1$ leaf node.

## Assumption

Let's assume that the equation $L = \frac{N+1}{2}$ is true for all perfect trees with $N$ nodes.

## Inductive step

We can see see that the number of leaves double every time we add a new level, so we need to prove
that the number of leaves is $2L$ when we add $N+1$ nodes to our current tree.

$$
\begin{align}
      2L \\\
      & = \frac{N + 1 + N + 1}{2} \\\
      & = \frac{2(N + 1)}{2} \\\
      & = N + 1 \\\
      & = 2L \quad \blacksquare \\\
  \end{align}
$$

## Relationship between $H$ and $L$ of a perfect binary tree

This is by far the simplest and most intuitive of the proofs we'll go over. From the other
tables you've probably noticed the number of leaves double from height to height. Further,
the mathematical relationship we can deduce between the height $H$ and number of leaves $L$ is
$L = 2^H$. Let's prove it by induction.

## Base case

A tree of height $0$ should only have $1$ leafe node. $L = 2^0 = 1$.

## Assumption

Let's assume that at height $H$, a perfect binary tree will have $2^H$ leaf nodes.

## Inductive step

Prove that at height $H+1$, a perfect binary tree will have double its current leaves, or $2^{H+1}$.
Since the proof is basically just asking us to prove that $2^{H+1}$ is double $2^H$ all we have to
establish is:

$$2(2^H) = 2^{H+1} \quad \blacksquare$$

## The number of leaves in a non-empty full 

# Complete binary tree

Complete binary tree proofs coming soon

# Good resources

 - [Height of a tree with only one node](http://stackoverflow.com/questions/4065439/height-of-a-tree-with-only-one-node)
 - [Binary tree height](http://stackoverflow.com/questions/1951091/binary-tree-height)
 - [Carnegie Mellon tree lecture](https://www.cs.cmu.edu/~pattis/15-1XX/15-200/lectures/trees/lecture.html)
 - [Height of a full binary tree](http://math.stackexchange.com/questions/329166/height-of-a-full-binary-tree)
 - [Wiki Binary Tree](https://en.wikipedia.org/wiki/Binary_tree)
 - [Full binary tree theorems](http://courses.cs.vt.edu/~cs3114/Fall09/wmcquain/Notes/T03a.BinaryTreeTheorems.pdf)
