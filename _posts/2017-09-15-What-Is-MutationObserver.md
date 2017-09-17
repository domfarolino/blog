---
layout: post
title: What is MutationObserver?
category: Web
tags: [web standards]
---

`MutationObserver` is a DOM API that allows us to react to changes in the DOM in a
performant way. The `MutationObserver` constructor in JavaScript takes a callback
function which gets called on each mutation you choose to observe. The callback is
called with two parameters.

 - An array of `MutationRecord` objects
 - The instance of this `MutationObserver`

### Example:

```js
  let observer = new MutationObserver((records, instance) => {
    console.log(records);
  });
```

An instance of a `MutationObserver` has three functions.

 - `observe(Node, MutationObserverInit)`
 - `disconnect()`
 - `takeRecords()`

To observe a node, we simply call the `observe` function on an instance of `MutationObserver` and specify
the node to observe. But what is this `MutationObserverInit` parameter?
The [DOM standard](https://dom.spec.whatwg.org/#dictdef-mutationobserverinit) tells us that this is just
a JavaScript object we use to tell the browser exactly what it is we'd like to observe on a particular
node. The spec makes it clear that we can observe a node's child list, attributes, and character
data by passing in an object with any of the following keys associated with boolean values.

 - `childList`
 - `attributes`
 - `characterData`

There are also more settings for us to choose from! For example, if we want to observe a specific
type of mutation not only on some node, but also on the nodes in its subtree, we can set `subtree: true` in
our `MutationObserverInit` object. We can also indicate that on an `attribute` or `characterData` change
we'd like to be passed the old value prior to these mutations by specifying either `attributeOldValue: true`
or `characterDataOldValue: true`. Additionally, if observing all attributes is not necessary, we can set
the `attributeFilter` key to an array of specific attribute names to observe.

# Reacting to DOM Mutations

As mentioned, when a DOM mutation occurs that we're observing, our callback function is supplied with
an array of `MutationRecord` objects and our instance of `MutationObserver`. We can loop through our
array of mutation records to choose which one we'd like to react to, and how. The
[`MutationRecord`](https://dom.spec.whatwg.org/#interface-mutationrecord) object provides us with many
helpful members we can use to distinguish mutations. The `type` and `target` members are among the most
useful in helping us determine which mutation we're viewing. The `type` member will either return
`attributes`, `characterData` or `childList` depending on the type of mutation (no surprise there), while
`target` will return the node affected by the mutation. There are also several more helpful members such as
`oldValue`, `addedNodes`, `removedNodes`, and `attributeName` used to extract information from the
mutation that occurred.

# Mutations and Microtasks

In case you are unfamiliar with the concept of microtasks and how the browser schedules work, I recommend reading
Jake Archibald's article on [tasks and microtasks](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/).
It's a tad lengthy, so I'll provide a bit about how it pertains to how `MutationObserver` callbacks are scheduled.
Basically, when an observed DOM mutation occurs, `MutationObserver` callbacks are queued as microtasks in the event loop's
microtask queue. This means a callback will be executed after the currently running task in the event loop *OR* mid-task
after callbacks, if the JS stack is empty.
