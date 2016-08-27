---
layout: post
title: Merging K Sorted Arrays and Linked Lists
category: Algorithms and Data Structures
tags: [leetcode, algorithm, recursive, divide and conquer]
---

Given the heads to $K$ sorted linked lists, return a pointer to the head of a sorted linked
list containing all elements in all $K$ lists.

There are several different ways to merge $K$ sorted contiguous arrays or linked lists, in this
post I'll be covering a method that takes advantage of a `merge()` function similar to what we use
in `mergeSort()` to merge two lists. If you are unfamiliar with the process of merging two sorted linked
lists check out my blog post [here](http://chinocode.com/Merging-Two-Sorted-Linked-Lists/) where I describe
in detail the logic behind the algorithm. For now let's assume each list has an average of $n$ elements.

## Merging $K$ sorted contiguous arrays

### Naive solution

In C++ I'll be using `vector<int>` to represent a contiguous array. One way
to merge $K$ contiguous arrays/vectors together is to create a final output
list. In this case I'll create a `vector<int> finalList` and set it equal to
the first of $K$ contiguous vectors. Then I can can set `finalList = mergeLists(secondList, finalList);`.
`finalList` will then contain all of the elements in the first $2$ of $K$ sorted
vectors in sorted order. We can continue this process for every vector of our list
of vectors. Consider the following code:

```cpp
vector<int> mergeInefficient(const vector<vector<int> >& multiList) {
  vector<int> finalList = multiList[0];
  for (int j = 1; j < multiList.size(); ++j) {
    finalList = mergeLists(multiList[j], finalList);
  }

  return finalList;
}
```

With this method we're merging in a very inefficient way. Remember that the `mergeLists()` function
requires time and space asymptotically linear to the total number of elements in both input
vectors. Since our `finalList` vector grows proportionally with each call to `mergeLists()`, our first
merge will be $O(2n)$, our second merge will be $O(3n)$, and our final merge will be $O(nk)$. Summing all
of this up gives us the following complexity:


$$\sum_{i = 2}^{k} {i \cdot n} = O(2n + 3n + 4n + 5n + ...) = O(nk^2)$$

## Merging $K$ sorted linked lists

![data plot]({{ site.baseurl }}/images/2016-8-27-Merging-K-Sorted-Arrays-and-Linked-Lists/plot.png)