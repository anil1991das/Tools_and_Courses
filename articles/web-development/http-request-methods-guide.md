---
title: "HTTP Request Methods Explained: GET, POST, PUT, PATCH, and DELETE"
description: "Understand common HTTP methods, safe and idempotent requests, and how to choose the right method when designing or debugging an API."
slug: "http-request-methods-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Web Development"
tags:
  - "HTTP"
  - "REST API"
  - "Web Development"
  - "Backend"
relatedTools:
  - title: "URL Parser"
    url: "/url-parser"
relatedArticles: []
draft: false
---

# HTTP Request Methods Explained: GET, POST, PUT, PATCH, and DELETE

When a browser loads a page or an application calls an API, it sends an HTTP request to a server. The request method communicates the intended action. Choosing the right method makes an API easier to understand, test, cache, and maintain.

HTTP methods are sometimes called HTTP verbs. They describe the semantics of a request, not simply the name of a programming function.

## What is an HTTP request method?

An HTTP request contains a method, a target, headers, and sometimes a body. For example:

~~~http
GET /api/products/42 HTTP/1.1
Host: example.com
Accept: application/json
~~~

The method is GET. It tells the server that the client wants to retrieve a representation of the product resource.

The server's response includes a status code, headers, and possibly a body. The method and response together define how the request should be interpreted.

## Common HTTP methods

| Method | Typical purpose | Common use |
|---|---|---|
| GET | Retrieve a resource representation | Read product details |
| POST | Submit data for processing | Create an order or run an operation |
| PUT | Create or replace a resource at a known target URI | Replace a complete profile representation |
| PATCH | Apply a partial modification | Change one profile field |
| DELETE | Request removal of a resource | Delete a saved item |
| HEAD | Retrieve response metadata without a response body | Check headers or resource availability |
| OPTIONS | Ask about communication options | Discover supported methods or support CORS preflight |

The actual behavior is defined by the server and the resource's contract. A method name does not guarantee that an implementation follows the intended semantics.

## GET: retrieve information

GET is intended to retrieve a representation of a resource. A well-designed GET request should not cause an application-level state change such as placing an order or deleting a record.

Example:

~~~http
GET /api/products/42 HTTP/1.1
Host: example.com
Accept: application/json
~~~

A server might respond:

~~~http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 42,
  "name": "Wireless Keyboard",
  "available": true
}
~~~

GET responses can often be cached when the response headers and request context permit it. Avoid putting secrets in query strings because URLs can be recorded in logs and browser history.

## POST: submit data or request processing

POST asks the target resource to process the request content according to its own semantics. It is commonly used to create a resource when the server chooses the new resource URI, or to trigger a process.

~~~http
POST /api/orders HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "productId": 42,
  "quantity": 1
}
~~~

The server may create an order and return 201 Created with a Location header identifying the new resource.

POST is not generally idempotent. Repeating the same request may create multiple orders unless the application implements an idempotency mechanism.

## PUT: create or replace a resource

PUT requests that the target resource's state be created or replaced with the supplied representation, according to the resource's semantics.

~~~http
PUT /api/users/42/preferences HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "theme": "dark",
  "language": "en"
}
~~~

A PUT request is defined as idempotent: repeating the same request should have the same intended effect as sending it once. This does not mean the server must return exactly the same response every time.

## PATCH: partially modify a resource

PATCH applies partial modifications to a resource. The patch document's format and meaning depend on the API.

For example, an API might accept a JSON object containing only the field to change:

~~~http
PATCH /api/users/42/preferences HTTP/1.1
Content-Type: application/json

{
  "theme": "light"
}
~~~

PATCH is not automatically idempotent. Whether repeated requests have the same effect depends on the patch format and operation.

## DELETE: request removal

DELETE asks the server to remove the association between the target resource and its current functionality. It is idempotent in HTTP semantics, even if repeated requests receive different status codes.

~~~http
DELETE /api/cart/items/42 HTTP/1.1
Host: example.com
~~~

A server might return 204 No Content when the operation succeeds without a response body, or 200 OK with a result representation.

## Safe versus idempotent methods

Two important HTTP concepts are safety and idempotency.

**Safe** means the client does not request a state-changing operation. GET and HEAD are safe methods. A server may still update logs, counters, or analytics internally; safety refers to the intended semantics of the request.

**Idempotent** means that making the same request multiple times has the same intended effect on the server as making it once.

| Method | Safe | Idempotent |
|---|---|---|
| GET | Yes | Yes |
| HEAD | Yes | Yes |
| OPTIONS | Yes | Yes |
| PUT | No | Yes |
| DELETE | No | Yes |
| POST | No | No, in general |
| PATCH | No | Not necessarily |

Idempotency is useful when clients retry requests after a timeout. If a client does not know whether the server processed a request, repeating an idempotent request is generally easier to reason about than repeating a non-idempotent operation.

## How to choose a method when designing an API

Ask what the client is trying to do:

- Read a resource: GET.
- Submit data or request processing: POST.
- Replace a resource representation at a known URI: PUT.
- Apply a partial change: PATCH.
- Request deletion: DELETE.
- Inspect headers only: HEAD.
- Discover communication options: OPTIONS.

Keep the API consistent. Do not use GET for operations that change business data, and document any application-specific behavior.

## Common mistakes

### Using GET to change state

A link that triggers a purchase or deletion should not use GET. Safe methods may be followed by crawlers, prefetchers, or caches in ways that make state-changing GET endpoints dangerous.

### Assuming POST retries are harmless

A timeout does not tell the client whether the server completed the operation. Use an application-level idempotency key or another documented retry strategy for operations such as payments or order creation.

### Treating PUT and PATCH as interchangeable

PUT generally represents replacement semantics, while PATCH describes partial modification. The API contract should make the difference clear.

### Assuming the method determines authorization

A server must still authenticate the caller and authorize the requested operation. Choosing POST or DELETE does not provide access control.

## Inspect a URL before calling an API

Before debugging a request, examine its scheme, hostname, path, and query string. The [iamnoobs URL Parser](https://iamnoobs.com/url-parser) can help inspect URL components. It does not send the HTTP request or validate the server's authorization rules.

## Frequently asked questions

### Is POST always used to create a record?

No. POST asks the target resource to process the submitted content. Creating a resource is common, but POST can also trigger other processing.

### Is PUT always safe to retry?

PUT is idempotent by definition, but the client must still consider authentication, authorization, request consistency, and the server's actual implementation.

### Does DELETE always remove data permanently?

No. DELETE semantics are defined by the API and resource model. A server may perform a soft delete, schedule deletion, or remove an association rather than immediately erase every stored copy.

## Further reading

- [MDN: HTTP request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods)
- [MDN: Overview of HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview)
- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110)

---

**Related tool:** [iamnoobs URL Parser](https://iamnoobs.com/url-parser)
