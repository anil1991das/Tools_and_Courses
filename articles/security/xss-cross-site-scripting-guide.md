---
title: "Cross-Site Scripting (XSS): Types, Risks, and Prevention"
description: "Understand reflected, stored, and DOM-based XSS, how unsafe rendering creates risk, and how to defend web applications."
slug: "xss-cross-site-scripting-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Security Basics"
tags:
  - "XSS"
  - "Web Security"
  - "JavaScript"
  - "Secure Coding"
relatedTools: []
relatedArticles:
  - "web-accessibility-basics-guide"
  - "https-tls-explained"
draft: false
---

# Cross-Site Scripting (XSS): Types, Risks, and Prevention

Cross-Site Scripting (XSS) is a web security vulnerability in which an attacker can cause a website to execute unintended script in another user's browser. It often occurs when an application places untrusted input into a page without applying the protections required by that output context.

XSS can expose user data, perform actions through a victim's authenticated session, or alter what the user sees. The right defense is to treat untrusted input carefully at every point where it is processed and rendered.

## What is XSS?

A browser interprets HTML, JavaScript, CSS, and other web content. If an application mixes untrusted data into executable or markup contexts without suitable handling, the browser may interpret that data as code rather than ordinary text.

The vulnerability is not simply that a user can enter special characters. It occurs when the application uses untrusted data in a context where the browser gives it unintended meaning.

## Common types of XSS

### Stored XSS

Stored XSS occurs when unsafe content is saved, such as in a comment, profile field, or message, and later rendered to users.

Because the data persists, multiple visitors may be affected.

### Reflected XSS

Reflected XSS occurs when request data is included in a response without appropriate context-specific handling.

For example, a search page may display the search term. The application must ensure that the term is rendered as text, not interpreted as markup or script.

### DOM-based XSS

DOM-based XSS occurs when client-side JavaScript uses untrusted data in an unsafe DOM operation.

The vulnerability may be entirely in browser-side code, even when the server returns a static page.

## Why output context matters

Different browser contexts require different handling. HTML text, HTML attributes, JavaScript strings, CSS values, and URLs are parsed differently.

A single generic escaping function is not correct for every context. Use a framework's safe templating mechanisms and a context-aware encoding strategy.

For ordinary text, prefer text APIs such as textContent rather than inserting strings as HTML.

~~~javascript
const message = document.querySelector("#message");
message.textContent = userProvidedText;
~~~

This treats the provided value as text instead of parsing it as markup.

Avoid using innerHTML with untrusted data unless a carefully reviewed sanitization process is required and correctly configured.

## Input validation versus output encoding

Input validation checks whether data conforms to the application's expected format, such as a valid identifier or an allowed date.

Output encoding ensures data is represented safely in the context where it is displayed.

Validation is useful but is not a replacement for output encoding. A legitimate name can contain punctuation and still need safe rendering.

Use allowlists for structured values when possible, but do not assume that rejecting a few suspicious characters provides complete XSS protection.

## Use a trusted HTML sanitizer when HTML is required

Some applications intentionally allow users to submit rich text. In that case, escaping all markup may not meet the product requirement.

Use a well-maintained HTML sanitizer with an explicit allowlist of permitted elements, attributes, and URL schemes. Keep the sanitizer updated and avoid trying to build a sanitizer with regular expressions.

Sanitization must be appropriate for the exact context in which the content will be inserted.

## Content Security Policy

Content Security Policy (CSP) is a browser security mechanism that can restrict where a page loads scripts, styles, images, and other resources.

A carefully configured CSP can reduce the impact of some injection bugs. It is a defense-in-depth control, not a substitute for safe rendering.

Prefer nonce- or hash-based script policies where suitable. Avoid broad unsafe-inline allowances unless the risk is understood and a migration plan exists.

Deploy CSP in report-only mode first where practical, review violations, and then enforce a tested policy.

## Secure cookies and session protection

Use appropriate cookie attributes for session cookies, including Secure, HttpOnly, and a suitable SameSite policy.

HttpOnly prevents JavaScript from reading a cookie through document.cookie, but it does not stop an XSS payload from performing actions through the compromised page. Preventing the injection remains essential.

## How to review an application for XSS risk

1. Identify all places where user-controlled data enters the application.
2. Trace where that data is stored, transformed, and rendered.
3. Review DOM operations that interpret strings as HTML or executable content.
4. Check the framework's escaping behavior and any bypass or raw-rendering APIs.
5. Verify that rich-text input passes through a maintained sanitizer.
6. Review CSP and cookie configuration as additional controls.
7. Add regression tests for affected rendering paths.

Use authorized test environments and established application-security testing processes.

## Common mistakes

### Trusting client-side validation alone

Clients can be bypassed. Validate important data on the server and render it safely wherever it appears.

### Escaping for the wrong context

HTML text encoding is not interchangeable with JavaScript, CSS, attribute, or URL handling.

### Assuming CSP fixes unsafe rendering

CSP can reduce impact but does not correct the underlying data-handling vulnerability.

### Using innerHTML for ordinary text

Use textContent or framework-safe text interpolation when markup is not required.

### Writing a custom sanitizer

HTML parsing is complex. Use a maintained sanitizer and carefully review its configuration.

## Frequently asked questions

### Is XSS the same as SQL injection?

No. XSS affects how a browser interprets web content. SQL injection affects how a database interprets a query. Both can result from mixing untrusted input into a language context without proper controls.

### Does HTTPS prevent XSS?

No. HTTPS protects data in transit. It does not prevent a vulnerable application from generating unsafe HTML or JavaScript.

### Is input validation enough to prevent XSS?

No. Use validation where it makes sense, but also apply context-appropriate output encoding and safe rendering APIs.

## Further reading

- [OWASP: Cross Site Scripting Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- [MDN: Cross-site scripting](https://developer.mozilla.org/en-US/docs/Web/Security/Attacks/XSS)
- [W3C: Content Security Policy](https://www.w3.org/TR/CSP3/)

---
