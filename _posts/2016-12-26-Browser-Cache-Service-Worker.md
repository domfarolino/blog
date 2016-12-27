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

When building a Node app with express we have the choice to not worry about setting any response
headers, including the ones in charge of telling the browser how to cache our content. In this post
I want to give a brief overview of browser caching mechanics and the control we have. I then want to
talk about how the service worker's lifecycle and cache play into all of this.

# Browser Cache Fundamentals

When the browser receives content from the server it also gets a list of HTTP headers. The headers contain
data about the actual content in the request and essentially allow the client and server to talk about the
data in the request. The browser (client) can use some of the headers to figure out how it can minimize the
amount of data that will be transferred over the network for **future** requests of the same content. The
client uses two types of headers for this. The first type will tell the client whether it can cache received
content and if so for how long. The second type will act as a "fingerprint" for some asset. This is like
hashing in that we generate a unique identifier for a large piece of data so we can efficiently compare this
data to other data.

For example, say the client receives a **massive** file from the server. The server might say "Hey client, you
can cache this for up to 10 minutes". The client is then allowed to use its cached version of the asset for up
to 10 minutes of requests before it must goes back to the server asking for an updated copy. But what if the copy
on the server hasn't changed? In this situation we'd prefer the client and server efficiently discuss whether the
version of the asset that the client has is outdated or good for another 10 minutes.

## Headers

Let's talk about how exactly the above conversation takes place between client and server. The first type of information
that indicates whether an asset can be cached or not can be expressed with either an `Expires` or `Cache-Control` header.
Anymore all you need is just a `Cache-Control` These
headers are used to tell the client how long it can use some asset without going back to the server. It is important 

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