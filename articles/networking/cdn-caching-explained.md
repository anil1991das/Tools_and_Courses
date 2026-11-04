---
title: "CDN and Caching Explained: Deliver Web Content Faster"
description: "Learn how content delivery networks cache assets near users, understand cache headers and invalidation, and avoid serving stale content."
slug: "cdn-caching-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Networking"
tags:
  - "CDN"
  - "Caching"
  - "Web Performance"
  - "HTTP"
relatedTools: []
relatedArticles:
  - "dns-records-explained"
  - "https-tls-explained"
draft: false
---

# CDN and Caching Explained: Deliver Web Content Faster

A website may have users in many locations. If every request must travel to one origin server, distance and origin load can affect performance.

A Content Delivery Network (CDN) uses a distributed network of edge locations to deliver content closer to users. Caching allows frequently requested responses to be reused rather than regenerated or fetched from the origin every time.

## What is a CDN?

A CDN is a distributed system that helps deliver web content through points of presence closer to end users.

A typical request flow is:

1. A browser requests an asset or page.
2. DNS and routing direct the request to a suitable CDN edge.
3. The edge checks whether a reusable cached response is available.
4. If the response is fresh, the edge returns it.
5. If the response is missing or stale, the CDN may contact the origin.
6. The response may then be cached according to policy.

The exact routing, cache behavior, and origin-fetch process depend on the CDN provider and configuration.

## What is a cache hit and cache miss?

A **cache hit** occurs when a suitable fresh response is already available in the cache.

A **cache miss** occurs when the requested content is not available or cannot be reused. The CDN may fetch it from the origin.

A cache hit can reduce origin traffic and response time. A cache miss adds an origin request and may take longer.

## Static and dynamic content

Static assets such as versioned JavaScript, CSS, images, and fonts are often good candidates for caching.

Dynamic or personalized content needs more careful cache rules. A response containing one user's account details must not be served to another user by a shared cache.

Use explicit cache policies and understand how the CDN treats cookies, authorization headers, query strings, and request methods.

## Important HTTP cache headers

### Cache-Control

Cache-Control communicates caching directives.

Example:

~~~http
Cache-Control: public, max-age=31536000, immutable
~~~

This policy is often used for versioned static assets whose URLs change when their content changes.

A private response can use:

~~~http
Cache-Control: private, max-age=0
~~~

The correct directive depends on the content and privacy requirements.

### ETag

An ETag is a validator for a representation. A client can send If-None-Match to ask whether the cached representation is still current.

If the server determines the representation has not changed, it can return 304 Not Modified.

### Last-Modified

Last-Modified gives a time-based validator. Clients may send If-Modified-Since in a later request.

Time-based validation can be useful, but timestamps may have less precision than content-based validators.

## Cache-control strategy for static assets

A common approach is to use content-hashed filenames:

~~~text
app.8b7c2d.js
styles.4a1f9e.css
~~~

When the content changes, the filename changes. The old asset can remain cached for a long period while the updated page references the new filename.

This makes long cache durations safer for immutable, versioned assets.

HTML documents often need a shorter cache lifetime because they contain references to current asset names and deployment metadata.

## Cache invalidation

When content changes, an old cached response may remain available until its freshness lifetime expires or the cache is explicitly purged.

Common strategies include:

- Versioned or content-hashed URLs.
- Shorter TTL for frequently changing content.
- Purge or invalidation APIs.
- Revalidation through ETag or Last-Modified.
- Stale-while-revalidate behavior when appropriate.

A purge is not always instant across every edge. Understand provider-specific propagation and cache-key behavior.

## Cache keys and query strings

A CDN cache key determines which requests are considered equivalent for caching.

For example, these requests may be treated as different:

~~~text
/image.jpg?width=400
/image.jpg?width=800
~~~

A provider may include all, some, or none of the query parameters in the cache key based on configuration.

Incorrect cache-key settings can cause the wrong variant to be served. Treat user-specific query values and cookies carefully.

## CDN versus browser cache

| Browser cache | CDN cache |
|---|---|
| Stored on the user's device | Stored at distributed network locations |
| Reused by that user's browser | May be shared among many users |
| Controlled by browser cache policies | Controlled by CDN and origin cache policies |
| Reduces repeated user-to-network requests | Can reduce origin traffic and network distance |

Both layers can work together. A browser may reuse an asset locally while a CDN serves the asset to other users.

## Common mistakes

### Caching personalized responses publicly

A shared cache must not expose private user content. Use appropriate private or no-store policies where required.

### Setting a long TTL for unversioned assets

Users may continue receiving outdated content. Use versioned filenames or an invalidation strategy.

### Ignoring cache keys

If the response varies by query, cookie, language, or authorization, the cache key and Vary behavior must be designed correctly.

### Purging everything too often

Frequent global purges reduce cache efficiency. Prefer versioned assets and targeted invalidation.

### Assuming CDN caching makes an origin secure

A CDN can add protection and availability features, but origin access controls, TLS configuration, application security, and monitoring remain necessary.

## Frequently asked questions

### Does a CDN always make a site faster?

No. The benefit depends on user location, content, cache hit rate, routing, origin performance, and configuration.

### Can dynamic pages be cached?

Some dynamic pages can be cached safely when the response is shared and varies only by controlled dimensions. Personalized pages require careful private caching or no-store policies.

### What is a 304 response?

It indicates that a conditional request's cached representation can be reused because it has not changed according to the validator.

## Further reading

- [MDN: HTTP Caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Caching)
- [Cloudflare: What is a CDN?](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/)
- [RFC 9111: HTTP Caching](https://www.rfc-editor.org/rfc/rfc9111)

---
