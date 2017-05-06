---
layout: post
title: Intro To Custom Elements
category: Web
tags: [javascript, es6, web applications, web standards]
---

## Custom Elements and Reusable Web Components

I recently discovered the new `customElements` API and, after some experimentation,
I decided to write about its awesomeness. The Custom Elements API allows web developers
to define their own top-level HTML elements, thus promoting reusability among the
[Web Components](http://webcomponents.org/) community. On top of creating your own
elements, you can extend the functionality of existing elements and share your elements
with other developers.

## What's the big deal?

Before digging just yet, let's talk about why the idea is so important.

Prior to Custom Elements, there was no clean way to associate JavaScript logic with an
element's functionality. It is often not obvious from looking at markup that there may
be JavaScript functionality associated with some element. However, the Custom Elements
API allows us to more tightly couple an element's definition and functionality.

Custom elements can define their own internal markup, but furthermore can be given super
powers when they utilize [`Shadow DOM`](http://w3c.github.io/webcomponents/spec/shadow/).
In short, Shadow DOM gives us the ability to produce a fully encapsulated, self-contained
DOM tree attached to some element. A mouthful, I know. As a result of this provided
encapsulation, CSS style rules are constrained to the shadow tree they were defined in.
This style encapsulation gives us two noteworthy benefits:

 - Styles will **not leak out** from the Shadow DOM to an outer tree.
 - Styles from an outer tree will **not bleed into** the Shadow DOM.

It is important to realize that Custom Elements and Shadow DOM are two separate things,
and as such can be used independent of each other. However, using them together allows
us to create rock-solid, reusable components.

## Quick Shadow DOM Primer

Shadow DOM v1 has a fairly simple API allowing a shadow tree to be attached to any
element like this:

```js
somElement.attachShadow({mode: 'open'});
```

The API's `attachShadow()` function requires an object whose `mode` property is set
to `open` or `closed`. In general, you want to use `open` mode but I won't be covering
the specifics of both in this post. Once a shadow root is attached to some element, you
can treat it as a regular document. Note only one shadow root can be attached to an
element. This means elements that have a shadow root by default, like `<input>`, cannot
have another shadow root attached to them.

Curious as to why some elements have shadow roots by default? See
[this](https://glazkov.com/2011/01/14/what-the-heck-is-shadow-dom/) article.

## Our First Custom Element

We can define a Custom Element like this:

<script src="https://gist.github.com/domfarolino/3cc609b871f534c9a7a6c2575938f30c.js"></script>

You can interact with the above example in [this](https://jsfiddle.net/domfarolino/799fo1r0/)
fiddle.

Custom elements that do not extend the functionality of some existing HTML element, like
`<button>`, can be registered with the `window.customElements.define()` function. This
function takes in a tag name that must contain a hyphen (among some other requirements),
and the corresponding element class/function constructor. Custom elements that do not extend
the functionality of an existing element must extend the `HTMLElement` interface. Implementing
this interface allows our element to take the programmatic shape of a generic HTML element.
Thus the elements get properties that appear on the `HTMLElement` interface for free like
`style` and `tabIndex`, event handlers like `ontouchend` and `onpaste`, and methods like
`click()` and `blur()`. For more information on this interface, see
[this](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement) article.

Now, with our Custom Element definition and registration in place, users can can throw it
around their markup with the `<cool-card></cool-card>` tag.

## Element Callbacks

In the above class, I added the function `connectedCallback()` to our element's class. This
is a synchronous callback that is called when our `cool-card` gets inserted into the DOM and
instantiated with our class. There are several other callbacks available for us to use as event
hooks for our element. A full list can be found
[here](https://developers.google.com/web/fundamentals/getting-started/primers/customelements#reactions).

## Interacting with a Custom Element via `<slot>`

So far, we've seen a simple Custom Element that we can throw around with the
`<cool-card></cool-card>` tag. This tag basically acts as a placeholder for all
of its encapsulated markup, styles, and logic. However, we can make the element
more interactive by accepting some user defined markup to customize its content.
We can accept markup, called the `light DOM`, from a user and compose it with our
Shadow DOM by using the `<slot></slot>` element. This allows us to create an API
of sorts for our element. The developer is responsible for telling the user of
this element about this API. Consider the following Shadow DOM:

<script src="https://gist.github.com/domfarolino/071653d1886c3916fbed9c18a3e2ce27.js"></script>

The above `<slot name="slotNameHere"></slot>` tag allows markup with the `slot` attribute
set to `slotNameHere` to be infused in our Shadow DOM, thus merging multiple DOM trees
together. We can even style the user's DOM with the `::slotted(selector){}` CSS selector.
First, let's see how the user can insert markup inside our Shadow DOM.

<script src="https://gist.github.com/domfarolino/46f48631bad63974e6f16f92db6233b4.js"></script>

It should be clear that the `<slot name="slotNameHere"></slot>` in our element's Shadow DOM
is analagous to the `<element slot="slotNameHere"></element>` in the user's light DOM. The
styling of slotted light DOM is slightly more complex, so let's take a look at a possible
stylesheet:

<script src="https://gist.github.com/domfarolino/2326099d38dc1887c2b34166cb4f44ed.js"></script>

First off, we can style the actual Custom Element itself with the `:host{}` selector, which
allows us to react
[stylistically](https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom#contextstyling)
to attribute changes and class modifications. Next, we can style elements from the light DOM with the `::slotted()`
pseudo-element. In the above gist, I'm using an attribute selector with the slotted elements so that I can style
whichever element a user passes in as my card's header, body, and footer. You, of course, could style any slotted
elements like this:

```css
::slotted(div), ::slotted(h1) {
  text-align: center;
}
```

One caveat is that you **cannot style nested slotted elements**. For example, the following
style rules will never be applied:

```css
::slotted(div > .nestedElem) {
  /* Sorry boss, cannot style nested slotted elements :( */
}

::slotted(div) > ::slotted(.nestedElem) {
  /* Sorry boss, cannot style nested slotted elements :( */
}
```

The above interactive `CoolCard` Custom Element can be seen in this fiddle:

<script async src="//jsfiddle.net/domfarolino/n39ntatm/embed/html,result/"></script>

You can also provide an empty `<slot></slot>` element in your Custom Element's shadow
DOM to act as a pass-through for **any** light DOM content that does not match a specific
slot name. Providing default styles for pass-through slotted elements is a good idea to
ensure whatever the user passes in will adhere to the basic style and feel of our element.

## Customized Built-in Elements

So far we've only discussed the creation of "autonomous" Custom Elements, however, there
also exists another type. If some existing HTML element provides most of the functionality
you'd like to use by default, and you don't feel like recreating the wheel just to augment
it, your Custom Element class/function constructor can literally extend this element instead
of the `HTMLElement` interface. A Custom Element that extends `<button></button>` may look
like this:

<script src="https://gist.github.com/domfarolino/abbc6d7a3693501bc43d0dc576cb11d0.js"></script>

Notice that both the definition and registration of this kind of element differ. In addition,
its usage also differs very greatly. The reason we told `customElements.define(...)` that we
extend `"button"` is so we can use the Custom Element like this in our markup:

```html
<button is="cool-button">Click Me!</button>
```

## Polyfills

So you've been introduced to this new and exciting `window.customElements` API, but can you use
it everywhere? Unfortunately not :(. On the bright side, the Web Components community has created
several [polyfills](http://webcomponents.org/polyfills/) to emulate this technology in the browser.
These also provide helpful events you can listen to so that you know the polyfill(s) have been
loaded. There are also a couple variations of the polyfills, designed to give you only what you need,
so you're not pulling down more code than is necessary.

Since this is a brief introduction to Custom Elements, there is much more to learn. Below are some
great resources if you'd like to dig deeper:

 - [What the Heck is Shadow Dom?](https://glazkov.com/2011/01/14/what-the-heck-is-shadow-dom/)
 - [Custom Elements](https://developers.google.com/web/fundamentals/getting-started/primers/customelements)
 - [Shadow DOM](https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom)
 - [Web Components](http://webcomponents.org/articles/introduction-to-custom-elements/)
 - [All Web Components Polyfills](http://webcomponents.org/polyfills/)
 - [Custom Elements Spec](https://www.w3.org/TR/custom-elements/)

## Follow-up

If you like what you read and want to chat about the web, you can follow me anywhere
[@domfarolino](https://twitter.com/domfarolino) or grab my email from
[my site](https://domfarolino.com) and shoot me a message!
