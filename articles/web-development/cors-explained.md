---
title: "CORS Explained: Why Browsers Block Cross-Origin Requests"
description: "Understand the browser same-origin policy, CORS response headers, preflight requests, credentials, and common CORS troubleshooting steps."
slug: "cors-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Web Development"
tags:
  - "CORS"
  - "JavaScript"
  - "Web Security"
  - "API"
relatedTools: []
relatedArticles:
  - "http-request-methods-guide"
draft: false
---

# CORS Explained: Why Browsers Block Cross-Origin Requests

A web application may be hosted on one domain while its API is hosted on another. When JavaScript in a browser tries to request a resource from a different origin, the browser applies security rules that may block the page from reading the response.

Cross-Origin Resource Sharing (CORS) is a mechanism that allows a server to declare which browser origins may access its resources.

## What is an origin?

An origin is the combination of:

- Scheme, such as http or https.
- Hostname, such as example.com.
- Port, when applicable.

These addresses are different origins:

```text
https://example.com
http://example.com
https://api.example.com
https://example.com:8443
```

A difference in scheme, host, or port creates a different origin.

The browser's same-origin policy restricts how scripts from one origin interact with resources from another origin.

## What is CORS?

CORS is an HTTP-header-based mechanism used by browsers to decide whether JavaScript may access a cross-origin response.

The server can return a header such as:

```http
Access-Control-Allow-Origin: https://app.example.com
```

This tells the browser that JavaScript running at the specified origin is allowed to read the response, subject to the other CORS rules.

CORS is enforced by the browser. It is not an authentication system and does not prevent non-browser clients from making HTTP requests.

## Simple cross-origin requests

Some requests can be sent without a preflight request, depending on the method and headers. The browser sends the request and checks whether the response contains appropriate CORS headers before exposing the response to JavaScript.

A request may reach the server successfully while the browser still prevents the calling script from reading the result.

This is why a request can appear in the Network panel but fail with a CORS error in the Console.

## What is a preflight request?

For requests that are not CORS-safelisted, the browser may first send an OPTIONS request to ask whether the actual request is allowed.

For example:

```http
OPTIONS /api/orders HTTP/1.1
Origin: https://app.example.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: content-type, authorization
```

The server may respond with headers such as:

```http
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: GET, POST, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 600
```

If the response satisfies the browser's checks, it can send the actual request.

## Credentials and cookies

Cross-origin requests that include credentials require additional configuration.

The server commonly needs to return:

```http
Access-Control-Allow-Credentials: true
Access-Control-Allow-Origin: https://app.example.com
```

When credentials are included, the wildcard origin value `*` cannot be used for Access-Control-Allow-Origin. The server must specify an allowed origin.

Cookie behavior also depends on cookie attributes such as SameSite and Secure, as well as browser privacy settings.

## Common CORS errors

### Missing Access-Control-Allow-Origin

The server did not return an origin permission that matches the calling page.

### Wildcard used with credentials

The server returned a wildcard origin while the browser request includes credentials. Use an explicit allowed origin.

### Preflight request failed

The OPTIONS request may be blocked, redirected, rejected, or missing the required allow-methods or allow-headers response.

### Custom authorization header not allowed

The server's Access-Control-Allow-Headers response must include the requested header, such as Authorization, where required.

### Origin mismatch

The configured origin may differ by scheme, hostname, or port. Compare the browser's Origin header with the server configuration.

## How to troubleshoot CORS

1. Open the browser developer tools.
2. Select the Network panel.
3. Find the failed API request and any preceding OPTIONS request.
4. Inspect the Origin request header.
5. Check Access-Control-Allow-Origin and the other CORS response headers.
6. Verify the API server or reverse proxy configuration.
7. Test again from the exact frontend origin.

Do not "fix" CORS by allowing every origin with credentials or by disabling browser security. Use a narrow allowlist of trusted origins.

## CORS is not a replacement for authentication

A CORS policy controls whether browser JavaScript can read a response. It does not prove the user is authorized to access the resource.

Every API must still perform authentication and authorization on the server. Do not rely on CORS to protect private API endpoints.

## Frequently asked questions

### Does CORS block curl or Postman?

CORS is a browser security mechanism. Tools such as curl and Postman do not enforce browser CORS rules in the same way.

### Why does the request reach the server but JavaScript cannot read it?

The browser may send the request but block access to the response when the CORS headers are missing or invalid.

### Can I allow multiple origins?

Yes. A server can validate the incoming Origin against an allowlist and return the matching origin. It should not blindly reflect arbitrary origins.

## Further reading

- [MDN: Cross-Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS)
- [MDN: Same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)
- [Fetch Standard: CORS protocol](https://fetch.spec.whatwg.org/#http-cors-protocol)

---
