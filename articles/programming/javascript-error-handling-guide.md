---
title: "JavaScript Error Handling Explained: try, catch, throw, and finally"
description: "Learn to handle synchronous and asynchronous JavaScript failures, create custom errors, and avoid swallowing rejected promises."
slug: "javascript-error-handling-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "JavaScript"
  - "Error Handling"
  - "Promises"
  - "Debugging"
relatedTools: []
relatedArticles:
  - "javascript-async-await-explained"
  - "javascript-event-loop-guide"
draft: false
---

# JavaScript Error Handling Explained: try, catch, throw, and finally

Errors are part of normal software development. A file may not load, an API may be unavailable, or a function may receive an invalid value.

JavaScript provides exceptions and Promise rejection handling so applications can respond to failures without silently corrupting application state.

## Use try and catch

A try block contains code that may throw an exception. A catch block handles the error:

~~~javascript
try {
  const value = JSON.parse('{"active": true}');
  console.log(value.active);
} catch (error) {
  console.error("Invalid JSON:", error.message);
}
~~~

Catch errors at a layer that can make a meaningful decision. Avoid broad catches that hide programming defects.

## Throw an error

Use throw when a function detects an invalid state or input:

~~~javascript
function divide(a, b) {
  if (b === 0) {
    throw new RangeError("Divisor must not be zero");
  }

  return a / b;
}
~~~

JavaScript can throw any value, but using Error objects provides stack information and a standard error interface.

## Use finally for cleanup

The finally block runs as control leaves the try/catch construct through normal completion, return, or exception propagation:

~~~javascript
try {
  performOperation();
} catch (error) {
  reportError(error);
} finally {
  releaseTemporaryResource();
}
~~~

Cleanup code should be designed carefully so that it does not accidentally replace the original failure with an unrelated exception.

## Create custom error classes

Custom errors can distinguish domain-specific failure types:

~~~javascript
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}
~~~

Callers can check the error type and respond appropriately.

Use custom errors when they provide meaningful information or handling behavior. Avoid creating many types that do not add value.

## Handle Promise rejections

A Promise can be fulfilled or rejected. Use catch() to handle a rejected Promise:

~~~javascript
fetch("/api/profile")
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    return response.json();
  })
  .then(data => {
    renderProfile(data);
  })
  .catch(error => {
    showErrorMessage("Unable to load profile");
    console.error(error);
  });
~~~

The fetch API rejects on some network failures, but an HTTP error status such as 404 does not automatically reject the Promise. Check response.ok or status explicitly.

## Use try/catch with async and await

~~~javascript
async function loadProfile() {
  try {
    const response = await fetch("/api/profile");

    if (!response.ok) {
      throw new Error(`Request failed: ${response.status}`);
    }

    return await response.json();
  } catch (error) {
    console.error("Profile loading failed", error);
    throw error;
  }
}
~~~

A try/catch around an awaited Promise can handle its rejection. If the function catches an error and does not rethrow, its returned Promise may fulfill with the catch block's result.

Decide whether the function should recover, return a fallback, or propagate the failure.

## Promise.all and error behavior

Promise.all() rejects when one of its input promises rejects. If independent operations should all be observed even when some fail, Promise.allSettled() can be more suitable.

Choose based on the requirement:
- Use Promise.all() when all results are required for success.
- Use Promise.allSettled() when each outcome should be inspected independently.

Neither method automatically cancels the other operations when one fails.

## Global error handlers

Browsers and Node.js provide global error-reporting mechanisms. They can help capture failures that were not handled locally, but they are not a replacement for correct Promise and exception handling.

A global handler should record safe diagnostics and avoid leaking sensitive information.

## Common mistakes

### Assuming fetch rejects for every HTTP error

Check response.ok and handle non-success status codes explicitly.

### Forgetting to await an asynchronous operation

A surrounding try/catch cannot catch a Promise rejection that is not awaited or returned in the relevant control flow.

### Swallowing errors

Do not catch an error and continue as though an operation succeeded unless the fallback is valid and documented.

### Exposing internal error details

Show users an appropriate message. Keep sensitive traces and internal details in protected logs.

### Using exceptions for ordinary branching

Use normal conditions for expected alternatives. Exceptions are most useful for exceptional or invalid states.

## Frequently asked questions

### Does try/catch handle Promise rejections?

It can, when the Promise is awaited inside the try block. It does not automatically catch every unawaited asynchronous rejection.

### Does fetch throw when the server returns 500?

No. fetch normally resolves with a Response object for HTTP error statuses. Check response.ok or status.

### Should errors be logged and rethrown?

That depends on the layer. Avoid duplicate logging at every layer. Log where sufficient context is available and preserve propagation where a caller needs to handle the failure.

## Further reading

- [MDN: Control flow and error handling](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control_flow_and_error_handling)
- [MDN: Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [Node.js: Errors](https://nodejs.org/api/errors.html)

---
