---
layout: post
title: "Binary Search: Useful Variants"
category: Algorithms and Data Structures
tags: [algorithm]
---

Binary search is one of the most fundamental algorithms in computer science, and often one of the
earliest taught algorithms in University (depending on major, of course). Alone, binary search lets
us _quickly_ determine the index of some value in a sorted dataset. If the value doesn't exist, ideally
it will return some sentinel value indicative of that. So in short:

```
In: (sortedArray, value)
Out: position | -1
```

A classic implementation binary search in C++ is as follows:
<script src="https://gist.github.com/domfarolino/b9d372564fc1adc29ad9da79e9880144.js"></script>

We set up our left and right bound index variables, and while they are not inverted, we keep zeroing
in on the element we're trying to find. Note, for an input array with no elements, the `left` and `right`
variables are inverted by default, and thus the `while` loop will not be entered.

<a name="limitations-&-useful-variants">
## Limitations & Useful Variants

Even the return value of binary search is quite binary, in that we either get the index of the value we're
searching for, or something telling us it wasn't there. Furthermore, if the dataset contains duplicates,
we get an index of a random occurrence of the one we're searching for. Often this is fine, but in a lot
of competitive programming questions, it would be nice to handle duplicates in a way that is more useful,
and perhaps get information like the index of:

 - The first occurrence of a value
 - The last occurrence of a value

... or in the case where the value we're searching for doesn't exist (DNE), the index of the:

 - Closest value
 - Next largest value
 - Next smallest value

... or some combination of the above sections! The rest of this article will be focused on building
useful variants of the classic binary search, and discussing some of the logic and intuition behind them.

<a name="first-occurrence">
## First Occurrence

Binary search typically stops immediately when `arr[mid]` is the value we're looking for. In this variant, we
want to modify the logic hit when we find an occurrence to not _immediately_ return the index of `mid`, but to
instead begin searching for _earlier_ occurrences that may appear in the range `[left, mid]`. If we're interested
in the range `[left, mid]` (inclusive) after we find one occurrence, we can just set `right = mid` to keep this
search up. Setting `right = mid` (instead of `right = mid - 1`) includes the occurrence we just found when searching
our "lower half", so in the following possible cases:

 - All values in the range `[left, mid - 1]` happen to be less < `arr[mid]`, we don't lose the occurrence we found
 - There is an earlier occurrence in the range `[left, mid - 1]`, we'll find it with the rest of our logic

It's important to know when to stop searching though; for example, how do we know when we've found the _earliest_
occurrence of a value? Well, when `mid == 1eft` (after we've found an occurrence), there is no earlier range to search
through, because our "lower half" is effectively gone, so at this point, `arr[mid]` must be the earliest occurrence, and
we'll return it!

<script src="https://gist.github.com/domfarolino/193314a82e385796f8cc6f8ff814098f.js"></script>

<a name="last-occurrence">
## Last Occurrence

We're going to use a similar tactic as we did above to find the last occurrence of a value appearing in a list
potential duplicates, bringing the lower boundary index variable up to `mid` (inclusive) when we've found
an occurrence, to give the second half of the list a fair chance of producing another occurrence. In the last example,
we check to see if we have room in the lower half to check for earlier occurrences (once we've found the value we're
looking for) with:

```cpp
if (left != mid) { //...
```

... and set `right = mid` if that condition is true.

For this variant it's tempting to use `if (mid != right)` to see if we have room in the second half of out list to check for
_later_ occurrences, and then set `left = mid` if so, but consider the array `[1, 1]`. Our index variables are initialized as
`left = 0, right = 1` and then `mid` will be set to `0`. Here it's true that `mid != right`, which may indicate that we have room
in our second half, but if we set `left = mid` in this case, we're never changing our bounds, and we enter an infinite loop. This
is because the only time `mid == right` is when all index variables are pointing to the same element, making the check `mid != right`
not indicative of a viable second half.

Instead we should be checking `left != mid` again. If `left != mid`, we can set `left = mid` safely and search our second half, but if
`left == mid`, our boundaries consist of either one or two elements. We know the occurrence we're looking for is definitely at index `left`
(also `mid`), and that the only later place it could appear is `right`. If the list bounds contain:

 - Two elements at this point, we'll give preference to `right` and see if it is a later occurrence
 - One element at this point, giving preference to `right` is the same as returning `left` or `mid`

So if `left == mid`, we can essentially perform `return (arr[right] == value) ? right : left`.

<script src="https://gist.github.com/domfarolino/35b105314530e638ea173dc23f18255e.js"></script>

<a name="closest-value">
## Closest Value

Finding the index of the closest value if the value we're searching for DNE is actually fairly simple. Consider the case in which
the value we are attempting to find DNE. Here we'll never be returning from within our `while` loop, and will eventually be thrown
outside of it. We know that once we're thrown outside of the loop, our `left` and `right` index variables must be inverted.
This can happen in one of the following ways:

 - All values in the array are greater than what we're looking for, in which `right = -1`, `left = 0`
 - All values in the array are lower than what we're looking for, in which `left = arr.size()`, `right = arr.size() - 1`
 - We just couldn't find it somewhere in the middle; `left` and `right` are both safe indices, just inverted

The first two are easy to check for, and save us from returning an invalid index, since only `left` or `right` can be valid
and thus safe to return. The final case means the value we were searching for would appear between indices `left` and `right`,
which leaves us in the situation of needing to test which value is closest to the one we're looking for.

<script src="https://gist.github.com/domfarolino/47eaec6e12d2c63cf30cc7f5eaecd143.js"></script>

<a name="next-largest-&-next-smallest">
## Next Largest & Next Smallest

I'll leave these as an exercise to the reader, since they're simple variants of the already-simple [Closest Value](#closest-value) variant.
To see some of these algorithms in actions, check out the
[Searching and Sorting](https://github.com/domfarolino/algorithms/tree/master/src/algorithm_practice/Searching_And_Sorting) folder of my
[domfarolino/algorithms](https://github.com/domfarolino/algorithms) repository on GitHub :).
