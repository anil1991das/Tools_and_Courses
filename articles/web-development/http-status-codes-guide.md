---
title: "HTTP Status Codes Explained: 2xx, 3xx, 4xx, and 5xx"
description: "Understand the five HTTP status-code classes and learn to diagnose common responses such as 200, 201, 301, 400, 401, 403, 404, and 500."
slug: "http-status-codes-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Web Development"
tags:
  - "HTTP"
  - "Status Codes"
  - "API Debugging"
  - "Web Development"
relatedTools: []
relatedArticles: []
draft: false
---

# HTTP Status Codes Explained: 2xx, 3xx, 4xx, and 5xx

When a browser or application sends an HTTP request, the server returns a response status code. The code gives a high-level indication of how the request was handled. It helps developers troubleshoot APIs, redirects, authentication problems, missing resources, and server failures.

A status code does not explain every detail. The response body and headers often provide the additional information needed to understand what happened.

## The five status-code classes

HTTP status codes are grouped by their first digit:

| Class | Range | Meaning |
|---|---|---|
| 1xx | 100–199 | Informational: request processing continues |
| 2xx | 200–299 | Success: request was received, understood, and accepted |
| 3xx | 300–399 | Redirection: further action is needed to complete the request |
| 4xx | 400–499 | Client error: the request has a problem or cannot be fulfilled as sent |
| 5xx | 500–599 | Server error: the server failed to fulfill an apparently valid request |

The status code is part of the HTTP response. The server may also include a message body, headers, or a redirect location.

## Common 2xx success codes

### 200 OK

The request succeeded. A GET response commonly returns the requested representation.

### 201 Created

The request successfully created a resource. The response may include a Location header identifying the new resource.

### 202 Accepted

The request has been accepted for processing, but processing may not be complete. It is often used for asynchronous jobs.

### 204 No Content

The request succeeded and the server has no response content to send. This is common after successful deletion or an update where no representation is returned.

### 206 Partial Content

The server is returning a requested range of a resource. This is commonly used for resumable or partial downloads.

## Common 3xx redirection codes

### 301 Moved Permanently

The target resource has a permanent redirect. Clients and search engines may update references based on the redirect and related signals.

### 302 Found

The resource is temporarily available at another location. Historically, clients may change POST to GET when following some 302 responses, which is why more specific redirect codes exist.

### 303 See Other

The client should retrieve the resource at the new URI using GET. It is commonly used after a form submission to redirect to a result page.

### 304 Not Modified

The client's cached representation can be reused because the resource has not changed according to the conditional request. A 304 response normally has no body.

### 307 Temporary Redirect

The client should temporarily use another URI and preserve the request method and body when following the redirect.

### 308 Permanent Redirect

Like 301, but preserves the method and body when following the redirect.

## Common 4xx client-error codes

| Code | Name | Typical meaning |
|---|---|---|
| 400 | Bad Request | The request is malformed or cannot be processed |
| 401 | Unauthorized | Authentication is required or failed |
| 403 | Forbidden | The server refuses the request |
| 404 | Not Found | The resource was not found |
| 405 | Method Not Allowed | The method is not supported for the target resource |
| 406 | Not Acceptable | The server cannot provide a representation matching the request's negotiation headers |
| 408 | Request Timeout | The server timed out waiting for the request |
| 409 | Conflict | The request conflicts with current resource state |
| 410 | Gone | The resource is no longer available and this is likely permanent |
| 413 | Content Too Large | The request content exceeds the server's accepted limit |
| 415 | Unsupported Media Type | The request content format is not supported |
| 422 | Unprocessable Content | The content type and syntax are understood, but the instructions cannot be processed |
| 429 | Too Many Requests | The client has sent too many requests in a period |

### 401 versus 403

These two codes are often confused.

A 401 response means the request lacks valid authentication credentials for the target resource. The response generally includes a WWW-Authenticate challenge when appropriate.

A 403 response means the server understood the request but refuses to fulfill it. The caller may be authenticated but lack permission, or the server may refuse access for another reason.

The exact response should not disclose sensitive information about private resources.

## Common 5xx server-error codes

| Code | Name | Typical meaning |
|---|---|---|
| 500 | Internal Server Error | Unexpected server-side condition |
| 501 | Not Implemented | The server does not support the functionality required to fulfill the request |
| 502 | Bad Gateway | A gateway or proxy received an invalid response from an upstream server |
| 503 | Service Unavailable | The server is temporarily unable to handle the request |
| 504 | Gateway Timeout | A gateway or proxy did not receive a timely upstream response |

A 5xx status does not necessarily mean the origin application is the only component at fault. A reverse proxy, load balancer, DNS issue, or upstream dependency may be involved.

## How to diagnose a status code

A practical debugging sequence is:

1. Open the browser's developer tools or inspect the API client's response.
2. Check the request URL, method, and request headers.
3. Record the status code and response headers.
4. Read the response body, if present.
5. Compare the result with the API documentation.
6. Check server logs and upstream services when the issue is server-side.

For a failed API request, also confirm whether the request body uses the expected content type and data structure.

## Status codes do not replace application error details

An API should return useful, safe error details in its response body. A 400 response might explain which field failed validation, while a 409 response might describe a version conflict.

Do not return stack traces, internal file paths, database credentials, or other sensitive implementation details to public clients.

## Common troubleshooting examples

### A page returns 404

Check the path, route configuration, case sensitivity, and whether the resource exists. A 404 may also be deliberately returned to avoid revealing whether a protected resource exists.

### An API returns 415

Check the Content-Type header and whether the server accepts the submitted media type. For JSON requests, this is commonly application/json.

### A request returns 429

Slow down the request rate and inspect Retry-After if the server provides it. Implement backoff rather than retrying in a tight loop.

### A request returns 502 or 504

Check the upstream service, proxy configuration, network connection, and timeout settings. Repeatedly refreshing the page may not solve an infrastructure issue.

## Frequently asked questions

### Is 200 always returned when an API works?

No. A successful request may return 201, 202, 204, 206, or another 2xx code depending on the operation.

### Does 404 mean the server is down?

No. A 404 means the requested resource was not found at the target. The server itself may be working normally.

### Should every API error return 400?

No. Use status codes that accurately describe the error condition. Authentication, authorization, conflicts, rate limiting, and server failures have more specific status codes.

## Further reading

- [MDN: HTTP response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status)
- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110)
- [RFC 6585: Additional HTTP Status Codes](https://www.rfc-editor.org/rfc/rfc6585)

---
