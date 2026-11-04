---
title: "REST API Design Basics: Resources, Methods, and Status Codes"
description: "Learn the core ideas behind REST-style APIs, resource-oriented URLs, HTTP methods, status codes, and predictable JSON responses."
slug: "rest-api-design-basics"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Web Development"
tags:
  - "REST API"
  - "API Design"
  - "HTTP"
  - "Backend Development"
relatedTools: []
relatedArticles:
  - "http-request-methods-guide"
  - "http-status-codes-guide"
draft: false
---

# REST API Design Basics: Resources, Methods, and Status Codes

REST is an architectural style for distributed systems. Many web APIs are described as REST APIs, although not every JSON endpoint follows all REST constraints.

A well-designed resource-oriented API makes it predictable for clients to discover resources, send operations, handle errors, and evolve over time.

## What is a REST-style API?

REST stands for Representational State Transfer. It describes architectural constraints such as a client-server separation, stateless interactions, cacheability, a uniform interface, and a layered system.

In everyday development, a REST-style API commonly exposes resources through HTTP URLs and uses HTTP methods to retrieve or modify them.

For example:

~~~text
GET    /api/products
GET    /api/products/42
POST   /api/products
PUT    /api/products/42
PATCH  /api/products/42
DELETE /api/products/42
~~~

These endpoints describe operations on product resources rather than using action names in every URL.

## Design URLs around resources

Use nouns that describe resources:

- /users
- /orders
- /products
- /products/42/reviews

A collection URL generally identifies a group of resources. An item URL identifies one resource.

Avoid designing every route as a verb such as /getProduct, /createProduct, or /deleteProduct when HTTP methods can express the operation more consistently.

Some operations are not naturally CRUD operations. It can be reasonable to model domain actions explicitly when they are meaningful business operations.

## Use HTTP methods consistently

A common mapping is:

| Operation | Method | Example |
|---|---|---|
| List resources | GET | GET /api/products |
| Read one resource | GET | GET /api/products/42 |
| Create a resource | POST | POST /api/products |
| Replace a resource | PUT | PUT /api/products/42 |
| Partially update | PATCH | PATCH /api/products/42 |
| Delete a resource | DELETE | DELETE /api/products/42 |

Understand the semantics of the method, especially safety and idempotency. These properties affect caching, retries, and client behavior.

## Return meaningful status codes

Use status codes that describe the outcome:

- 200 OK for a successful request with a representation.
- 201 Created when a resource is created.
- 202 Accepted when processing is accepted but incomplete.
- 204 No Content when success has no response body.
- 400 Bad Request for malformed requests.
- 401 Unauthorized for missing or invalid authentication.
- 403 Forbidden when access is refused.
- 404 Not Found when a resource is unavailable.
- 409 Conflict for a state conflict.
- 422 Unprocessable Content for understood content that cannot be processed.
- 500 Internal Server Error for unexpected server failures.

Do not return 200 for every outcome and place all meaning into an undocumented message string.

## Use predictable JSON structures

A resource response might look like:

~~~json
{
  "id": 42,
  "name": "Wireless Keyboard",
  "price": 2499,
  "available": true
}
~~~

Use consistent property naming, data types, and date formats. Document which fields are optional, nullable, read-only, or required.

For an error response, provide enough detail for a client to correct the request without exposing internal implementation details.

## Pagination and filtering

Large collections should not require clients to download every record in one response.

An API may support query parameters such as:

~~~text
GET /api/products?page=2&limit=20
GET /api/products?category=accessories
GET /api/products?sort=price
~~~

The API should document default page size, maximum page size, sorting behavior, and how clients know whether more results are available.

For frequently changing collections, cursor-based pagination can be more stable than page numbers.

## Authentication and authorization

Authentication identifies the caller. Authorization determines what that caller may do.

Common approaches include session cookies, API keys, OAuth 2.0 access tokens, and other credential models. The right approach depends on the client type and trust boundary.

Do not rely on an obscure URL or a CORS rule to protect a resource. Enforce permissions on the server for every protected operation.

## Versioning and compatibility

APIs evolve. Adding an optional field may be backward-compatible for many clients, while renaming or removing a field can break existing integrations.

Document compatibility expectations. Versioning strategies may use URL paths, headers, or media types. Avoid introducing a new major version for every small change.

## Common API design mistakes

### Using a different response shape for every endpoint

Consistency makes clients easier to build and test. Define common conventions for errors, pagination, timestamps, and identifiers.

### Exposing internal database structure

An API representation should express the public resource contract, not require clients to understand database table names or joins.

### Leaking sensitive errors

Do not return stack traces, SQL queries, filesystem paths, or secrets to API clients.

### Ignoring rate limits

Protect APIs against abuse and accidental overload. Document limits and return appropriate responses, such as 429 Too Many Requests, when rate limits are exceeded.

### Treating REST as just JSON over HTTP

REST is an architectural style with constraints. An API can use HTTP and JSON without being fully RESTful.

## Frequently asked questions

### Does every REST API need CRUD endpoints?

No. Resources and operations should reflect the domain. Some actions may be better represented by dedicated resources or domain-specific endpoints.

### Is REST the same as HTTP?

No. HTTP is a protocol. REST is an architectural style that can use HTTP as its application protocol.

### Should APIs always return JSON?

JSON is common for web APIs, but REST does not require JSON. Representations can use different media types based on the API contract.

## Further reading

- [Roy Fielding: Architectural Styles and the Design of Network-based Software Architectures](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)
- [MDN: HTTP request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods)
- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110)

---
