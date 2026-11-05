---
title: "TypeScript Generics Explained: Reusable Functions, Types, and Constraints"
description: "Learn how TypeScript generics preserve type information in reusable functions, interfaces, classes, and constrained type parameters."
slug: "typescript-generics-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "TypeScript"
  - "Generics"
  - "Type Safety"
  - "JavaScript"
relatedTools: []
relatedArticles:
  - "typescript-types-vs-interfaces-guide"
draft: false
---

# TypeScript Generics Explained: Reusable Functions, Types, and Constraints

Generics allow TypeScript code to work with different data types while preserving useful information about those types. They help create reusable functions, classes, and interfaces without falling back to any.

A generic type parameter represents a type selected by the caller or inferred by the compiler.

## Why use generics?

A function that returns its input might be written with any:

~~~typescript
function identity(value: any): any {
  return value;
}
~~~

This loses the relationship between the input and output types. A caller can assign the result to an unrelated type without a useful compiler error.

Use a generic parameter instead:

~~~typescript
function identity<T>(value: T): T {
  return value;
}
~~~

When called with a string, T is inferred as string. The returned value retains that type.

## Generic functions

A generic function can accept an array and return its first element:

~~~typescript
function firstItem<T>(items: T[]): T | undefined {
  return items[0];
}
~~~

The function works with arrays of numbers, strings, or objects while preserving the element type.

The return type includes undefined because an empty array has no first element.

## Type inference

TypeScript can often infer generic type parameters from arguments:

~~~typescript
const numberValue = identity(10);
const textValue = identity("hello");
~~~

Explicit type arguments can still be supplied when inference is insufficient or the desired type needs to be specified:

~~~typescript
const result = identity<number>(10);
~~~

Prefer inference when it communicates the type clearly. Explicit arguments can improve readability at API boundaries.

## Generic constraints

A generic function can require a type parameter to satisfy a constraint.

~~~typescript
function getLength<T extends { length: number }>(value: T): number {
  return value.length;
}
~~~

This function accepts values that have a numeric length property, such as strings and arrays.

The constraint allows the function body to access length safely according to the declared type.

## keyof and generic property access

keyof creates a union of an object's property keys. Combined with generics, it can describe a safe property getter:

~~~typescript
function getProperty<T, K extends keyof T>(
  object: T,
  key: K
): T[K] {
  return object[key];
}
~~~

The return type is related to the selected key. If the key refers to a number property, the returned type reflects that property.

## Generic interfaces

An interface can have a type parameter:

~~~typescript
interface ApiResponse<T> {
  data: T;
  success: boolean;
  message?: string;
}
~~~

The same response structure can represent different payloads:

~~~typescript
type User = {
  id: number;
  name: string;
};

const response: ApiResponse<User> = {
  data: { id: 1, name: "Asha" },
  success: true
};
~~~

Generic interfaces are useful for API responses, repositories, events, and reusable component contracts.

## Generic classes

A class can preserve a type for its stored value:

~~~typescript
class Box<T> {
  constructor(public value: T) {}

  getValue(): T {
    return this.value;
  }
}
~~~

A Box<number> stores a number, while Box<string> stores a string according to the compile-time type.

## Default type parameters

A generic type parameter can have a default:

~~~typescript
interface Result<T = string> {
  value: T;
}
~~~

When the caller does not specify a type argument, the default is used if inference does not provide another value.

Defaults can make a public API convenient, but avoid defaults that conceal important type differences.

## Generics versus any

any disables many type checks. Generics preserve the relationship between inputs and outputs.

Use unknown when a value's type is genuinely not known and must be narrowed. Use generics when the type is selected by the caller and should remain consistent through an operation.

## Common mistakes

### Using generics where no type relationship exists

A generic parameter is useful when it represents a meaningful relationship. It should not be added only to make a signature look advanced.

### Overusing constraints

Constraints should express the minimum capabilities the function needs, not unnecessarily restrict callers.

### Returning any from a generic function

That discards the type information the generic was intended to preserve.

### Expecting generics to validate runtime data

TypeScript types are erased at runtime. Validate API payloads and user input explicitly.

## Frequently asked questions

### What does T mean in TypeScript?

T is a conventional name for a type parameter. It can be named something more descriptive, such as TValue or TResponse.

### Can a function have multiple generic parameters?

Yes. For example, a function may use T for an object and K for one of its property keys.

### Do generics exist at runtime?

TypeScript generic type information is erased during compilation. Runtime validation requires ordinary JavaScript logic or a validation library.

## Further reading

- [TypeScript Handbook: Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [TypeScript Handbook: Keyof Type Operator](https://www.typescriptlang.org/docs/handbook/2/keyof-types.html)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)

---
