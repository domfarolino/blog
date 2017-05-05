---
layout: post
title: JavaScript "in" Confusion
category: Web
tags: [javascript]
---

The `in` operator, when used with arrays in JavaScript, comes with a little nuance and may
be the cause of some confusion. The `in` operator is strictly used to tell whether a given
property name exists *in* an object or its `[[Prototype]]` chain (and is enumerable, but
we'll cover that later). So how does this work for arrays? It's tempting to think `2` is
`in [1, 2]` but this will return false.

# Why?

Arrays are really just `objects` with integer-looking property names. So in the above example,
both `0` and `1` are `in [1, 2]` just as `0` and `1` are `in [99, 100]`. But wait, you may have
heard that all object property names in JavaScript are strings*, so how does the array allow
integer indexing and `in` checking?

You're probably used to referencing elements in an array by passing some integer to the bracket
operator like this: `array[0]` right? For example, the following array *appears* to have integer
property names:

![console array representation]({{ site.baseurl }}/images/2016-12-11-JavaScript-in-Confusion/console.png)

But in reality, array property names (you know them as indices) are strings just like in the object below.

![console object representation]({{ site.baseurl }}/images/2016-12-11-JavaScript-in-Confusion/console2.png)

The reason that `0 in array` works just as well as `0 in object` and `"0" in object` is because of
JavaScript type coercion. Yes, it is exactly what it sounds like. When checking property existence
with an integer, JavaScript coerces the integer into becoming a string by calling `toString()` before
the property lookup.

# Prove it Dom

So given our previous evidence, it makes sense to say there is some equivalence between `0 and "0"`,
and in fact there is! This is precisely why the language has the triple or "strict" `===` equals
operator. In JavaScript, `0 == "0"` beacuse type coercion can make their values equivalent. This
means `array[0]` really makes the interpreter say
*"Hey, is there some property name on this object that == 0?"*.
On the contrary, `0 === "0"` will return false.

Now that we know `in` evaluates the existence of a property in some object, it should be fairly
obvious that a `for..in` loop will iterate through all of the enumerable properties in some object.
I say *enumerable properties* because if a property's data descriptor has `{enumerable: false}`, it
will not show up in a `for..in` loop. Even though it is still accessible via a regular call to `in`
outside of a loop.

Consider the following example showing that arrays are just augmented objects:

![enumerability and iteration]({{ site.baseurl }}/images/2016-12-11-JavaScript-in-Confusion/second.png)

# in vs hasOwnProperty

In the second sentence of this post, I alluded to the fact that `in` checks not only some given
object for property existence, but also its `[[Prototype]]` chain. This is a subtle but important
detail. If you want to check for property existence **only** in some object, while not consulting
its `[[Prototype]]` chain, you should use `.hasOwnProperty()` available to any object through
`Object.prototype.hasOwnProperty`. This final example should sum up the details:

![in vs hasOwnProperty]({{ site.baseurl }}/images/2016-12-11-JavaScript-in-Confusion/final.png)
