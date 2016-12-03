---
layout: post
title: Intro To Custom Elements
category: Web
tags: [javascript, es6, web applications, web specs]
---

## Custom Elements and Reusable Web Components

I recently discovered the new `customElements` API and after some experimentation I decided to write about its awesomeness. Custom Elements allows web developers, to define their own top-level HTML elements, thus promoting reusability among the [Web Components](http://webcomponents.org/) community. On top of creating your own elements, you can extend the functionality of existing elements and share your defined extensions with other developers.

## What's the big deal?

Before digging into the mechanics of Custom Elements let's talk about why their so important.

Prior to Custom Elements there was no clean way to associate JavaScript logic with some element's functionality. It is often not obvious from looking at markup that there may be JavaScript functionality associated with some element, however Custom Elements allows us to more tightly couple an element's definition and functionality.

Custom Elements can also define their own internal markup, but furthermore can be given super powers when they utilize [`Shadow DOM`](http://w3c.github.io/webcomponents/spec/shadow/). In short, `shadow DOM` gives us the ability to produce a fully encapsulated self-contained DOM tree attached to some document. CSS defined in `shadow DOM` is completely contained inside this tree giving us two big benefits:

 - Styles will **not leak out** from this DOM to an outer tree
 - Styles from an outer tree will **not bleed into** this DOM

It is important to realize that Custom Elements and shadow DOM are two separate things, and as such can be used independent of each other; however using them together allows us to create rock-solid reusable components.

## Our First Custom Element

We can define a Custom Element like this:

```js
class CoolCard extends HTMLElement {
  constructor() {
    super(); // MUST to allow HTMLElement interface to set itself up
  }
}

customElements.define('cool-card', CoolCard);
```

Note that CustomElements that do not extend some existing HTML element must extend the `HTMLElement` interface which allows our card to
