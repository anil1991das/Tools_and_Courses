---
title: "JavaScript Prototypes and Inheritance Explained"
description: "Learn JavaScript prototype chains, property lookup, constructor functions, Object.create, and how class syntax uses prototypes."
slug: "javascript-prototypes-and-inheritance-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "JavaScript"
  - "Prototypes"
  - "Inheritance"
  - "Object Model"
relatedTools: []
relatedArticles:
  - "javascript-closures-explained"
draft: false
---

# JavaScript Prototypes and Inheritance Explained

JavaScript objects can inherit properties and methods from other objects through a prototype chain. This is the foundation of JavaScript's inheritance model.

The class syntax introduced in modern JavaScript provides a familiar way to define constructors and methods, but it still uses the prototype system.

## What is a prototype?

An object can have an internal prototype reference to another object. When JavaScript looks up a property and does not find it directly, it follows the prototype chain.

~~~javascript
const animal = {
  speak() {
    return "A sound";
  }
};

const dog = Object.create(animal);
dog.name = "Milo";

console.log(dog.name);
console.log(dog.speak());
~~~

The dog object does not own speak, but property lookup finds it on animal.

## Own properties versus inherited properties

An own property belongs directly to an object. An inherited property is found through its prototype chain.

~~~javascript
console.log(Object.hasOwn(dog, "name"));  // true
console.log(Object.hasOwn(dog, "speak")); // false
~~~

Object.hasOwn() is useful when the distinction matters. The in operator checks both own and inherited properties.

## Prototype chains

Prototype lookup continues until a property is found or the chain reaches null.

The chain allows objects to share behavior without copying every method into each object.

However, modifying prototypes globally can affect unrelated code and libraries. Prefer well-defined class or object composition patterns.

## Constructor functions

Before class syntax became common, constructor functions and prototype assignments were widely used:

~~~javascript
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  return "Hello, " + this.name;
};

const person = new Person("Asha");
console.log(person.greet());
~~~

The new operator creates an object, links it to Person.prototype, calls the function with the new object as this, and returns the object unless the constructor explicitly returns another object.

## Class syntax uses prototypes

Modern syntax:

~~~javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    return `Hello, ${this.name}`;
  }
}
~~~

Methods defined in a class body are placed on the prototype rather than copied as own methods to every instance.

The class syntax also has specific semantics, including strict-mode behavior and restrictions on calling a class without new.

## Inheritance with extends

~~~javascript
class Employee extends Person {
  constructor(name, role) {
    super(name);
    this.role = role;
  }

  describe() {
    return `${this.name} is a ${this.role}`;
  }
}
~~~

super() calls the parent constructor in a derived class. A derived constructor must call super() before using this.

Use inheritance when the subtype genuinely specializes the parent behavior. Composition may be clearer when objects merely need to collaborate.

## Static methods

A static method belongs to the class constructor rather than an instance:

~~~javascript
class MathTools {
  static square(value) {
    return value * value;
  }
}

console.log(MathTools.square(4));
~~~

Static methods are useful for related operations that do not need instance state.

## Property descriptors

JavaScript properties can have descriptors that control whether they are writable, enumerable, configurable, or accessors.

Object.defineProperty() can define such behavior, but ordinary object literals and class syntax are enough for many applications.

Understanding property descriptors is useful when working with libraries, proxies, and framework internals.

## Common mistakes

### Assuming class creates a different inheritance model

JavaScript class syntax is built on prototypes. It adds a clearer syntax and semantics over the prototype system.

### Modifying built-in prototypes

Adding or changing methods on Object.prototype or Array.prototype can create collisions and unexpected behavior. Avoid it in application code.

### Confusing own and inherited properties

Use Object.hasOwn() when the property must belong directly to the object.

### Forgetting super() in a derived constructor

A derived class constructor must call super() before accessing this.

## Frequently asked questions

### Are JavaScript classes syntactic sugar?

They are often described that way because they use prototypes, but class syntax also imposes language-specific semantics and constraints.

### What is Object.create() used for?

It creates an object with a specified prototype, allowing explicit prototype-based delegation.

### Does inheritance copy methods into every object?

Prototype methods are typically shared through the prototype object rather than copied into each instance.

## Further reading

- [MDN: Inheritance and the prototype chain](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
- [MDN: Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
- [ECMAScript Specification](https://tc39.es/ecma262/)

---
