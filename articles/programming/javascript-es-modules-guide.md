---
title: "JavaScript ES Modules Explained: import, export, and Module Scope"
description: "Understand JavaScript modules, named and default exports, static imports, dynamic import(), and how modules organize application code."
slug: "javascript-es-modules-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "JavaScript"
  - "ES Modules"
  - "import"
  - "export"
  - "Web Development"
relatedTools: []
relatedArticles:
  - "javascript-closures-explained"
  - "javascript-prototypes-and-inheritance-guide"
draft: false
---

# JavaScript ES Modules Explained: import, export, and Module Scope

As an application grows, keeping all JavaScript code in one file makes it harder to understand and maintain. ECMAScript modules (ES modules or ESM) provide a standard way to organize code into separate files with explicit imports and exports.

Modules help define public interfaces, separate responsibilities, and make dependencies visible.

## What is a JavaScript module?

An ES module is a JavaScript file interpreted using module semantics. Modules have their own top-level scope and can import bindings from other modules or export values for other modules to use.

In browsers, a module script is loaded using:

~~~html
<script type="module" src="/src/main.js"></script>
~~~

Module scripts are deferred by default and use strict mode.

## Named exports

A module can export multiple named values:

~~~javascript
// math.js
export function add(a, b) {
  return a + b;
}

export const PI = 3.14159;
~~~

Import selected values:

~~~javascript
import { add, PI } from "./math.js";

console.log(add(2, 3));
~~~

Named exports make the public interface explicit.

## Default exports

A module can have one default export:

~~~javascript
// formatter.js
export default function formatName(name) {
  return name.trim().toUpperCase();
}
~~~

Import it with any local binding name:

~~~javascript
import formatName from "./formatter.js";
~~~

A module can use named and default exports together, but a consistent convention often makes imports easier to scan.

## Rename imports and exports

An exported binding can be renamed during import:

~~~javascript
import { add as sum } from "./math.js";
~~~

A module can also re-export values from another module:

~~~javascript
export { add, PI } from "./math.js";
~~~

This can create a central entry point for a package or feature.

## Import a module namespace

Use namespace import syntax to collect named exports:

~~~javascript
import * as math from "./math.js";

console.log(math.add(2, 3));
console.log(math.PI);
~~~

The namespace object exposes the module's exported bindings.

## Dynamic import()

Static import declarations are resolved as part of module loading. Dynamic import() returns a Promise and can load a module when needed.

~~~javascript
async function openEditor() {
  const editor = await import("./editor.js");
  editor.createEditor();
}
~~~

Dynamic imports can support code splitting and optional features. They are useful when a module is not required during initial application startup.

## Module scope

Top-level declarations in a module do not automatically become properties of the global object.

This helps prevent unrelated scripts from overwriting one another's variables.

Modules can still intentionally interact with global APIs or objects, but dependencies should generally be explicit through imports or well-defined interfaces.

## Circular dependencies

A circular dependency occurs when module A imports module B while module B also imports module A, directly or indirectly.

ES modules support live bindings, but circular initialization can produce surprising behavior if one module accesses another module's binding before initialization is complete.

Reduce unnecessary circular dependencies by extracting shared contracts or restructuring module responsibilities.

## Browser modules and Node.js

Browsers support ES modules natively. Node.js also supports ESM, but package configuration and file extensions affect how JavaScript files are interpreted.

Node.js projects commonly use package.json settings such as "type": "module" or .mjs files. Check the runtime and build tool documentation when mixing CommonJS and ESM.

## Common mistakes

### Forgetting the relative path

Browser module specifiers generally need valid relative, absolute, or supported bare-specifier resolution. A bundler may apply different resolution rules.

### Mixing CommonJS and ESM assumptions

require/module.exports and import/export follow different module systems. Use an intentional interoperability approach.

### Creating large circular dependency chains

They complicate initialization and make code structure harder to follow.

### Importing heavy features eagerly

Dynamic imports can defer optional functionality and improve initial loading when configured and measured appropriately.

## Frequently asked questions

### Can a module have multiple named exports?

Yes. A module can export many named bindings and at most one default export.

### Are ES modules strict mode?

Yes. Module code executes in strict mode automatically.

### Does import copy a value?

Imports are live bindings to exported values. Their behavior is not simply equivalent to copying a value into another variable.

## Further reading

- [MDN: JavaScript Modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)
- [Node.js: ECMAScript Modules](https://nodejs.org/api/esm.html)
- [ECMAScript Specification](https://tc39.es/ecma262/)

---
