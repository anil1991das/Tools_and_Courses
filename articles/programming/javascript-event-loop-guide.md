---
title: "JavaScript Event Loop Explained: Call Stack, Tasks, and Microtasks"
description: "Understand how JavaScript schedules asynchronous work through the call stack, task queues, microtasks, and the event loop."
slug: "javascript-event-loop-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Programming"
tags:
  - "JavaScript"
  - "Event Loop"
  - "Asynchronous Programming"
  - "Promises"
relatedTools: []
relatedArticles:
  - "javascript-async-await-explained"
draft: false
---

# JavaScript Event Loop Explained: Call Stack, Tasks, and Microtasks

JavaScript is often described as single-threaded in its main execution model, but browsers and runtimes handle timers, network I/O, and other asynchronous work without blocking the entire application.

The event loop coordinates when JavaScript callbacks run. Understanding the call stack, tasks, and microtasks helps explain why code does not always execute in the order developers first expect.

## The call stack

The call stack tracks active function calls.

When a function is called, a stack frame is added. When the function returns, its frame is removed.

~~~javascript
function first() {
  second();
}

function second() {
  console.log("Inside second");
}

first();
~~~

The second function runs while first is waiting for it to complete. This is ordinary synchronous execution.

## What is the event loop?

The event loop coordinates execution of queued work when the JavaScript call stack is available.

In browsers, the event loop also coordinates rendering opportunities and other host-environment behavior. In Node.js, the runtime has its own event-loop phases and APIs.

Do not assume every runtime schedules all asynchronous operations identically.

## Tasks and task queues

Tasks may be scheduled by timers, user interaction, message events, and other host APIs.

For example:

~~~javascript
console.log("Start");

setTimeout(() => {
  console.log("Timer callback");
}, 0);

console.log("End");
~~~

The output is:

~~~text
Start
End
Timer callback
~~~

A zero-millisecond timer does not execute immediately. It schedules a callback that can run when the current JavaScript work has completed and the timer becomes eligible.

## What are microtasks?

Microtasks include promise reactions and callbacks scheduled through queueMicrotask().

After the current JavaScript task finishes, the runtime generally drains the microtask queue before moving to the next task, subject to the host's event-loop rules.

Example:

~~~javascript
console.log("Start");

setTimeout(() => console.log("Task"), 0);

Promise.resolve().then(() => {
  console.log("Microtask");
});

console.log("End");
~~~

A typical browser output is:

~~~text
Start
End
Microtask
Task
~~~

The promise callback is a microtask. The timer callback is a task.

## async and await use promises

An async function returns a Promise. When execution reaches an await expression, the function suspends and resumes through promise-related scheduling.

~~~javascript
async function run() {
  console.log("A");
  await Promise.resolve();
  console.log("B");
}

console.log("Start");
run();
console.log("End");
~~~

A typical output is:

~~~text
Start
A
End
B
~~~

The code before await runs synchronously. The continuation after await runs later.

## Avoid blocking the event loop

A long-running synchronous function prevents other JavaScript callbacks from running on that thread.

For example, an expensive loop can make a page unresponsive:

~~~javascript
for (let index = 0; index < 1e10; index++) {
  // CPU-intensive work
}
~~~

For CPU-heavy tasks, consider breaking work into smaller chunks, using Web Workers in browsers, or using worker threads or processes in server environments.

## Rendering and microtasks

In browsers, rendering is coordinated with the event loop. Long tasks and repeated microtask scheduling can delay rendering and make an interface appear frozen.

A microtask that continually schedules more microtasks can prevent the browser from reaching a rendering opportunity. Use task scheduling or rendering APIs such as requestAnimationFrame for work that must coordinate with visual updates.

## Common misconceptions

### setTimeout(fn, 0) runs immediately

It schedules a future task. The callback runs only after the current work and relevant scheduling conditions allow it.

### Promises run in parallel automatically

Promises represent eventual results. Creating a promise does not automatically move CPU work to another thread.

### JavaScript has no concurrency

The main JavaScript execution context may be single-threaded, but the runtime coordinates many asynchronous operations and can provide workers for parallel execution.

### Every runtime has the same event loop

Browser event loops and Node.js event loops have different host-specific details. Learn the environment where the code runs.

## Frequently asked questions

### What runs first: setTimeout or Promise.then?

For the common example where both are scheduled during the same task, the promise reaction microtask runs before the timer task.

### Does await block the browser?

No. It suspends the current async function. Other events can be processed while the awaited promise is pending.

### How can I avoid blocking the UI?

Keep synchronous work short, use workers for CPU-intensive tasks, and divide long operations into scheduled chunks when practical.

## Further reading

- [MDN: JavaScript execution model](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Execution_model)
- [HTML Standard: Event loops](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops)
- [Node.js: Event loop, timers, and nextTick](https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick)

---
