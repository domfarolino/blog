---
layout: post
title: Merging K Sorted Arrays and Linked Lists
category: Algorithms and Data Structures
tags: [leetcode, algorithm, recursive, divide and conquer]
---

# Problem Statements

 - Given $k$ sorted contiguous arrays return a single array containing all
of the elements in each array in sorted order.

 - Given the heads to $k$ sorted linked lists, return a pointer to the head
of a linked list containing all elements in each list in sorted order.

There are several different ways to merge $k$ sorted contiguous arrays or linked lists, in this
post I'll mainly be covering a method that takes advantage of a `merge()` function similar to what
we use in `mergeSort()` to merge two sorted lists. If you are unfamiliar with the process of merging
two sorted linked lists check out my blog post [here](https://chinocode.com/Merging-Two-Sorted-Linked-Lists/)
where I describe in detail the logic behind the algorithm. For now let's assume each list has an average of
$n$ elements.

# Naive merging

In C++ I'll be using a `vector<int>` container to represent a contiguous array and a
generic `struct` to represent a list node. One way to merge $k$ contiguous
arrays/vectors together is to create a final output vector `finalList` and set it equal
to the first of $k$ contiguous vectors. Then we can merge in each remaining vector one
at a time. To do this we set `finalList = mergeLists(secondList, finalList);` for all
vectors. Consider the following code:

```cpp
vector<int> mergeInefficient(const vector<vector<int> >& multiList) {
  vector<int> finalList = multiList[0];
  for (int j = 1; j < multiList.size(); ++j) {
    finalList = mergeLists(multiList[j], finalList);
  }

  return finalList;
}
```

The merging in this method is very inefficient. Remember that the `mergeLists()` function
requires time and space asymptotically linear to the total number of elements in both input
vectors. Since our `finalList` vector grows by $O(n)$ elements with each call to `mergeLists()`,
we're getting the worst complexity possible out of our `mergeLists()` function by using the longest
sorted vector every time. Our first merge will be $O(2n)$, our second merge will be $O(3n)$, and our
final merge will be $O(nk)$. Summing all of this up gives us the following complexity:

$$O(n) + \sum_{i = 2}^{k} {i \cdot n} = O(n) + O(2n + 3n + 4n + 5n + ...) = O(nk^2)$$


# Naive selecting

Naive selecting information here.

# Pairwise merging

The key here is to call `mergeLists()` with the smallest input vectors as possible. After
we call `mergeLists()` once, some vector has gotten $O(n)$ elements longer. Instead of calling
`mergeLists()` with this longer vector we'd be better off merging two smaller ones so that we
can spread out the increasing vector lengths more evenly. It makes sense to use up all $n$-sized
vectors while they exist, leaving us with $\frac{k}{2}$ vectors each of size $2n$. In fact this
idea of a pairwise merging is exactly how `mergeSort` gets its $O(n\log(n))$ time complexity.

![merge sort recursion tree]({{ site.baseurl }}/images/2016-8-29-Merging-K-Sorted-Arrays-and-Linked-Lists/tree.gif)

Eventually we will merge $2$ arrays of size $\frac{n}{2}$ into our final array. It is important to
realize the total number of calls to the merge function will be the same as with the previous method,
the difference lies within the sizes of the arrays we pass into the merge method. Pairwise merging
allows us to minimize the heavy lifting by only merging large arrays when there are no smaller ones
to merge. One way to look at its complexity is sum up all of the arrays we're
merging: $O(kn + k/2 \cdot 2n + k/4 \cdot 4n + ... + 2(nk/2) + 1nk)$. The total amount of work each
in each term is $nk$ because there are always $nk$ elements at play however we're only doing $nk$ work
$\log(k)$ times thus making this version of the algorithm $O(nk\log(k))$, a much more favorable time
complexity.

Iterative code simulating the divide and conquer process `mergeSort` uses can be found below:

```cpp
vector<int> mergeDivideAndConquer(vector<vector<int> > multiList) {
  int l = 0, r = multiList.size()-1;
  
  while (multiList.size() > 1) {
    multiList[l++] = mergeLists(multiList[l], multiList[r]);
    multiList.pop_back();
    
    r = multiList.size()-1;
    
    // Reset `l` when there are no more smaller lists to
    // merge (simulating new level in the recursion tree)
    if (r <= l) l = 0;
  }

  return multiList[0];
}
```

![data plot]({{ site.baseurl }}/images/2016-8-29-Merging-K-Sorted-Arrays-and-Linked-Lists/plot.png)