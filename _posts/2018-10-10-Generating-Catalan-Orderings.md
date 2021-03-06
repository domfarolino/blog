---
layout: post
title: Generating Catalan Orderings
category: Algorithms and Data Structures
tags: [algorithm, combinatorics, recursion]
---

This is a super cool problem. It started as a modified version of the following two problems:

 1. [Optimal Division](https://leetcode.com/problems/optimal-division/description/)
 2. [Generate Parentheses](https://leetcode.com/problems/generate-parentheses/description/)

...and then I realized what I was trying to accomplish was actually a real thing
(it was bound to happen). The problem at hand: given _n_ terms, return a list of strings enumerating all
possible ways to group the terms with parentheses. We're assuming that all terms need to be grouped.
In other words, "1234" is not a valid grouping but "(((12)3)4)" is. (Note, the two would be considered
identical if operators of the same precedence were applied between all terms, because left-most parentheses
are redundant, given the order in which same-precedent operators are evaluated naturally). Equivalently, we
can also accept "((12)3)4", which is the same, just without parentheses on the outer-most level ...whatevs.

To get a better feel for this likely-recursive problem, let's check out some simple examples/base cases:

----

With a single term, $Groupings(1)$ has a single base grouping:

 - 1

With two terms [1, 2], $Groupings(2)$ also has a single grouping:

 - 12 (alternatively (12) would be fine too, it doesn't matter too much for the base cases)

With three terms [1, 2, 3], $Groupings(3)$ has some more options:

 - 1(23)
 - (12)3

With four terms [1, 2, 3, 4], $Groupings(4)$ has even more!

 - 1(2(34))
 - 1((23)4)
 - (12)(34)
 - (1(23))4
 - ((12)3)4

----

In that last example, we can observe that the problem can be broken down into smaller subproblems,
lending itself nicely to a recursive solution. For example, the reason $Groupings(3)$ had two groupings
is because we had two ways to choose our first group. We see a similar problem when looking at $Groupings(4)$.
We can choose to hold 1 as our first left group:

```
|-This will be our first group
|
|   |- This will be our second group
| __v__
v |   |
1 2 3 4
```

...effectively breaking the terms into two groups, and our problem into two subproblems, of which the total number
of groupings will be $Groupings(1) \times Groupings(3)$. Therefore, with 1 held as our first group, 1(234) has
two possible groupings:

 - 1(2(34))
 - 1((23)4)

Ah, now that we've exhausted the number of groupings possible with 1 held as the first group, let's expand
our first group to include 12. With this, we have $Groupings(2) \times Groupings(2)$ possibilities. Finally, we can expand
our first group to include 123, providing $Groupings(3) \times Groupings(1)$ more groupings. In total, this gives us:

$$ Groupings(1) \times Groupings(3) + Groupings(2) \times Groupings(2) + Groupings(3) \times Groupings(1) $$

...total groupings. We can see the general trend here is:

$$\sum_{k=1}^{n-1} {Groupings(k) \times Groupings(n - k)}$$

It turns out this [formula](http://people.math.sc.edu/howard/Classes/554b/catalan.pdf) is the formula for the
[Catalan numbers](https://en.wikipedia.org/wiki/Catalan_number), a sequence appearing in a lot of recursive
combinatorial situations! It's pretty cool.

# Implementation

As far as an implementation for this problem goes, we basically have two options:

 1. Output the _number_ of possible parentheses orderings of _n_ terms
 2. Output all of the possible parentheses orderings of _n_ terms

We see these choices in a lot of combinatorial situations. Carrying out (1) is much easier and often requires
much less work, since we don't actually have to enumerate over everything (+ even less so when there is a closed
form solution). We can see it as a dynamic programming, problem in which we build a table of the Catalan numbers
from bottom (base case) ⇒ top (n), eventually stopping when we obtain $Catalan(n)$, the nth Catalan number. Of
course we could actually just implement the closed formula too, but doing that won't learn ya nuthin' (not true,
if you read the proof it's actually a wild ride).

## Complexity analysis (1)

 - Time complexity: $O(n^2)$ (or $O(1)$ if we implement the closed form solution)
 - Space complexity: $O(n)$

However, the problem we _actually_ set out is (2), which requires enumerating through all $Catalan(n)$ of
the possible groupings, since we need to produce them all. In
[my implementation](https://github.com/domfarolino/algorithms/tree/master/src/algorithm_practice/Combinatorics/catalanSequence/catalan.cpp),
I store all of them and print them out at the end.

It can be seen as a divide-and-conquer sorta problem where we consider each possible group split, breaking the
original sequence of terms into two subsequences, $Catalan(k) \times Catalan(n - k)$. From here we can recurse, producing
two lists of strings of the recursive solutions. At the "combine" stage, we essentially take the cartesian product
of the two lists to form and return a larger list of longer strings. We just have to be careful to put parentheses
in the right spot and what not. See
[the implementation](https://github.com/domfarolino/algorithms/tree/master/src/algorithm_practice/Combinatorics/catalanSequence/catalan.cpp)
for more!

## Complexity analysis (2)

 - Time complexity: $O(Catalan(n))$
 - Space complexity $O(Catalan(n))$
