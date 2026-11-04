---
title: "Python List Comprehensions Explained with Practical Examples"
description: "Learn Python list comprehensions, filtering conditions, nested comprehensions, readability guidelines, and equivalent loop-based code."
slug: "python-list-comprehensions-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "List Comprehension"
  - "Programming"
  - "Data Processing"
relatedTools: []
relatedArticles:
  - "python-virtual-environments-guide"
draft: false
---

# Python List Comprehensions Explained with Practical Examples

Python list comprehensions provide a concise way to create a new list from an iterable. They are commonly used to transform values, filter items, and build simple collections.

A comprehension can make straightforward transformations easier to read. Complex nested expressions, however, may be clearer as ordinary loops.

## Basic list comprehension syntax

The general form is:

~~~python
new_list = [expression for item in iterable]
~~~

For example, create a list of squares:

~~~python
numbers = [1, 2, 3, 4, 5]

squares = [number * number for number in numbers]

print(squares)
# [1, 4, 9, 16, 25]
~~~

The expression is evaluated for each item in the iterable, and its result is added to the new list.

## Filter values with an if condition

Add an if clause to keep only items that satisfy a condition:

~~~python
numbers = [1, 2, 3, 4, 5, 6]

evens = [number for number in numbers if number % 2 == 0]

print(evens)
# [2, 4, 6]
~~~

The condition controls whether an item contributes a value to the output.

## Transform and filter together

A comprehension can transform each selected value:

~~~python
names = ["asha", "ravi", "meera", ""]

normalized = [
    name.strip().title()
    for name in names
    if name.strip()
]

print(normalized)
# ['Asha', 'Ravi', 'Meera']
~~~

Keep the expression understandable. Repeating a costly transformation inside both the expression and filter can be less efficient; an explicit loop may be better for complicated work.

## Use if-else expressions

An if-else expression can choose the output value:

~~~python
numbers = [1, 2, 3, 4]

labels = [
    "even" if number % 2 == 0 else "odd"
    for number in numbers
]

print(labels)
# ['odd', 'even', 'odd', 'even']
~~~

This is different from a trailing filter if clause. The conditional expression chooses a value, while the trailing if filters items out.

## Nested list comprehensions

A nested comprehension can flatten a simple nested structure:

~~~python
matrix = [
    [1, 2],
    [3, 4],
    [5, 6]
]

flat = [value for row in matrix for value in row]

print(flat)
# [1, 2, 3, 4, 5, 6]
~~~

The order of for clauses follows the order of nested loops. Nested comprehensions become difficult to understand when many conditions and transformations are combined.

## Dictionary and set comprehensions

Python supports related syntax for dictionaries and sets.

Dictionary comprehension:

~~~python
names = ["asha", "ravi"]

lengths = {name: len(name) for name in names}
~~~

Set comprehension:

~~~python
numbers = [1, 2, 2, 3, 3, 3]

unique_squares = {number * number for number in numbers}
~~~

A set removes duplicate values according to its equality and hashing rules.

## List comprehension versus a for loop

The following loop:

~~~python
squares = []

for number in range(5):
    squares.append(number * number)
~~~

Can be written as:

~~~python
squares = [number * number for number in range(5)]
~~~

Both forms produce the same list. Choose the version that makes the intent clearest.

## When not to use a comprehension

Use a normal loop when:

- Several statements are needed for each item.
- The logic contains complex branching.
- You need detailed exception handling.
- Side effects are involved.
- Intermediate steps improve clarity.
- The resulting expression is difficult to review.

A comprehension is intended for constructing collections, not as a general replacement for every loop.

## Common mistakes

### Mixing up filtering and conditional expressions

A trailing if filters elements. An if-else expression chooses which value to include.

### Writing overly nested comprehensions

Long expressions may save lines but cost readability. Use explicit loops when the logic becomes difficult to follow.

### Modifying a collection during iteration

Create a new list or use an intentional in-place strategy. Avoid changing a collection in ways that make iteration behavior unclear.

### Forgetting that comprehensions create a new list

A list comprehension materializes its result in memory. For large or lazy data processing, consider a generator expression.

## Frequently asked questions

### Are list comprehensions faster than loops?

They can be faster for many simple operations, but performance depends on the workload and implementation. Prefer clarity and measure when performance is important.

### What is the difference between a list comprehension and a generator expression?

A list comprehension creates a list immediately. A generator expression yields values lazily as they are requested.

### Can list comprehensions have multiple for clauses?

Yes. They can represent nested loops, but readability should guide whether that form is appropriate.

## Further reading

- [Python Tutorial: List Comprehensions](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)
- [Python Language Reference: Displays](https://docs.python.org/3/reference/expressions.html#displays-for-lists-sets-and-dictionaries)

---
