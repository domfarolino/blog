---
layout: post
title: JavaScript Event Flow
category: Web
tags: [javascript, es6, web applications, web standards]
---

This article is intended to further your knowledge of the various phases a DOM event goes through.

Great resources to refer to:

 - [W3 Event Flow Spec](https://www.w3.org/TR/DOM-Level-3-Events/#event-flow)
 - [Bubbling and Capturing](https://javascript.info/tutorial/bubbling-and-capturing)

## TL;DR

Experiment with [this](https://gist.github.com/domfarolino/fdde99c1ad3fa1668a1849c33f87f437#file-index-html) HTML file to learn about the JavaScript event flow.

# Event Flow

When you click an element that is nested in various other elements, before your `click` actually reaches its destination ([`target element`](https://www.w3.org/TR/DOM-Level-3-Events/#event-target)) it must trigger the click event of each of its parent elements first. Starting at the top with the global `window` object, the click event trickles down parent by parent, in what is called the [`capture phase`](https://www.w3.org/TR/DOM-Level-3-Events/#capture-phase), until it finally arrives at the event's intended target element.

When the event can finally be handled by its intended target, we enter the [`target phase`](https://www.w3.org/TR/DOM-Level-3-Events/#target-phase). The target phase is really just the very end of the capture phase and the very beginning of the bubble phase. Once the target phase is complete and the target element handles the event however it wishes, we begin the third and final phase of the event flow called the [`bubble phase`](https://www.w3.org/TR/DOM-Level-3-Events/#bubble-phase). In this phase, the event "bubbles" up from the event target through all of its parent elements back to the global `window` object. This is essentially the opposite of the capture phase.

# How can I use this?

Let's take a look at some generic code you've probably seen before that registers an event listener on some DOM element.

```js
document.getElementById('target-element').addEventListener('click', function(e) {
  // Handle click appropriately
});
```

This almost always does exactly what you want. However, there is much to learn when exploring how exactly the default `addEventListener()` code above works with the aforementioned event phases. Let's consider the following HTML:

```html
<div id="parent-element">
  <div id="target-element">
    Yay, some target text...
  </div>
</div>
```

If I were to register click event listeners on both the parent and target elements above, that say do a simple `console.log`, we might see the following output after clicking the `target-element` directly:

```
target-element event listener triggered
parent-element event listener triggered
```

Hmm. Since the `parent-element`'s click listener got triggered after the `target-element`'s, it is safe to say that `addEventListener()`'s default usage registers a listener on the event's bubble phase. So how, you might ask, can we register an event listener on the event's capture phase? Well, `EventTarget.addEventListener()` actually has a neat optional third parameter you can pass in to indicate which phase you'd like to be listening to. The API looks like this:

```
target.addEventListener(type, listener[, useCapture]);
```

If you pass nothing in as the boolean `useCapture` argument, it defaults to `false`. Thus registering an event listener on the event's bubble phase.

# $event.stopPropagation()$

The `event` API makes it possible for you, the developer, to stop an event dead in its tracks to ensure it never completes the current phase it's on. By calling `event.stopPropagation()`, you stop the event from moving onto the next element in its propagation path. This works with both the capture and bubble phases. To be clear, `event.stopPropagation()` will stop an event from traversing closer to its target if called in the capture phase, and stop an event from bubbling up to any parent elements if called in the bubble phase.

# What good is any of this?

Say you have event listeners registered with all kinds of elements on your web page. However, you only want the logic in each of the event listeners to be fired if the user intended on triggering the event on that specific element. It is probably clear now that clicking on some nested element is essentially the same as clicking on all of its parents too. If you want to avoid triggering your logic for all of these collateral clicks, we can utilize the event object's API!

You can simply check your current element against `event.target` to see if the passed in event was truly intended for the current element on which the event was triggered, or if the event is just making its rounds through its propagation path to get to or from its target. This may look something like:

```js
document.getElementById('parent-element').addEventListener('click', function(e) {
  if (this !== event.target) return; // avoid collateral clicks from event flow

  // All kinds of logic down here that should only be hit
  // If the event was originally intended for this the `parent-element`
  // .....
}, true);
```

# Caution with ES6 arrow functions

This is all neat and fancy stuff and, if you're feeling extra fancy, you may want to use ES6 style arrow functions as your second argument in a call to `addEventListener()`. However, **I'd strongly recommend against this practice.** When using call-back style functions, it is often very helpful to have the `this` argument dynamically bound to whichever object the call-back is referring to.

ES6 arrow functions are not capable of dynamically binding `this`. Therefore, when using an arrow function as a callback in `addEventListener()` or something similar, `this` will often be statically set to the global `window` object as in this case, or some outer origin element in others. This implicitly prevents you from performing the basic `this !== event.target` check we saw before, since `this` will usually never be what you expect it to be! Note this does not make a difference when you're registering event listeners on the `window` object itself. To see more instances where ES6 arrow functions can be dangerous, read [this article](https://rainsoft.io/when-not-to-use-arrow-functions-in-javascript/).

# Homework

I strongly urge you to experiment with these concepts. To get started, feel free to check out **[this](https://gist.github.com/domfarolino/fdde99c1ad3fa1668a1849c33f87f437#file-index-html)** basic HTML file I've created. Open up the console and run it in your favorite browser. Happy coding!
