---
layout: post
title: Merging Two Sorted Linked Lists
category: Algorithms and Data Structures
tags: [leetcode, algorithm, recursive, divide and conquer]
---

Given the heads to two sorted linked lists, return a pointer to the head of a sorted linked
list containing all elements in both lists.

While this problem seems/is fairly straight forward, we can document some interesting
implementation quirks and complexity differences we run into when working with linked
data structures vs contiguous arrays.

## Merging 2 Sorted Arrays

Let's look at a simpler version of the same problem. When faced with two sorted arrays
most people take advantage of the `merge()` subroutine in classic `mergeSort()` to merge
them together in $O(n+m)$ time and $O(n+m)$ space, where $n$ and $m$ are the number of elements
in each input array.

Merging two sorted arrays has a simple enough implementation due to the fact that we need space
asymptotically linear to the total number of elements. Our space requirement gives us the tradeoff
of easy implementation because we never modify the input arrays. Instead we just follow the colloquially
named "two finger algorithm" (not [Floyd's cycle detection](http://math.mit.edu/~rpeng/18434/cycleDetection.pdf)),
iterating through each input array starting at the beginning and taking the smaller of the two values we're assessing
and **copying** it to our final array. Once we do this, we advance foward in the array we took the value from and continue.
The input array that has values left over once we exhaust the other gets its remaining elements **copied** one-by-one and
committed to the final list.

## Merging 2 Sorted Linked Lists

Merging two sorted linked lists differs in implementation and complexity. For one, we can merge two sorted linked lists
in $O(1)$ space. This is because we're working with pointers as opposed to elements in contiguous memory. When working
with pointers we can make structural changes that affect $O(n)$ elements in $O(1)$ time, and consequently we can merge two
sorted linked lists in-place, and turn both input lists into one sorted list while returning a pointer to its head. Let's
take a closer look at how we'd do this.

```cpp
// Sample list node
struct Node {
  int data;
  Node* next;
  Node(int data, Node* next = NULL) : data(data), next(next) { }
};
```

To merge two sorted linked lists, we need to maintain pointers to the current list nodes we're looking at so we can compare their
values and modify the list. Let's say we have two pointers `aCurr` and `bCurr`. We want to choose the smaller of the two to focus
on. Either `aCurr->data <= bCurr->data` or `bCurr->data < aCurr->data`, and this will be our first conditional `while` we're able
to traverse the lists. In this example the former is true, and we need to figure out what should come after `aCurr`.

![list01]({{ site.baseurl }}/images/2016-8-25-Merging-Two-Sorted-Linked-Lists/list01.png)

The next two smallest nodes are `aCurr->next` and `bCurr`. As it stands, `aCurr`'s next is by default pointing to `aCurr->next` so it
makes sense that we would set `aCurr->next = bCurr` if `bCurr` is attractive enough, namely if it is strictly less than what `aCurr`
already points to. Since `bCurr->data < aCurr->next->data` we set `aCurr->next = bCurr`.

![list02]({{ site.baseurl }}/images/2016-8-25-Merging-Two-Sorted-Linked-Lists/list02.png)

Note how we've lost contact with the rest of the top list. Since this is not a contiguous array we can't just index
forward to *increment* `aCurr`. Instead, right away we need to keep some pointer to the next node in the event we overwrite
`aCurr->next`. Once we're done with an iteration we can then *increment* our current node by setting it equal to the pointer we kept.

![list03]({{ site.baseurl }}/images/2016-8-25-Merging-Two-Sorted-Linked-Lists/list03.png)

Let's go back and consider the case in which `bCurr->data < aCurr->data`. Of course the next two smallest nodes are `bCurr->next` and `aCurr`
so we need to figure out which one should come after `bCurr`. As it stands, `bCurr`'s next is obviously pointing to `bCurr->next`.

![list04]({{ site.baseurl }}/images/2016-8-25-Merging-Two-Sorted-Linked-Lists/list04.png)

You might think we should only set `bCurr->next = aCurr` if `aCurr->data` is strictly less than `bCurr->next->data` otherwise
we shouldn't bother but that actually leads to an insidious bug. Let's move forward with this logic to see why it is wrong.

![list05]({{ site.baseurl }}/images/2016-8-25-Merging-Two-Sorted-Linked-Lists/list05.png)

Since we just continued to the next iteration, we're back at the case where `aCurr->data <= bCurr->data` so we are focusing on
`aCurr` and figuring out what comes next. As with our logic earlier, we'll *only* set `aCurr->next = bCurr` if `bCurr` makes a compelling
enough case (namely it is strictly less than the current `aCurr->next`). Since 6 < 80, we set `aCurr->next = bCurr` and *increment* `aCurr`.

![list06]({{ site.baseurl }}/images/2016-8-25-Merging-Two-Sorted-Linked-Lists/list06.png)

Therein lies the problem. Nothing was ever pointing to the old `aCurr` and it was not designated to be the head of the entire list, so it
is lost in memory limbo only to become a leak. Instead, something needs to give. If we decide to be selfish when focusing on `aCurr`, in that
we only set `aCurr->next = bCurr` if `bCurr->data < aCurr->next->data`, then we need to be more lenient when focusing on `bCurr`. If the value in
`aCurr` $\leq$ `bCurr->next` we need to set `bCurr->next = aCurr` so that the logic is handled properly in the next iteration.

See the code below for the full implementation of this algorithm:

<script src="https://gist.github.com/domfarolino/6482c4f902e0886b130e332d226271d3.js"></script>