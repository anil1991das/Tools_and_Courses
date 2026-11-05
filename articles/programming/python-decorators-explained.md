---
title: "Python Decorators Explained: Functions, Wrappers, and @ Syntax"
description: "Understand how Python decorators wrap functions, preserve metadata, accept arguments, and support reusable logging, timing, and authorization behavior."
slug: "python-decorators-explained"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "Decorators"
  - "Functions"
  - "Code Reuse"
relatedTools: []
relatedArticles:
  - "python-iterators-and-iterables-guide"
  - "python-type-hints-guide"
draft: false
---

# Python Decorators Explained: Functions, Wrappers, and @ Syntax

Python decorators provide a way to modify or extend a function or class without changing its original definition. They are commonly used for logging, timing, caching, validation, authorization, and framework features such as routes.

A decorator is a callable that receives another callable and returns a callable or another replacement object.

## Functions are objects in Python

Functions can be assigned to variables, passed as arguments, and returned from other functions.

~~~python
def greet():
    return "Hello"


message_function = greet
print(message_function())
~~~

This property makes it possible to create a function that receives another function and wraps its behavior.

## Write a simple decorator

~~~python
def announce_call(function):
    def wrapper():
        print("Function is starting")
        result = function()
        print("Function has finished")
        return result

    return wrapper


@announce_call
def greet():
    print("Hello")


greet()
~~~

The @announce_call syntax is equivalent to:

~~~python
def greet():
    print("Hello")

greet = announce_call(greet)
~~~

The decorated name refers to the wrapper returned by the decorator.

## Accept arbitrary arguments

A general-purpose decorator should usually support the wrapped function's positional and keyword arguments.

~~~python
def announce_call(function):
    def wrapper(*args, **kwargs):
        print("Calling:", function.__name__)
        return function(*args, **kwargs)

    return wrapper
~~~

The wrapper returns the function's result so that decoration does not accidentally discard it.

## Preserve function metadata

A basic wrapper has its own name and documentation metadata. functools.wraps copies important metadata from the wrapped function.

~~~python
from functools import wraps


def announce_call(function):
    @wraps(function)
    def wrapper(*args, **kwargs):
        print("Calling:", function.__name__)
        return function(*args, **kwargs)

    return wrapper
~~~

Using wraps improves introspection, documentation generation, debugging, and framework compatibility.

## Decorators with arguments

A decorator that accepts configuration needs an outer function that returns the actual decorator.

~~~python
from functools import wraps


def repeat(times):
    def decorator(function):
        @wraps(function)
        def wrapper(*args, **kwargs):
            result = None

            for _ in range(times):
                result = function(*args, **kwargs)

            return result

        return wrapper

    return decorator


@repeat(3)
def say_hello():
    print("Hello")
~~~

Here repeat(3) creates a decorator configured to invoke the function three times.

Consider whether repeating a function is safe. A function that charges a payment or writes a record may create unwanted side effects when called repeatedly.

## Decorators and return values

A decorator should preserve the wrapped function's return value unless changing it is the intended contract.

It should also decide how exceptions are handled. Swallowing exceptions can make failures invisible, while logging and re-raising may preserve the original behavior.

## Decorators on methods and classes

A function decorator can also be applied to methods. The wrapper receives self as part of the arguments.

Class decorators receive a class object and return a class or replacement callable. Built-in examples include dataclasses.dataclass.

Decorators are applied when the function or class definition is executed, not each time the decorated function is called.

## Async functions

An ordinary wrapper around an async function must preserve asynchronous behavior. In many cases, the wrapper should itself be async and await the wrapped function.

~~~python
from functools import wraps


def log_async(function):
    @wraps(function)
    async def wrapper(*args, **kwargs):
        print("Starting async operation")
        result = await function(*args, **kwargs)
        print("Finished async operation")
        return result

    return wrapper
~~~

If a decorator needs to support both synchronous and asynchronous functions, it should deliberately handle both forms rather than assuming one wrapper works for every callable.

## Common mistakes

### Forgetting to return the result

A wrapper that calls a function but does not return its result changes the decorated function's behavior.

### Omitting functools.wraps

Without wraps, function metadata such as the name and docstring may be replaced by wrapper metadata.

### Catching every exception

Broad exception handling can hide programming errors. Catch only what the decorator can meaningfully handle.

### Making decorators too complicated

A decorator should isolate reusable behavior. If it obscures control flow or makes debugging difficult, a normal helper or explicit call may be clearer.

## Frequently asked questions

### Are decorators executed at import time?

The decorator expression is evaluated when the function or class definition executes, which commonly occurs during module import.

### Can decorators be stacked?

Yes. Multiple decorators can be applied to one function. They are applied from the closest decorator to the function outward.

### Are decorators only for functions?

No. Decorators can be applied to classes and other supported definition forms.

## Further reading

- [Python: Primer on Decorators](https://docs.python.org/3/glossary.html#term-decorator)
- [Python: functools](https://docs.python.org/3/library/functools.html)
- [PEP 318: Decorators for Functions and Methods](https://peps.python.org/pep-0318/)

---
