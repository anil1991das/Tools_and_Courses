---
title: "Python Iterables and Iterators Explained: iter(), next(), and for Loops"
description: "Learn the iterable and iterator protocols, how for loops use iter() and next(), and how to create custom iterable classes in Python."
slug: "python-iterators-and-iterables-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "Iterators"
  - "Iterables"
  - "Language Fundamentals"
relatedTools: []
relatedArticles:
  - "python-generators-and-yield-guide"
  - "python-list-comprehensions-guide"
draft: false
---

# Python Iterables and Iterators Explained: iter(), next(), and for Loops

Python uses iteration throughout the language. Lists, tuples, strings, dictionaries, files, and generators can all be processed with for loops.

To understand how iteration works, it helps to distinguish an iterable from an iterator.

## What is an iterable?

An iterable is an object from which an iterator can be obtained. In Python, this is commonly done through iter().

Examples include:
- Lists.
- Tuples.
- Strings.
- Dictionaries.
- Sets.
- File objects.
- Range objects.

~~~python
names = ["Asha", "Ravi", "Meera"]

for name in names:
    print(name)
~~~

The for loop obtains an iterator and requests values until the iterator is exhausted.

## What is an iterator?

An iterator produces values one at a time. It implements the iterator protocol, including:
- __iter__(), which returns the iterator itself.
- __next__(), which returns the next value or raises StopIteration when exhausted.

~~~python
numbers = [10, 20, 30]
iterator = iter(numbers)

print(next(iterator))
print(next(iterator))
print(next(iterator))
~~~

Calling next() after the last value raises StopIteration.

## Iterable versus iterator

| Iterable | Iterator |
|---|---|
| Can provide an iterator | Produces values |
| Often supports repeated iteration | Usually consumed once |
| Examples: list, tuple, range | Examples: list_iterator, generator |
| Provides __iter__() | Provides __iter__() and __next__() |

A list is iterable but is not itself an iterator. Calling iter(list) creates a list iterator.

## How a for loop works conceptually

This loop:

~~~python
for item in collection:
    process(item)
~~~

Can be understood conceptually as obtaining an iterator and repeatedly calling next() until StopIteration occurs.

Python handles the termination exception automatically in a for loop.

## Build a custom iterator

A class can implement the iterator protocol:

~~~python
class CountDown:
    def __init__(self, start):
        self.current = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.current <= 0:
            raise StopIteration

        value = self.current
        self.current -= 1
        return value


for number in CountDown(3):
    print(number)
~~~

This iterator stores its position in the object and returns one value per next() call.

Because the iterator object is its own state, iterating over the same instance again does not restart it. A separate iterable class can return a new iterator each time when repeatable iteration is needed.

## Generators are a convenient way to create iterators

A generator function automatically implements the iterator protocol:

~~~python
def count_down(start):
    while start > 0:
        yield start
        start -= 1
~~~

Generators are often simpler than writing a custom iterator class when the sequence can be described with yield statements.

## Iterating over dictionaries

Iterating over a dictionary directly produces its keys:

~~~python
person = {"name": "Asha", "age": 28}

for key in person:
    print(key)
~~~

Use items() to access key-value pairs:

~~~python
for key, value in person.items():
    print(key, value)
~~~

Dictionary iteration order is guaranteed to be insertion order in modern Python language versions.

## Infinite iterators

Some iterators can produce values indefinitely. For example, a counter generator may never reach StopIteration.

When consuming an infinite iterator, use a stopping condition or a bounded tool such as itertools.islice().

Be careful when converting an infinite iterator to a list; the operation cannot finish.

## Common mistakes

### Calling iter() on a non-iterable object

The object must implement the iterable protocol or otherwise support iteration.

### Reusing an exhausted iterator

Create a new iterator when another pass is needed.

### Treating a generator as a list

Iterators do not generally support indexing or length. Consume them intentionally.

### Implementing __next__ without StopIteration

An iterator must signal exhaustion with StopIteration. Otherwise, a loop may never terminate.

## Frequently asked questions

### Is every iterator iterable?

Yes. An iterator returns itself from __iter__().

### Is every iterable an iterator?

No. A list is iterable, but iter(list) returns a separate iterator object.

### Why does a generator return an iterator?

A generator object implements the iterator protocol and preserves its execution state between yielded values.

## Further reading

- [Python: Iterator Types](https://docs.python.org/3/library/stdtypes.html#iterator-types)
- [Python: Iterators](https://docs.python.org/3/tutorial/classes.html#iterators)
- [Python: itertools](https://docs.python.org/3/library/itertools.html)

---
