---
title: "Python Exception Handling: try, except, else, and finally"
description: "Learn how Python exceptions work, how to catch specific errors, define custom exceptions, and clean up resources safely."
slug: "python-exception-handling-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "Exceptions"
  - "Error Handling"
  - "Programming"
relatedTools: []
relatedArticles:
  - "python-virtual-environments-guide"
draft: false
---

# Python Exception Handling: try, except, else, and finally

Programs encounter unexpected conditions. A file may be missing, a network request may fail, or input may contain a value that cannot be converted into a number.

Python uses exceptions to signal errors and other exceptional conditions. Proper exception handling helps applications respond predictably without hiding useful diagnostic information.

## What is an exception?

An exception is an object raised when an error or exceptional condition occurs.

For example:

~~~python
number = int("hello")
~~~

This raises ValueError because the string cannot be converted into an integer.

If an exception is not handled, it propagates up the call stack and may terminate the current operation or program.

## Use try and except

A try block contains code that may raise an exception. An except block handles a matching exception type.

~~~python
try:
    value = int("42")
except ValueError:
    print("Please provide a valid integer")
~~~

Catch the specific exceptions that the application can handle. A broad except clause may hide programming errors and make debugging difficult.

## Handle more than one exception type

Multiple except blocks can handle different failure conditions:

~~~python
try:
    value = int(input("Enter a number: "))
    result = 100 / value
except ValueError:
    print("Input must be an integer")
except ZeroDivisionError:
    print("The value must not be zero")
~~~

Each block handles a different type of error. The order matters when exception classes have inheritance relationships: more specific handlers should appear before broader ones.

## Use else for successful execution

The else block runs when the try block completes without raising an exception handled by the associated except clauses.

~~~python
try:
    value = int("25")
except ValueError:
    print("Invalid input")
else:
    print("Converted value:", value)
~~~

Keeping success-path code in else can make it clearer which statements are protected by the exception handler.

## Use finally for cleanup

The finally block runs whether or not the try block raises an exception, subject to process termination and other exceptional runtime conditions.

~~~python
file = None

try:
    file = open("notes.txt", encoding="utf-8")
    content = file.read()
except OSError as error:
    print("File operation failed:", error)
finally:
    if file is not None:
        file.close()
~~~

For file handling, a context manager is usually more concise and reliable:

~~~python
with open("notes.txt", encoding="utf-8") as file:
    content = file.read()
~~~

The file is closed when the with block exits.

## Raise an exception

Use raise when a function detects invalid input or an unsupported state:

~~~python
def set_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")

    return age
~~~

Raise an appropriate exception type and include a useful message. Do not use exceptions as a substitute for ordinary control flow when a simple condition is clearer.

## Define a custom exception

An application can define a custom exception by inheriting from Exception:

~~~python
class PaymentError(Exception):
    pass


def process_payment(amount):
    if amount <= 0:
        raise PaymentError("Amount must be positive")
~~~

Custom exceptions allow callers to handle domain-specific failures without depending on error-message text.

## Re-raise an exception

After logging or adding context, use raise without an argument to re-raise the current exception:

~~~python
try:
    perform_operation()
except OSError:
    log_failure()
    raise
~~~

This preserves the original exception and traceback. Avoid replacing the original failure with an unrelated exception unless that is intentional.

## Exception chaining

When one exception causes another, Python supports exception chaining:

~~~python
try:
    load_configuration()
except OSError as error:
    raise RuntimeError("Unable to start application") from error
~~~

The original exception remains available as the cause. This is useful when translating low-level errors into application-level errors.

## Common mistakes

### Catching every exception

Catching Exception may be appropriate at a top-level boundary, such as a worker process that must log a failure. It is often too broad in ordinary application logic.

### Using exceptions to hide failure

Do not return a success-like default value when an important operation failed. Make the error visible to the caller or user.

### Ignoring the exception variable

Use the exception details for appropriate logging and diagnosis. Avoid exposing sensitive file paths, tokens, or internal information to an untrusted user.

### Writing cleanup code manually

Prefer context managers such as with open(...) when available. They provide reliable resource cleanup.

### Catching errors too early

Handle an exception at the layer that can make a meaningful decision. Lower-level code can often let the exception propagate to a component that knows what recovery is possible.

## Frequently asked questions

### Does finally always run?

It normally runs during standard exception propagation and function exit, but not if the process is forcibly terminated or the runtime stops unexpectedly.

### Should I catch Exception or BaseException?

Most application code should catch specific exceptions or Exception. BaseException also includes system-exiting conditions such as KeyboardInterrupt and SystemExit, which usually should not be swallowed.

### What is the difference between raise and return?

return completes a function with a value. raise signals an exceptional condition and transfers control to an exception handler or the caller.

## Further reading

- [Python Tutorial: Errors and Exceptions](https://docs.python.org/3/tutorial/errors.html)
- [Python Built-in Exceptions](https://docs.python.org/3/library/exceptions.html)
- [Python: The with statement](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement)

---
