---
layout: post
title: "Binary Search: Useful Variants"
category: Algorithms and Data Structures
tags: [algorithm]
---

Binary search is one of the most fundamental algorithms in computer science, and often one of the
earliest-taught "algorithms" in University (depending on major, of course). Alone, binary search lets
us determine the position of some key in a sorted dataset. If the key doesn't exist, ideally it will
return some sentinel value that will tell us that. So in short:

```
in  => (sortedArray, key)
out => position | -1
```

A classic implementation binary search in C++ is as follows:
<script src="https://gist.github.com/domfarolino/b9d372564fc1adc29ad9da79e9880144.js"></script>

We set up our left and right bound index variables, and while they are not twisted around, we keep
zeroing in on the element we're trying to find. Note, for an input array with no elements, the `left`
and `right` variables are twisted around by default, and thus the loop will not be entered.

## Limitations & Useful Variants

Even the return value of binary search is quite binary, in that either get an index of the value we
were searching for, or we get something telling us it wasn't there. Furthermore, if the dataset contains
duplicates, we'll get an index of a random occurrence of the one we're searching for. Often this is fine,
but in a lot of competitive programming questions, it would be nice to handle duplicates in a way that is
more useful, and perhaps get information like the index of:

 - The first occurrence of a value
 - The last occurrence of a value

... or in the case where the value we're searching for doesn't exist (DNE), the index of the:

 - Closest value
 - Next largest value
 - Next smallest value

... or some combination of the above sections! The rest of this article will be focused on building
useful variants of the classic binary search, and discussing some of the logic and intuition behind them.

## First Occurrence

Binary search typically stops immediately when `arr[m]` is the value we're looking for. In this variant, we
want to modify the logic hit when we find an occurrence, and instead of _immediately_ returning, we want to
search for _earlier_ occurrences that may appear in the range `[left, mid]`. If we're interested in the range
`[left, mid]` (inclusive), we can just set `right = mid` Doing this (instead of `right = mid - 1`) includes the
occurrence we just found, so in the case:

 - All values in the range `[left, mid - 1]` happen to be less < `arr[mid]`, we don't lose our occurrence
 - There is an earlier occurrence in the range `[left, mid - 1]`, we'll find it with the rest of our logic

It's important to know when to stop searching though; for example, how do we know we've found the _earliest_
occurrence of a value? Well, when `mid == 1eft` (after we've found _one_ occurrence), there is no earlier range
to search through, so at this point, `arr[mid]` must be the earliest occurrence, and we'll return it!

<script src="https://gist.github.com/domfarolino/193314a82e385796f8cc6f8ff814098f.js"></script>

## Last Occurrence

We're going to use a similar tactic as we did above to find the last occurrence of a value appearing in a list
with potential duplicates, bringing one of the boundary index variables up to `mid` (inclusive) when we've found
`value`, to give the other half of the list a fair chance of producing another occurrence. In the last example, we
check to see if we have room in the lower half to check for earlier occurrences (if we've found the value we're
looking for) with:

```cpp
if (left != mid) { //...
```

... and set `right = mid`.

It's tempting for this variant, to add `if (mid != right)` to see if we have room in the second half of the list to check
for _later_ occurrences, and then set `left = mid`, but consider the array `[1, 1]`. Our index variables are initialized as
`left = 0, right = 1` and then `mid` will be set to `0`. If we simply set `left = mid` in this case, we're never changing our
bounds, and we enter an infinite loop. This is because the only time `mid == right` is when all index variables are pointing to
the same element.

So before instead of checking `mid != right`, we should be checking `left != mid` again. If `left != mid`, we can set `left = mid`
safely. Like last time though, we need a "base case" telling us when to stop adjusting our bounds (when we've found the occurrence
we're looking for). In this case, if `left == mid`, we know the occurrence we're looking for is either at index `left` or `mid`. Well,
it's definitely at `left`, but to give our second half a chance to produce another occurrence, we defer to `right` if it has an occurrence.

<script src="https://gist.github.com/domfarolino/35b105314530e638ea173dc23f18255e.js"></script>

Note this also works for the single element case, in which case `left == right`, so when we dump into our new logic and give `right` preference,
we return the correct value.
