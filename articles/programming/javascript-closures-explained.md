---
title: "JavaScript Closures Explained: Lexical Scope and Persistent State"
description: "Understand JavaScript closures, lexical environments, function factories, private state, and common loop-related closure behavior."
slug: "javascript-closures-explained"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "JavaScript"
  - "Closures"
  - "Scope"
  - "Functions"
relatedTools: []
relatedArticles:
  - "javascript-event-loop-guide"
  - "javascript-async-await-explained"
draft: false
---

# JavaScript Closures Explained: Lexical Scope and Persistent State

A closure is a function together with access to the lexical environment in which it was created. It allows a function to continue using variables from an outer scope even after that outer function has returned.

Closures are used in callbacks, event handlers, function factories, module patterns, and stateful utilities.

## Understand lexical scope

JavaScript uses lexical scoping. A function can access variables from the scopes surrounding its definition.

~~~javascript
const language = "JavaScript";

function showLanguage() {
  console.log(language);
}

showLanguage();
~~~

The function can access language because it is defined in the scope where language exists.

## Create a closure

~~~javascript
function createCounter() {
  let count = 0;

  return function increment() {
    count += 1;
    return count;
  };
}

const counter = createCounter();

console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
~~~

createCounter has returned, but the inner function still has access to count. The variable remains available through the closure.

## Keep state private

Closures can provide encapsulated state without exposing a variable directly:

~~~javascript
function createAccount(initialBalance) {
  let balance = initialBalance;

  return {
    deposit(amount) {
      if (amount <= 0) {
        throw new Error("Amount must be positive");
      }
      balance += amount;
    },
    getBalance() {
      return balance;
    }
  };
}
~~~

Code using the returned object can update and inspect the balance through the provided methods, but it cannot directly access the local balance binding.

This is a language-level scope boundary, not a security boundary against code that already controls the surrounding runtime.

## Function factories

A closure can create specialized functions:

~~~javascript
function multiplyBy(factor) {
  return function (value) {
    return value * factor;
  };
}

const double = multiplyBy(2);
const triple = multiplyBy(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
~~~

Each returned function closes over its own factor value.

## Closures in event handlers

A browser event handler often needs information about the element or operation it represents.

~~~javascript
function attachMessage(button, message) {
  button.addEventListener("click", () => {
    console.log(message);
  });
}
~~~

The callback closes over message. When the user clicks later, the callback can still access that value.

Be mindful of memory retention. A long-lived callback can keep objects reachable through its closure, preventing them from being garbage-collected.

## The var loop problem

A classic example uses var in a loop:

~~~javascript
for (var index = 0; index < 3; index++) {
  setTimeout(() => console.log(index), 0);
}
~~~

The callbacks share the same function-scoped index binding. When they execute, the loop has completed, so they typically log 3 three times.

Using let creates a new block-scoped binding for each loop iteration:

~~~javascript
for (let index = 0; index < 3; index++) {
  setTimeout(() => console.log(index), 0);
}
~~~

The output is 0, 1, 2.

## Closures and memory

A closure keeps its referenced lexical environment reachable while the closure itself remains reachable. This can be useful for state but may retain large objects longer than expected.

Remove event listeners or release references when components are no longer needed. Avoid unintentionally keeping DOM nodes or large data structures alive in long-lived callbacks.

## Common mistakes

### Thinking a closure copies every outer variable

A closure retains access to bindings in its lexical environment. It does not simply take a snapshot of every variable value at function creation.

### Sharing state unintentionally

Two functions returned from the same outer call may share the same closed-over variables. Separate calls to the factory create separate environments.

### Retaining objects accidentally

A callback can keep referenced objects alive. Review listener lifecycle and references in long-running applications.

### Confusing closures with asynchronous execution

A closure is about lexical access. It can be used in asynchronous callbacks, but it does not itself make code asynchronous.

## Frequently asked questions

### Does every JavaScript function create a closure?

Functions have access to their lexical environment. The term closure is especially useful when a function retains access to bindings outside its immediate scope.

### Can a closure modify an outer variable?

Yes, when the binding is writable and the function is allowed to access it.

### Are closures used in React?

Yes. Event handlers and hooks rely on JavaScript closures. Developers need to understand when a callback captures values from a particular render.

## Further reading

- [MDN: Closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)
- [ECMAScript Language Specification](https://tc39.es/ecma262/)
- [MDN: Lexical scoping](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#variable_scope)

---
