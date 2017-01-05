---
layout: post
title: Browser Cache Fundamentals
category: Web
tags: [web standards, javascript, web applications]
---

The browser cache is something that I think a lot of developers know of but don't understand
well primarily because many tools take care of it for us. For example, when building an
application with Node.js the first thing many developers do is type:

> `npm install --save express`

With express we have the choice to not worry about setting any response headers, including
ones that tell the browser how to cache content. In this post I want to give a brief overview
of browser caching mechanics and the control we have as developers.

# Browser Cache Fundamentals

![Four Different Headers]({{ site.baseurl }}/images/2017-01-04/4-headers.png)

When the browser (client) receives content from a server it also gets a list of HTTP headers. The headers
contain data about the content being transferred. Specific headers can be sent to instruct the client to cache
a response in order to minimize the data transferred on subsequent requests. Two types of headers are used for
client-side caching. The first is a **cache response header** which tells the client whether or not
a response is able to be cached, and if so for how long. The second is a **validator** which acts as a fingerprint
for a cached response. If a request is made for content that appears in the cache but is expired, the client should
validate its cache with the server, as it may be outdated. The validator is used for a quick comparison between a
cached response and what the server would normally send. This allows a client to *only* re-downloaded content when
necessary. For example, when sending some content the server may say:

> "Hey client, you can cache this for up to 10 minutes".

The client can then use this cached version to fulfill up to 10 minutes of requests before it should go back to the
server asking for an updated copy. But what if the copy on the server hasn't changed when the 10 minutes is up? In
this case we'd prefer the client and server efficiently discuss whether the version in the client's cache is outdated
(needs to be re-downloaded) or is good for another 10 minutes. This is made possible by the validator.

# Cache Response Headers

Let's talk about how the above conversation takes place. Along with a response, the server can send an `Expires`
or `Cache-Control` header instructing the client to cache the response. What's the difference? Great question! `Expires`
provides an expiry date of a response in the form of a timestamp computed by the server. The newer and preferred `Cache-Control`
header allows us to be more specific. When both are provided, `Cache-Control` takes precedence and is the preferred method of cache
instruction so I'll mostly be discussing it here. The `Cache-Control` header can take many different values (directives) explaining
how an asset should be cached but in this post I'm going to cover three that should cover all of your needs.

** Disclaimer: just because the server instructs a client to cache something does not guarantee it will be
cached. The browser has the right to ignore cache headers and/or evict things from the cache.

### Cache-Control: max-age=xxx

The `max-age` directive instructs a client to cache a response for a number of seconds. The cache is considered "fresh"
until the max-age is reached, therefore subsequent requests for it can be served directly from the browser cache. A request
for a cached asset might look like this:

![max-age fulfilled]({{ site.baseurl }}/images/2017-01-04/max-age.png)

### Cache-Control: no-cache

Despite its name, the `no-cache` directive actually instructs the browser to cache content with the condition that it MUST be revalidated
with the server every time it is requested before being served from the cache. If the validators indicate the content has not changed, the
cached version will be served with an HTTP `304 Not Modified` response. If either the server's content differs from the client's cache or no
validation headers are provided, the content is re-downloaded and usually served with an HTTP `200 OK` response. This allows a client to
always be up to date without incurring the cost of re-downloading unchanged content.

> So `max-age=0` and `no-cache` are the same...right?

Note quite! Setting `max-age=0` marks cached content as "stale" right off the bat, however some clients can be configured to return stale content
even when they **`SHOULD`** revalidate. `no-cache` gives us a tighter contract in that content is actually considered unusable without successful
revalidation. So really `no-cache` is more of a guarantee that revalidation will happen on subsequent requests even on clients configured to
return stale responses. To force a client to revalidate stale content (older than `max-age`) the `must-revalidate` directive is necessary. Therefore
`no-cache` **=** `max-age=0, must-revalidate`.

### Cache-Control: no-store

The `no-store` directive is ruthless and instructs the client to not cache a response at all. Consequently, content must be downloaded from
the server on every request. This directive is rarely used and can be seen as a mechanism to prevent the storage of sensitive information.

# Validation Headers

Now that we've learned how to instruct the client to cache responses, let's  dig into the validation side of things. Validation headers are often
sent to clients in conjunction with cache response headers but this is not mandatory. As we've briefly discussed, when a request is made for a
stale or expired asset the client should validate its cache with the origin server by sending any and all validation headers it has associated
with the cached response. Once the server makes a determination, it will most likely respond with either a partial `304 Not Modified` response
instructing the client to use and "freshen" its cache, or a full `200 OK` response instructing the client to serve and update its cache with the
new content.

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

The `Last-Modified` validation header is a timestamp with granularity to the nearest second representing the time at which some content
was last modified. Most often it is sent with the `Expires` cache response header as they are both time-based. Upon request for an expired
or stale asset the client will send the timestampe in the form of the `If-Modified-Since: TimestampHere` request header. The server will
compare the timestamp the client sent with the actual `L-M` of the requested resource. If the server deems the client's version is outdated
it will fulfill the request with a full `200 OK` response giving the client a fresh download. Likewise if the client's version is up to date,
it will fulfill with a `304 Not Modified` instructing the client to use its cached version. The `L-M` header is a weak validator since a file
could change more than one time in a single second without the validator reflecting this.

### ETag

The `ETag` (Entity Tag) validator is by default strong. ETags are normally some string that represent the current state of a document. It is
common for an ETag to be generated based off of the hash of some file so it is an accurate representation of its contents however something as
simple as a revision string would suffice. The key here is that the ETag value is not necessarily time-based, and of course changes along with
the resource and headers it represents. ETags are sent to the server for validation in the form of the `If-None-Match: ETagHere` request header.
An ETag can be declared as "weak" with the `W/` prefix, which express uses by default.

# Experimenting with Cache Busting

Suppose you publish several assets to your site instructing clients to cache them for 3600 seconds (1 hour) but then quickly realize you need to make
a change that should be visible to everyone immediately. We haven't really covered a way to force the client to abandon its cache on demand because
there kind of isn't one, and this is still mostly true but there is a way to get around this issue by taking advantage of how some browsers treat user-initiated
requests.

When a browser makes a request it keeps track of how the request was initiated. A request's initiator is visible in the network tab of your brower's developer tools.
A request's initiator should take on one of the following values:

 1. Parser - HTML parser initiated the request
 1. Redirect - An HTTP redirect initiated the request
 1. Script - A script initiated the request
 1. Other - usually means the user

The initiator field is pretty standard but some browsers treat requests from different initiators differently. In Chrome (tested on v54 Linux + OSX), if
a user initiates a request for a site whose assets exist in the browser cache, the request for the URL *may still* get revalidated with the server. Once
validated, subsequent requests initiated by the page (like css/scripts) go through the browser cache as expected. So how can we use this to our advantage?

> I made a simple app that allows you to test the setting and removal of certain cache headers easily - see [GitHub](https://github.com/domfarolino/browser-cache-fundamentals)

First we publish some assets to our site. One of the assets in our site is a CSS file that gets pulled in through the `index.html` document.

![css-gray-bg]({{ site.baseurl }}/images/2017-01-04/css-gray-bg.png)

![grey-header]({{ site.baseurl }}/images/2017-01-04/grey-header.png)

When using the `max-age=3600` Cache-Control directive, changes to the above file may not appear in clients for another hour. As previously stated, sometimes browers
may revalidate user initiated URL requests more frequently than necessary, and in Chrome a page refresh *may* trigger this revalidation. Below is the output from an
express server revalidating a request triggered by a Chrome refresh. Notice no other page assets initiated by the parser are getting re-validated. Subsequent page requests
go through the cache as expected.

![server-root-request]({{ site.baseurl }}/images/2017-01-04/server-root-request.png)

If we make changes to our CSS nothing will change since clients, even those with revalidated markup, have no idea about this change.

![css-rebecca-bg]({{ site.baseurl }}/images/2017-01-04/css-rebecca-bg.png)

In order to tell the clients (ones that might revalidate user-initiated requests more frequently than necessary) about the change we can do the following.

![cssv2-initiate]({{ site.baseurl }}/images/2017-01-04/cssv2-initiate.png)

By changing the name or doing something as harmless as adding a query string to the filename, we've created a request for an asset that does not match the
URL of any cached items. In this case, when the browser's parser comes across this content it will be forced to hit the server.

![server-cssv2-request]({{ site.baseurl }}/images/2017-01-04/server-cssv2-request.png)

Notice in the above picture, two requests (the only necessary ones) are fulfilled, and any other page request is able to go through the cache. The client now
has the most up-to date styles and is looking rather dapper.

![purple-header]({{ site.baseurl }}/images/2017-01-04/purple-header.png)

The idea of changing a filename in some frequently revalidated markup is called "cache busting". It is common to see this in server side rendering, where the hash
generated from the contents of an asset is used as the name of that asset in the markup. This way, whenever the file changes the markup changes thus forcing the browser
to intelligently download new assets it needs.