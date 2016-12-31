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

When the browser (client) receives content from a server it also gets a list of HTTP headers. The headers
contain data about the content being transferred. The server can send specific headers to instruct the client
to cache some content in order to minimize the amount of data transferred over the network on **future** requests.
Two types of headers are used for client-side caching logic. The first type of header is a cache instruction header
which tells the client whether or not some content can be cached and if so for how long. The second type is a validator
which acts as a fingerprint for some content. If a request is made for content that appears in the cache but is expired,
we should validate the cached version with the server, as it may be outdated. The validator is used for this quick comparison
(think checksum) allowing the client to keep its cached version if it is up to date. For example, when sending some content the
server may say:

> "Hey client, you can cache this for up to 10 minutes".

The client can then use this cached version to fulfill up to 10 minutes of requests before it must go back to the
server asking for an updated copy. But what if the copy on the server hasn't changed when the 10 minutes is up? In
this case we'd prefer the client and server efficiently discuss whether the version in the client's cache is outdated
(needs to be re-downloaded) or is good for another 10 minutes. This quick comparison is made possible by the validator.

# Cache Instruction Headers

Let's talk about how exactly the above conversation takes place. The headers of a response may instruct the client
to cache an asset with either the `Expires` or `Cache-Control` header. `Expires` provides a timestamp computed by the
server whereas the newer and preferrd `Cache-Control` allows us to be more specific. If both are provided, `Cache-Control`
takes precedence and is the preferred method of cache instruction so I'll mostly be discussing it here. The `Cache-Control`
response header can take many different values (directives) explaining how an asset should be cached but in this post I'm
going to cover three that should meeet all of your needs.

** Disclaimer: just because the server instructs a client to cache some asset does not guarantee it will be
cached. Clients have the right to ignore cache headers and/or evict things from the cache.

### Cache-Control: max-age=xxx

The `max-age` directive instructs a client to cache an asset for a number of seconds. The cache is considered "fresh"
until the max-age is reached, therefore subsequent requests for it can be served directly from the browser cache. A request
for a cached asset might look like this:

![max-age fulfilled]({{ site.baseurl }}/images/2016-12-27/max-age.png)

### Cache-Control: no-cache

The `no-cache` Cache-Control directive isn't exactly intuitive. Contrary to popular belief this directive actually
instructs the browser to cache content with the condition that the client MUST revalidate the content every time before
serving from cache. If content has not changed on the server (regardless of whether headers have changed or not) the cached version
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

Now that we've learned how to instruct the client to cache content, we can dig into the validation of such content. Validation
headers are often sent to clients in conjunction with a cache instruction are most often sent alongside cache instruction headers (makes sense) but this is not mandatory.When a
request is made for a "stale" or "expired" asset, the client should validate its contents with the server by sending any and
all validation headers it got when it first received the content.

### Weak and Strong Validators

Validators can be weak or strong, and the difference between the two can be summed up with the following exerpt from the
[IETF RFC7826 Sec:](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13.3.3)

>...one normally would expect that if the entity (the entity-body or any entity- headers) changes in any way, then the
>associated validator would change as well. If this is true, then we call this validator a "strong validator."
>
>However, there might be cases when a server prefers to change the validator only on semantically significant changes,
>and not when insignificant aspects of the entity change. A validator that does not always change when the resource changes
>is a "weak validator."

### Last-Modified

The `Last-Modified` validation header is a timestamp often associated with the `Expires` cache instruction header however it can
be sent with `Cache-Control` or neither. Upon request for an expired or stale asset, the server will compare the `L-M` timestamp
the client sent with the actual `L-M` timestamp of the requested resource. If the server deems the client's version as outdated
it will fulfill the request with a `200 OK` response giving the client a fresh download. Likewise if the client's version is up
to date, it will fulfill with a `304 Not Modified` instructing the client to use its cached version. The `L-M` header is by default
a weak validator since a file could change multiple times in a single second.

### ETag

The `ETag` (Entity Tag) validator header is

  - ETag and W/Etag
  - ETag validation
  - 200 vs 200 (from x cache) vs 304

# Cache Busting - Understanding Initiator
 - Understanding that ETag is only checked on

 - ETag is checked on

# Brief Overview of Service Worker Lifecycle

# Service Worker cache + browser cache