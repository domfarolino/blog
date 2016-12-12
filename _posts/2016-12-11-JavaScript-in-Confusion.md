---
layout: post
title: JavaScript - "in" Confusion
category: Web
tags: [javascript]
---

The `in` operator when used with arrays in JavaScript can be a little vexing and I'd like to clear up some of the confusion. The `in` operator is strictly used to tell whether some given property name exists *in* some object or its `[[Prototype]]` chain. So how does this work for arrays? It is tempting to think `2` is `in [1, 2]`, but this will return false.

# Why?

Arrays are really just a type of `object` in JavaScript with integer-looking* property names. This means the `in` operator really just references enumerable indices in an array which act as property names for the array values. So in the above example, both `0` and `1` are `in [1, 2]` just as `0` and `1` are `in [99, 100]`. You're probably used to referencing elements in an array by passing some integer to the bracket operator like this: `array[0]`. For example the following array appears to have integer property names:

![console array representation]({{ site.baseurl }}/images/2016-12-11-JavaScript-in-Confusion/console.png)

On the contrary, you may know that all property names in JavaScript are really strings and arrays are just objects. Therefore if `array[0]` is ok, then `array['0']` and `array["0"]` should work out too. Hmm, so which is technically the *right* way to index an array, via string or integer? Furthermore which of the following is true?

 - When indexing an array with a string, e.g. `array["0"]`, the string is converted to an integer index
 - When indexing an array with an integer, e.g. `array[0]`, the integer is converted to a string index

As stated, ALL property names in JavaScript objects are strings, and since arrays are really just objects, string indices are technically more natural for the compiler as integers will need to be converted to strings before actual indexing occurs.

# Prove it Dom

So given our previous evidence it makes sense to say there is some equivalence between `0 and '0'`, and in fact there is! This is the reason why JavaScript has a triple, or strict `===` equals sign. In JavaScript `0 == '0'` which means `array[0]` really makes the interpreter say *"Hey, is there some property name (string) on this object that == the given 0?"*. On the contrary, `0 === '0'` will return false.

Now that we know `in` evaluates the existence of a property in some object it should be fairly obvious that a `for..in` loop will iterate through all of the enumerable properties in some object. I say *enumerable properties* because if a property has `enumerable` set to `false` in its data descriptor than it will not show up in a `for..in` loop even though it will accessible through a regular call to `in` outside of such iteration.

Consider the following example showing that arrays are just augmented objects:

![enumerability and iteration]({{ site.baseurl }}/images/2016-12-11-JavaScript-in-Confusion/second.png)

# in vs hasOwnProperty

In the second sentence of this post I alluded to the fact that `in` checks not only some given object for property existence, but also its `[[Prototype]]` chain. This is a subtle but important detail. If you want to check for property existence **only** in some object while not consulting its `[[Prototype]]` chain you should use `.hasOwnProperty()` available to any object through `Object.prototype.hasOwnProperty`. This final example should sum up the details:

![in vs hasOwnProperty]({{ site.baseurl }}/images/2016-12-11-JavaScript-in-Confusion/final.png)
