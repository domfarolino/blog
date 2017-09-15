---
layout: post
title: Browser Cache Fundamentals
category: Web
tags: [web standards, web applications]
---

The browser cache is something I think a lot of developers know of but don't understand
well, primarily because many tools take care of it for us. For example, when building an
application with Node.js the first thing many developers do is type:

> `npm install --save express`

With express we have the choice to not worry about setting any response headers, including
ones that tell the browser how to cache content. In this post, I want to give a brief overview
of browser caching mechanics and the control we have as developers.

# Browser Cache Fundamentals

![Four Different Headers]({{ site.baseurl }}/images/2017-01-04/4-headers.png)

When the browser (client) receives content from a server it also gets a list of HTTP headers. The headers
contain data about the content being transferred. Specific headers can be sent to instruct the client to cache
a response in order to minimize the data transferred on subsequent requests. Two types of headers are used for
client-side caching. The first is a **cache response header**, which tells the client whether or not a response
is able to be cached, and if so, for how long. The second is a **validator**, which acts as a fingerprint for a
cached response. Say a request is made for content that appears in the cache, but is expired. The client should
validate its cache with the server, as it may be still be able to be used as a response. The validator is used
to make a quick comparison between a cached response and the fresh response the server would naturally send. This
allows a client to *only* re-downloaded content when necessary. For example, when sending some content the server
may say:

> "Hey client, you can cache this for up to 10 minutes."

The client can then use this cached version to fulfill up to 10 minutes of requests before it should go back to the
server asking for an updated copy. But what if the copy on the server hasn't changed when the 10 minutes is up? In
this case, we'd prefer the client and server efficiently discuss whether the version in the client's cache is outdated
(needs to be re-downloaded) or is good for another 10 minutes. This is made possible by the validator.

# Cache Response Headers

Let's talk about how the above conversation takes place. Along with a response, the server can send an `Expires`
or `Cache-Control` header instructing the client to cache the response. What's the difference? Great question! The
`Expires` header provides an expiry date of a response computed by the server in the form of a timestamp. The newer,
and preferred, `Cache-Control` header allows us to be more specific. When both are provided, `Cache-Control` takes
precedence and is the preferred method of cache instruction, so I'll mostly be discussing it here. The `Cache-Control`
header can take many different values (directives) explaining how an asset should be cached, but in this post I'm going
to cover three that should cover all of your needs.

** Disclaimer: Just because the server instructs a client to cache a response does not guarantee it will be successfully
cached. The browser has the right to ignore cache headers and/or evict resources from the cache.

### Cache-Control: max-age=xxx

The `max-age` directive instructs a client to cache a response for a given number of seconds. The cache is considered "fresh"
until the max-age is reached. Therefore, subsequent requests for said response can be served directly from the browser cache.
A request for a cached asset might look like this:

![max-age fulfilled]({{ site.baseurl }}/images/2017-01-04/max-age.png)

### Cache-Control: no-cache

Despite its name, the `no-cache` directive actually instructs the browser to cache content with the condition that it MUST be revalidated
with the server every time it is requested before being served from the cache. If the validators indicate the content has not changed, the
cached version will be served with an HTTP `304 Not Modified` response. If either the server's content differs from the client's cache or no
validation headers are provided, the content is re-downloaded and usually served with an HTTP `200 OK` response. This allows a client to
always be up to date without incurring the cost of re-downloading unchanged content.

> So `max-age=0` and `no-cache` are the same... right?

Not quite! Setting `max-age=0` marks cached content as "stale" right off the bat, however some clients can be configured to return stale content
even when they **SHOULD** revalidate. `no-cache` gives us a tighter contract in that content is actually considered unusable without successful
revalidation. So really, `no-cache` is more of a guarantee that revalidation will happen on subsequent requests, even on clients configured to
return stale responses. To force a client to revalidate stale content (older than `max-age`), the `must-revalidate` directive is necessary. Therefore
`max-age=0, must-revalidate` is equivalent to using `no-cache`.

### Cache-Control: no-store

The `no-store` directive is ruthless and instructs the client to not cache a response at all. Consequently, content must be downloaded from
the server on every request. This directive is rarely used and can be seen as a mechanism to prevent the storage of sensitive information.

# Validation Headers

Now that we've learned how to instruct the client to cache responses, let's dig into validation. Validation headers are often sent to clients
in conjunction with cache response headers, but this is not mandatory. As we've briefly discussed, when a request is made for a stale or
expired asset, the client should validate its cache with the origin server. This is done by sending any and all validation headers it has
associated with the cached response. Once the server makes a determination, it will most likely respond with either a partial `304 Not Modified`
response instructing the client to use and "freshen" its cache, or a full `200 OK` response instructing the client to serve and update its cache
with the new content.

### Weak and Strong Validators

Validators can be weak or strong, and the difference between the two can be summed up with the following excerpt from the
[IETF RFC7826 Draft:](https://tools.ietf.org/html/rfc7826#section-16.1.3)

>...one normally would expect that if the entity (the entity-body or any entity- headers) changes in any way, then the
>associated validator would change as well. If this is true, then we call this validator a "strong validator."
>
>However, there might be cases when a server prefers to change the validator only on semantically significant changes,
>and not when insignificant aspects of the entity change. A validator that does not always change when the resource changes
>is a "weak validator."

### Last-Modified

The `Last-Modified` validation header is a timestamp, with granularity to the nearest second, representing the time at which some content
was last modified. It is most often sent with the `Expires` cache response header, as they are both time-based. Upon request for an expired
or stale asset, the client will send the timestamp in the form of the `If-Modified-Since: TimestampHere` request header. The server will
compare the client's timestamp with the actual `L-M` of the requested resource. If the server deems the client's version is outdated,
it will fulfill the request with a full `200 OK` response giving the client a fresh download. Likewise, if the client's version is up to date,
it will fulfill with a `304 Not Modified` instructing the client to use its cached version. The `L-M` header is a weak validator since a file
could change more than one time in a single second without the validator reflecting this.

### ETag

The `ETag` (Entity Tag) validator is by default strong. ETags are normally a string representing the current state of a document. It is
common for an ETag to be generated based off of the hash of some file, so it is an accurate representation of its contents, however something as
simple as a revision string would suffice. The key here is that the ETag value is not necessarily time-based, and of course changes along with
the resource and headers it represents. ETags are sent to the server for validation in the form of the `If-None-Match: ETagHere` request header.
An ETag can be declared as "weak" with the `W/` prefix, which express uses by default.

# Experimenting with Cache Busting

Suppose you publish several assets to your site instructing clients to cache them for 3600 seconds (1 hour), but then quickly realize you need to
make a change that should be visible to everyone immediately. We haven't really covered a way to force the client to abandon its cache on demand
because there kind of isn't one, but there is a neat way to get around this issue.

The idea behind cache busting starts with the server instructing clients to cache static assets for a very long time, while insisting application routes
always be validated.

For example, when a user visits the root of your website **`/`** they will receive a single document. This document often contains links to
external stylesheets and scripts, which the browser's parser will make requests for as it reads the document. If we instruct the browser to
cache these external assets for a very long time, we can still force a client to re-download changed content by making a change to the document
they will always be revalidating. See this in action below:

> I made a simple app that allows you to test the setting and removal of certain cache headers easily - see
[GitHub](https://github.com/domfarolino/browser-cache-fundamentals)

First we publish some assets to our site, one of which is a stylesheet that gets pulled in through the `index.html`
document.

![css-gray-bg]({{ site.baseurl }}/images/2017-01-04/css-gray-bg.png)

![grey-header]({{ site.baseurl }}/images/2017-01-04/grey-header.png)

The static assets on this page will be cached for a very long time, however, the document (`index.html`) that holds the links to
these assets gets revalidated on every request courtesy of `Cache-Control: no-cache`. Below is the output from an express server
revalidating a request triggered by a Chrome refresh. Notice no other page assets initiated by the document are getting revalidated.

![server-root-request]({{ site.baseurl }}/images/2017-01-04/server-root-request.png)

Changes to this CSS file are never communicated to clients, as requests for it are going through the cache. The trick here is to
modify the consistently revalidated document that pulls this file in.

![css-rebecca-bg]({{ site.baseurl }}/images/2017-01-04/css-rebecca-bg.png)

In order to tell the clients (ones that might revalidate user-initiated requests more frequently than necessary) about the change we
can do the following.

![cssv2-initiate]({{ site.baseurl }}/images/2017-01-04/cssv2-initiate.png)

By adding something as simple as a query string, or even changing the name, the browser's parser creates a request for an asset that
does not match the URL of any cached items. In this case, the browser will be forced to go to the server for it!

![server-cssv2-request]({{ site.baseurl }}/images/2017-01-04/server-cssv2-request.png)

Notice in the above picture, two requests (the only necessary ones) are fulfilled while other page requests are able to go through
the cache as normal. The client now has the most up-to-date styles yielding a very flattering purple header!

![purple-header]({{ site.baseurl }}/images/2017-01-04/purple-header.png)
