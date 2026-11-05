---
title: "Python Generators and yield Explained with Practical Examples"
description: "Learn how Python generators produce values lazily, use yield and yield from, and reduce memory use when processing large sequences."
slug: "python-generators-and-yield-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "Generators"
  - "Iterators"
  - "Memory Efficiency"
relatedTools: []
relatedArticles:
  - "python-iterators-and-iterables-guide"
  - "python-list-comprehensions-guide"
draft: false
---

# Python Generators and yield Explained with Practical Examples

Python generators let a function produce a sequence of values one at a time instead of building the entire result in memory. They are useful for processing files, streams, database records, and large ranges of data.

A generator is an iterator. It remembers its execution state between values, which makes it possible to write readable, incremental processing code.

## What is a generator?

A generator function looks like a normal function, but it uses yield to produce a value.

~~~python
def count_up_to(limit):
    number = 1

    while number <= limit:
        yield number
        number += 1


for value in count_up_to(3):
    print(value)
~~~

Output:

~~~text
1
2
3
~~~

When the function reaches yield, it produces the value and suspends execution. The next iteration resumes from that point.

Calling a generator function does not immediately execute its body. It returns a generator object that produces values when requested.

## yield versus return

return exits a function and provides its final result. yield produces a value while preserving the generator's state so execution can continue later.

A function can contain multiple yield statements. Once the generator has no more values, it raises StopIteration internally to signal completion to the iterator protocol.

## Use next() to request values

A generator can be consumed manually:

~~~python
def message_stream():
    yield "connected"
    yield "authenticated"
    yield "ready"


stream = message_stream()

print(next(stream))
print(next(stream))
print(next(stream))
~~~

Each call to next() requests another value. Calling next() after exhaustion raises StopIteration unless a default value is supplied.

Most application code uses a for loop because it handles iterator exhaustion automatically.

## Why generators can save memory

A list comprehension creates the whole list immediately:

~~~python
squares = [number * number for number in range(1_000_000)]
~~~

A generator expression produces values as they are requested:

~~~python
squares = (number * number for number in range(1_000_000))
~~~

The generator does not hold all one million calculated values at once. It calculates each value when iteration advances.

This can reduce memory use, but it does not make every operation faster. If the result must be reused many times or randomly indexed, a materialized list may be more appropriate.

## Generator expressions

Generator expressions use parentheses and a comprehension-like syntax:

~~~python
even_squares = (
    number * number
    for number in range(20)
    if number % 2 == 0
)
~~~

They are useful when passing values directly into functions such as sum(), any(), all(), or max().

~~~python
total = sum(number * number for number in range(100))
~~~

## Use yield from

yield from delegates iteration to another iterable or generator.

~~~python
def combined():
    yield "start"
    yield from [10, 20, 30]
    yield "finish"


print(list(combined()))
~~~

This is useful for composing generators from smaller iterable sources. It also supports delegation of generator operations in more advanced generator designs.

## Process a file incrementally

File objects are iterable, so they can be processed line by line:

~~~python
def error_lines(path):
    with open(path, encoding="utf-8") as file:
        for line in file:
            if "ERROR" in line:
                yield line.rstrip()
~~~

The caller can process each matching line without reading the entire file into a list.

The generator controls the file context while it is being iterated. Close or exhaust the generator promptly when early termination is possible; alternatively, keep file ownership explicit in the calling code.

## Generators are usually one-pass

A generator is generally consumed as it is iterated. Once exhausted, it does not automatically restart.

~~~python
values = (x for x in range(3))

print(list(values))
print(list(values))  # Empty: the generator is already exhausted
~~~

Create a new generator object when another pass is needed.

## Common mistakes

### Expecting a generator to be a list

Generators do not support normal list indexing or repeated iteration. Convert to a list only when the full result is needed.

### Forgetting that execution is lazy

Code inside a generator function does not run until a value is requested. This can affect when exceptions occur and when resources are opened.

### Keeping a generator alive too long

A generator may hold files, sockets, or other resources while suspended. Close or exhaust it appropriately when resource lifetime matters.

### Assuming lazy means automatically faster

Generators reduce eager materialization, but they have per-iteration overhead. Benchmark the actual workload.

## Frequently asked questions

### What is the difference between yield and yield from?

yield produces a value. yield from delegates iteration to another iterable or generator and yields its values.

### Can a generator be converted to a list?

Yes. list(generator) consumes the generator and stores all yielded values in a list.

### Are generators useful for large files?

Yes. They allow records or lines to be processed incrementally, which can reduce peak memory use.

## Further reading

- [Python: Generators](https://docs.python.org/3/howto/functional.html#generators)
- [Python Language Reference: yield expressions](https://docs.python.org/3/reference/expressions.html#yield-expressions)
- [Python: Iterator Types](https://docs.python.org/3/library/stdtypes.html#iterator-types)

---
