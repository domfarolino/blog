---
layout: post
title: Browser Cache and Service Worker Lifecycle
category: Web
tags: [web standards, javascript, web applications]
---

The browser cache is something that I think a lot of developers know of but don't understand
well primarily because many tools take care of it for us. For example, when building an
application with Node.js the first thing many developers do is type:

> `npm install --save express`

With express we have the choice to not worry about setting any response headers, including
the ones in charge of telling the browser how to cache our content. In this post I want to
give a brief overview of browser caching mechanics and the control we have. I then want to
talk about how the service worker's lifecycle and cache play into all of this.

# Browser Cache Fundamentals

When the browser receives content from the server it also gets a list of HTTP headers. The headers contain
data about the content being transferred. The server can send the client (browser) specific headers to instruct
it to cache some of the content in order to minimize the amount of data transferred over the network on **future**
requests for the same content. There are two types of headers the client can utilize for caching logic. The first
type tells the client whether or not some content can be cached and if so for how long, while the second type is a
validator that acts as a fingerprint for some content. The validator is used when the cached copy of some content
is expired so we can quickly tell whether the client needs to redownload some content.

For example, say the client receives a **massive** file from the server. The server might say "Hey client, you
can cache this for up to 10 minutes". The client is then allowed to use its cached version of the asset for up
to 10 minutes of requests before it must goes back to the server asking for an updated copy. But what if the copy
on the server hasn't changed when the 10 minutes is up? In this case we'd prefer the client and server efficiently
discuss whether the version that the client has is outdated (needs to be re-downloaded) or is still good for
another 10 minutes. This efficient exchange of information is where the validator comes into play.

## Headers

![Four Different Headers]({{ site.baseurl }}/images/2016-12-27/4-headers.png)

Let's talk about how exactly the above conversation takes place. The server instructs the client to cache an
asset with either the `Expires` or `Cache-Control` header. If both are provided, `Cache-Control` takes precedence
and is the preferred way to instruct the browser cache on how to work. The `Cache-Control` header can take many
different values (directives) explaining how an asset should be cached but in this post I'm going to briefly cover
three that should be able to suit all of your needs.

** Disclaimer: just because the server instructs a client to cache some asset does not guarantee it will be
cached - the client has the right to ignore cache headers if the cache is full or needs to make room for newer content.

### Cache-Control: max-age=xxx

It is common to instruct the browser to cache some asset for a number of seconds by sending the `Cache-Control: max-age=xxx`
header. The cached version is considered "fresh" and can be served directly from the browser cache for the next `xxx` seconds
at most. Cached responses may look like this:

### Cache-Control: no-cache

`Cache-Control: no-cache` doesn't quite do what it sounds like it does. The `no-cache` directive actually instructs the
browser to cache the content (IKR), but it MUST NOT serve the cached version of the asset without revalidating with the
server.

### Cache-Control: no-store

Using `Cache-Control: no-store` instructs the browser to

### Mixing max-age, no-cache, and no-store

Using `Cache-Control: max-age=xxx` instructs the browser to

  - Workflow - the browser grabs contents and given headers, tries to minimize the amount of data 
  - Cache-Control
   - When will the browser go to the server for a response
  - ETag and W/Etag
  - ETag validation
  - 200 vs 200 (from x cache) vs 304

# Cache Busting - Understanding Initiator
 - Understanding that ETag is only checked on

 - ETag is checked on

# Brief Overview of Service Worker Lifecycle

# Service Worker cache + browser cache