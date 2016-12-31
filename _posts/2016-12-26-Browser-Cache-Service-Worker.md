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

# Cache Response Headers

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

Despite its name, the `no-cache` directive actually instructs the browser to cache content with the condition that it MUST be revalidated
with the server every time it is requested before being served from the cache. If the content has not changed (regardless of whether or not
the headers have changed) the cached version will be served with an HTTP `304 Not Modified` response. If the cached content is out of date or
no validation headers are provided (see next section) the content is re-downloaded and a full HTTP `200 OK` response is usually served. This allows
a client to always have the latest version of some content without incurring the cost of re-downloading unchanged content.

> So does `max-age=0` do the same thing as `no-cache`?

Setting `max-age=0` makes cached content stale off the bat, however some clients can be configured to return stale content when they **`SHOULD`**
revalidate first. `no-cache` gives us a tighter contract in that content is not considered stale, but instead considered unusable without successful
revalidation. So really `no-cache` is more of a guarantee that revalidation will happen on subsequent requests especially on clients configured to return
stale responses. To force a client to revalidate stale content past its `max-age`, the `must-revalidate` directive is necessary. In short `no-cache` is
equivalent to `max-age=0, must-revalidate`.

### Cache-Control: no-store

The `no-store` directive is ruthless and instructs the client to not cache content a response at all. Consequently, content must be downloaded from
the server on every request. This directive is rarely used and can be seen as a mechanism to prevent the storage of sensitive information.

# Validation Headers

Now that we've learned how to instruct the client to cache content through cache response directives, we can dig into the validation of such content. Validation
headers are often sent to clients in conjunction with cache response directives (like `Cache-Control: max-age=60`) but this is not mandatory. As we've briefly
discussed, when a request is made for a stale or expired asset the client should validate its cache with the origin server by sending any and all validation
headers it has associated with a response. Once the server makes a determination it will most likely respond with either a partial `304 Not Modified` response
instructing the client to use and "freshen" its cache, or a full `200 OK` response instructing the client to serve and update its cache with the new content.

### Weak and Strong Validators

Validators can be weak or strong, and the difference between the two can be summed up with the following exerpt from the
[IETF RFC7826 Draft:](https://tools.ietf.org/html/rfc7826#section-16.1.3)

>...one normally would expect that if the entity (the entity-body or any entity- headers) changes in any way, then the
>associated validator would change as well. If this is true, then we call this validator a "strong validator."
>
>However, there might be cases when a server prefers to change the validator only on semantically significant changes,
>and not when insignificant aspects of the entity change. A validator that does not always change when the resource changes
>is a "weak validator."

### Last-Modified

The `Last-Modified` validation header is a timestamp often sent with the `Expires` cache instruction header, however, it can
be sent with `Cache-Control` or neither. Upon request for an expired or stale asset, the server will compare the `L-M` timestamp
the client sent with the actual `L-M` timestamp of the requested resource. If the server deems the client's version is outdated
it will fulfill the request with a full `200 OK` response giving the client a fresh download. Likewise if the client's version is
up to date, it will fulfill with a `304 Not Modified` instructing the client to use its cached version. The `L-M` header with
specificity to the nearest second is a weak validator since a file could change more than one time in a single second without the
validator reflecting this.

### ETag

The `ETag` (Entity Tag) validator header is by default a strong validator. ETags are normally some string that represent the current
state of a document. It is common for an ETag to be generated based off of the hash of some file so it is an accurate representation
of its contents however something as simple as revision `v1, v2, v3, ...` would work just as well. The key here is that the ETag value
changes along with the resource it represents. An ETag can be declared as "weak" with the `W/` prefix.

### ETags in Express

# Cache Busting - Understanding Initiator

# Brief Overview of Service Worker Lifecycle

# Service Worker cache + browser cache