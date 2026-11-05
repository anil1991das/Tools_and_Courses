---
title: "Python Context Managers Explained: with, __enter__, and __exit__"
description: "Learn how Python context managers manage resources, use the with statement, implement cleanup protocols, and write contextlib-based helpers."
slug: "python-context-managers-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "Context Managers"
  - "Resource Management"
  - "with Statement"
relatedTools: []
relatedArticles:
  - "python-exception-handling-guide"
  - "python-generators-and-yield-guide"
draft: false
---

# Python Context Managers Explained: with, __enter__, and __exit__

A program often acquires resources that must be released after use. Files need to be closed, locks need to be released, and temporary changes may need to be restored.

Python context managers make resource lifetime explicit through the with statement. They help ensure cleanup happens when a block exits normally or through an exception.

## What is a context manager?

A context manager defines setup and cleanup behavior around a block of code.

~~~python
with open("notes.txt", encoding="utf-8") as file:
    content = file.read()
~~~

The file is closed when the with block exits, including when an exception occurs during the block.

This is safer and clearer than manually calling close() along every possible execution path.

## The context management protocol

A class-based context manager commonly implements:
- __enter__(): performs setup and returns the value bound after as.
- __exit__(): performs cleanup and receives exception information if the block exits with an exception.

Example:

~~~python
class ManagedResource:
    def __enter__(self):
        print("Acquire resource")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print("Release resource")
        return False


with ManagedResource():
    print("Use resource")
~~~

Returning False from __exit__ indicates that an exception should not be suppressed. Returning a truthy value can suppress the exception, so do this only when that behavior is deliberate.

## Use contextlib.contextmanager

The contextlib module provides a convenient way to create a context manager with a generator:

~~~python
from contextlib import contextmanager


@contextmanager
def managed_resource():
    print("Acquire")
    try:
        yield "resource"
    finally:
        print("Release")


with managed_resource() as resource:
    print("Using", resource)
~~~

The code before yield performs setup. The code in finally performs cleanup when the with block exits.

The generator-based context manager should yield exactly once for the normal single-block context manager pattern.

## Manage multiple resources

A with statement can manage several resources:

~~~python
with open("input.txt", encoding="utf-8") as source,      open("output.txt", "w", encoding="utf-8") as target:
    target.write(source.read())
~~~

Python also supports contextlib.ExitStack for a dynamic number of resources or when resources are acquired conditionally.

## Context managers for locks

Locks are commonly used with a context manager so that release occurs even if protected code raises an exception.

~~~python
from threading import Lock

lock = Lock()
shared_count = 0

with lock:
    shared_count += 1
~~~

The lock protects the critical section. The context manager releases it when control leaves the block.

Keep critical sections short and avoid calling unknown or blocking code while holding a lock.

## Temporary state and test helpers

Context managers can also temporarily change state and restore it later. Examples include:
- Temporarily changing a working directory.
- Redirecting standard output.
- Applying a test configuration.
- Setting an environment variable for a limited scope.
- Creating a temporary directory.

Use standard library helpers where possible rather than implementing lifecycle logic unnecessarily.

## Exception behavior

If an exception occurs inside the with block, __exit__ receives the exception information. A context manager can:
- Perform cleanup and allow the exception to propagate.
- Translate the exception by raising a different one.
- Suppress the exception when suppression is explicitly part of its contract.

Cleanup should be reliable and should not accidentally hide the original failure.

## Common mistakes

### Forgetting cleanup on an early return

The with statement handles block exit even when the function returns early.

### Suppressing exceptions unintentionally

A truthy result from __exit__ suppresses the exception. Return False or None when the exception should propagate.

### Acquiring resources before entering the context

If setup can fail after acquiring some resources, arrange acquisition and cleanup so partial setup is also safe. ExitStack can help with dynamic acquisition.

### Writing a context manager for a trivial operation

Use the simplest tool that makes ownership clear. Not every function needs a custom context manager.

## Frequently asked questions

### Is with only for files?

No. It can manage any resource or temporary state that follows the context management protocol.

### Does finally always execute in a context manager?

The cleanup logic in the context manager is invoked during normal context exit, including ordinary exception propagation. Forced process termination or power loss can still prevent cleanup.

### Can a context manager suppress errors?

Yes, but that should be a deliberate documented behavior. Accidental suppression makes failures difficult to detect.

## Further reading

- [Python: The with statement](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement)
- [Python: contextlib](https://docs.python.org/3/library/contextlib.html)
- [Python: object.__enter__ and object.__exit__](https://docs.python.org/3/reference/datamodel.html#with-statement-context-managers)

---
