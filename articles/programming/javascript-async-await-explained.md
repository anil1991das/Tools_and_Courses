---
title: "JavaScript Async and Await Explained with Practical Examples"
description: "Understand promises, async functions, await, error handling, and parallel asynchronous work in JavaScript."
slug: "javascript-async-await-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Programming"
tags:
  - "JavaScript"
  - "Async Await"
  - "Promises"
  - "Web Development"
relatedTools: []
relatedArticles: []
draft: false
---

# JavaScript Async and Await Explained with Practical Examples

Modern JavaScript applications often perform operations that take time, such as fetching data from an API, reading files, or waiting for a database response. These operations should not unnecessarily block the rest of the application.

JavaScript uses promises and asynchronous functions to represent work that may complete later. The async and await keywords make promise-based code easier to read and maintain.

## What is asynchronous programming?

Synchronous code executes one statement after another. A statement normally completes before the next statement runs.

Asynchronous operations allow a program to start work and continue handling other tasks while waiting for the result.

Examples include:

- Network requests.
- Timers.
- File-system operations.
- Database queries.
- Reading user input.

JavaScript uses an event loop to coordinate execution and asynchronous callbacks. The exact scheduling behavior depends on tasks, microtasks, and the runtime environment.

## What is a Promise?

A Promise represents the eventual result of an asynchronous operation. It can be in one of three states:

- Pending.
- Fulfilled.
- Rejected.

A promise can be handled with then() and catch():

~~~javascript
fetch("https://api.example.com/products")
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
~~~

This example demonstrates promise chaining. In a real application, also check response.ok because fetch does not reject for ordinary HTTP error status codes such as 404 or 500.

## What does async do?

The async keyword declares an asynchronous function. Calling an async function always returns a Promise.

~~~javascript
async function getMessage() {
  return "Hello";
}

getMessage().then(console.log);
~~~

Even though the function returns a string, JavaScript wraps the result in a fulfilled Promise.

## What does await do?

The await keyword pauses execution inside the surrounding async function until a promise settles. It does not block the entire JavaScript runtime.

~~~javascript
async function loadProducts() {
  const response = await fetch("https://api.example.com/products");

  if (!response.ok) {
    throw new Error("Request failed");
  }

  const products = await response.json();
  return products;
}
~~~

The code reads in a top-to-bottom style while still waiting asynchronously for network operations.

## Handle errors with try and catch

Use try/catch when you need to handle rejected promises or errors thrown inside an async function.

~~~javascript
async function loadProducts() {
  try {
    const response = await fetch("/api/products");

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    return await response.json();
  } catch (error) {
    console.error("Unable to load products:", error);
    return [];
  }
}
~~~

Returning an empty array is only suitable if it makes sense for the application. In some cases, the error should be rethrown or shown to the user instead of being converted into an apparently successful empty result.

## Run independent operations in parallel

If two requests do not depend on one another, awaiting them sequentially can increase total waiting time.

Sequential example:

~~~javascript
const users = await fetchUsers();
const orders = await fetchOrders();
~~~

Parallel example:

~~~javascript
const [users, orders] = await Promise.all([
  fetchUsers(),
  fetchOrders()
]);
~~~

Promise.all waits for all promises to fulfill. If one rejects, the combined promise rejects.

Use Promise.allSettled when you need to inspect every result even if some operations fail.

## Common mistakes

### Forgetting await

Calling an async function without await gives you a Promise, not the final value.

### Not checking HTTP status

fetch resolves for HTTP error responses. Check response.ok or inspect the status before treating the body as a successful result.

### Using await inside a loop unnecessarily

A sequential loop may be correct when each operation depends on the previous result. If operations are independent, consider bounded concurrency or Promise.all.

### Ignoring rejected promises

Every asynchronous operation should have an intentional error-handling path. Unhandled rejections can make failures difficult to diagnose.

### Assuming await blocks everything

Await suspends the current async function. Other JavaScript work can continue according to the event loop.

## Frequently asked questions

### Is async/await different from promises?

Async/await is syntax built on promises. It provides a more readable way to write promise-based asynchronous code.

### Can I use await outside an async function?

Modern JavaScript supports top-level await in modules, subject to the module environment. In other contexts, await must be used inside an async function.

### Does Promise.all cancel the remaining requests when one fails?

No. Promise.all rejects when one input rejects, but the other operations may continue. Use AbortController or another cancellation mechanism when cancellation is required.

## Further reading

- [MDN: async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
- [MDN: await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)
- [MDN: Using Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

---
