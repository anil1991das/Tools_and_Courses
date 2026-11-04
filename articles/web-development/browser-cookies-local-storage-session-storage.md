---
title: "Cookies vs Local Storage vs Session Storage"
description: "Compare browser cookies, localStorage, and sessionStorage, including lifetime, request behavior, security considerations, and suitable use cases."
slug: "browser-cookies-local-storage-session-storage"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Web Development"
tags:
  - "Cookies"
  - "Web Storage"
  - "JavaScript"
  - "Browser Security"
relatedTools: []
relatedArticles: []
draft: false
---

# Cookies vs Local Storage vs Session Storage

Web applications need to store information between page loads. A website might remember a user preference, keep a temporary form value, or maintain an authenticated session.

Browsers provide multiple storage mechanisms, including cookies, localStorage, and sessionStorage. They differ in lifetime, request behavior, access controls, and security implications.

## What are cookies?

Cookies are small pieces of data stored by the browser and associated with a domain and path. Depending on their attributes, the browser sends matching cookies with HTTP requests.

A server commonly uses cookies for session identifiers, preferences, or other state that must accompany requests.

Important cookie attributes include:

- **Secure:** sends the cookie only over secure connections.
- **HttpOnly:** prevents JavaScript from reading the cookie through document.cookie.
- **SameSite:** controls when cookies are sent with cross-site requests.
- **Expires or Max-Age:** controls persistence.
- **Domain and Path:** influence where the cookie is sent.

Cookies are automatically attached to matching requests, which is useful for sessions but also creates cross-site request risks that must be managed.

## What is localStorage?

localStorage provides key-value storage scoped to an origin. Data generally remains available across browser sessions until it is removed by the application or user, subject to browser storage policies.

JavaScript can read and write localStorage:

~~~javascript
localStorage.setItem("theme", "dark");

const theme = localStorage.getItem("theme");

localStorage.removeItem("theme");
~~~

localStorage is not automatically sent to the server with every HTTP request. An application must read the value and send it if needed.

## What is sessionStorage?

sessionStorage is also key-value storage scoped to an origin, but its lifetime is associated with a page session, typically a browser tab.

~~~javascript
sessionStorage.setItem("checkout-step", "address");

const step = sessionStorage.getItem("checkout-step");

sessionStorage.removeItem("checkout-step");
~~~

It is useful for temporary page-session data that should not persist in the same way as localStorage.

## Comparison

| Feature | Cookies | localStorage | sessionStorage |
|---|---|---|---|
| Sent automatically with matching HTTP requests | Yes | No | No |
| JavaScript access | Usually, unless HttpOnly | Yes | Yes |
| Typical lifetime | Session or configured expiry | Persistent until cleared | Page session |
| Capacity | Relatively small | Larger, browser-dependent | Larger, browser-dependent |
| Scope | Domain/path rules | Origin | Origin and page session |
| HttpOnly protection | Available | Not available | Not available |

Storage limits and eviction behavior vary by browser. Do not rely on a specific capacity without checking the target environment.

## What should be stored where?

### Cookies

Use cookies when the server needs the browser to attach a value to matching requests, such as a session identifier. For sensitive session cookies, use Secure, HttpOnly, and an appropriate SameSite setting.

### localStorage

Use localStorage for non-sensitive preferences that should persist, such as a theme choice or a dismissed help notice.

### sessionStorage

Use sessionStorage for temporary tab-specific state, such as a multi-step form progress indicator that should not necessarily survive closing the tab.

Do not store long-lived secrets or sensitive authentication credentials in browser-accessible storage without understanding the risks.

## Security considerations

JavaScript running on a page can read localStorage and sessionStorage. If an attacker can execute malicious script through a cross-site scripting (XSS) vulnerability, they may be able to read these values.

HttpOnly cookies cannot be read directly through JavaScript, which can reduce token theft through some XSS scenarios. However, XSS can still perform actions as the user while the page is compromised.

Cookies also require protection against cross-site request forgery (CSRF) when they are used for authentication. SameSite settings, CSRF tokens, and server-side request validation can be part of the defense.

No storage choice replaces secure application design.

## Common mistakes

### Storing passwords in localStorage

Do not store users' passwords in browser storage. Authentication should be handled through a secure server-side design.

### Assuming sessionStorage is secure

It is accessible to JavaScript on the origin. It is not a secure vault.

### Forgetting cookie attributes

Session cookies should be configured with suitable security attributes and a deliberate lifetime.

### Using localStorage for large or critical datasets

Browser storage can be cleared, evicted, or unavailable. Important data should be persisted according to an explicit server or backup strategy.

### Treating cookies as automatically safe

Cookies are sent with requests and may be exposed to CSRF risks. Configure them carefully and protect authenticated endpoints.

## Frequently asked questions

### Does localStorage get sent to the server?

No. The browser does not automatically attach localStorage values to HTTP requests. Application code must explicitly include data in a request.

### Does sessionStorage survive browser restart?

It is associated with a page session and generally does not persist as durable storage after the tab/session is closed. Browser behavior around session restoration may vary.

### Are cookies better than localStorage for authentication?

HttpOnly Secure cookies are commonly used for server-managed sessions because JavaScript cannot read them directly. The best design depends on the application, CSRF protections, cross-origin requirements, and session architecture.

## Further reading

- [MDN: Using HTTP cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies)
- [MDN: Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
- [OWASP: HTML5 Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html)

---
