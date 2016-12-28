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
ones that tell the browser how to cache content. In this post I want to give a brief overview
of browser caching mechanics and the control we have. I then want to talk about how the service
worker's lifecycle and cache play into all of this.

# Browser Cache Fundamentals

![Four Different Headers]({{ site.baseurl }}/images/2016-12-27/4-headers.png)

When the browser receives content from the server it also gets a list of HTTP headers. The headers contain
data about the content being transferred. The server can send specific headers to instruct the browser (client)
to cache some content in order to minimize the amount of data transferred over the network on **future** requests
for the same content. Two types of headers are used for client-side caching logic. The first type of header is a
cache instruction header which tells the client whether or not some content can be cached and if so for how long.
The second type is a validator which acts as a fingerprint for some content. If a request is made for content that
appears in the cache but is expired, we need to validate the cached version with the server's version. The validator
is used for this quick comparison (think checksum) allowing the client to *only* re-download content if its cache is
out of date. For example, when sending some content the server may say:

> "Hey client, you can cache this for up to 10 minutes".

The client is then allowed to use its cached version of the asset for up
to 10 minutes of requests before it must go back to the server asking for an updated copy. But what if the copy
on the server hasn't changed when the 10 minutes is up? In this case we'd prefer the client and server efficiently
discuss whether the version that the client has is outdated (needs to be re-downloaded) or is good for another 10
minutes. This quick comparison is made possible by the validator.

# Caching Instruction Headers

Let's talk about how exactly the above conversation takes place. The server instructs the client to cache an
asset with either the `Expires` or `Cache-Control` header. If both are provided, `Cache-Control` takes precedence
and is the preferred method of caching, so I'll be discussing it here. The `Cache-Control` header can take many
different values (directives) explaining how an asset should be cached but I'm going to cover three that should
cover all of your needs.

** Disclaimer: just because the server instructs a client to cache some asset does not guarantee it will be
cached. Clients have the right to ignore cache headers and/or drop things from the cache.

### Cache-Control: max-age=xxx

The `max-age` directive instructs a client to cache an asset for a number of seconds. The cache is considered "fresh"
until the max-age is reached, and subsequent requests for it can be served directly from the browser cache. A request
for a cached asset might look like this:

![max-age fulfilled]({{ site.baseurl }}/images/2016-12-27/max-age.png)

### Cache-Control: no-cache

The `no-cache` Cache-Control directive isn't exactly intuitive. Contrary to popular belief this directive actually
instructs the browser to cache the content (IKR), however the client MUST revalidate the content before serving from
cache. If content has not changed on the server (regardless of whether headers have changed or not) the cached version
will be served with an HTTP `304 Not Modified` response content.

> So does `max-age=0` do the same thing as `no-cache`?

Setting `max-age=0` means the content is considered stale, and some clients can be configured to return stale content
though they **`SHOULD`** revalidate first. `no-cache` means the content is not even considered "stale", it is just considered
unusable without successful revalidation. So really `no-cache` is more of a guarantee that revalidation will happen on subsequent
requests. `no-cache` ensures revalidation will happen on clients that are configured to return stale responses. To force a client
to revalidate stale content past its `max-age` the `must-revalidate` directive is also necessary. So really `no-cache` is short-hand
for `max-age=0, must-revalidate`.

### Cache-Control: no-store

The `no-store` Cache-Contol directive is ruthless. It instructs the client to not cache the content AT ALL, and any requests
must be re-downloaded from the server every time. This directive is rarely used and can be seen as a mechanism to prevent the
leakage and storing of sensitive content.

# Validation Headers

Now that we've learned how to instruct the client to cache content, we can dig into the validation of cache. When a request
is made for a "stale" or "expired" asset the client *should* validate its contents with the server by sending validation headers.
All available validation headers will be sent to the server, and if at least one indicates the asset on the server does not match the
one the client has in cache, the asset must be re-downloaded.

  - ETag and W/Etag
  - ETag validation
  - 200 vs 200 (from x cache) vs 304

# Cache Busting - Understanding Initiator
 - Understanding that ETag is only checked on

 - ETag is checked on

# Brief Overview of Service Worker Lifecycle

# Service Worker cache + browser cache