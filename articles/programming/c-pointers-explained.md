---
title: "C Pointers Explained: Addresses, Dereferencing, and Pointer Arithmetic"
description: "Learn how pointers store addresses, use address-of and dereference operators, work with arrays, and avoid common pointer errors in C."
slug:"c-pointers-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Programming"
tags:
  - "C Programming"
  - "Pointers"
  - "Memory"
  - "Embedded C"
relatedTools: []
relatedArticles:
  - "c-memory-management-guide"
draft: false
---

# C Pointers Explained: Addresses, Dereferencing, and Pointer Arithmetic

Pointers are a fundamental feature of C. They allow a program to work with memory addresses, access objects indirectly, pass data efficiently to functions, and interact with hardware or low-level APIs.

Pointers are powerful, but incorrect pointer use can lead to undefined behavior, memory corruption, and security vulnerabilities.

## What is a pointer?

A pointer is an object whose value identifies the location of another object or function, subject to the C language rules.

For example:

~~~c
int value = 42;
int *ptr = &value;
~~~

Here:

- value is an integer object.
- &value obtains its address.
- ptr stores a pointer to an integer.
- *ptr accesses the object pointed to by ptr.

## Address-of operator

The address-of operator (&) obtains the address of an object:

~~~c
int number = 10;
int *pointer = &number;
~~~

The pointer type should be compatible with the type of the object it points to.

A pointer to int should not be treated as a pointer to an unrelated type without a valid reason and correct alignment and aliasing considerations.

## Dereference operator

The unary * operator dereferences a valid pointer:

~~~c
int number = 10;
int *pointer = &number;

printf("%d\n", *pointer);

*pointer = 25;

printf("%d\n", number);
~~~

The assignment through the pointer modifies the original object.

Dereferencing a null, uninitialized, dangling, or otherwise invalid pointer is undefined behavior.

## Pointer types matter

C uses pointer types to determine how pointed-to objects are accessed and how pointer arithmetic behaves.

~~~c
int *int_ptr;
char *char_ptr;
double *double_ptr;
~~~

These pointers may have the same size on a particular platform, but their pointed-to types differ. Do not assume every pointer type has the same representation or that an object can safely be accessed through an arbitrary pointer type.

## Pointers and arrays

In many expressions, an array expression is converted to a pointer to its first element. However, arrays and pointers are not the same type.

~~~c
int values[3] = {10, 20, 30};
int *ptr = values;

printf("%d\n", ptr[1]);
~~~

The expression ptr[1] accesses the second integer element.

The array's size is not carried by the pointer. When passing an array to a function, also pass its element count or use another interface that communicates the length.

## Pointer arithmetic

Pointer arithmetic is scaled by the size of the pointed-to type.

~~~c
int values[3] = {10, 20, 30};
int *ptr = values;

ptr = ptr + 1;

printf("%d\n", *ptr);
~~~

The pointer advances to the next integer element, not simply one byte forward.

In C, pointer arithmetic is defined only within an array object and one element past its end. The one-past pointer may be used for comparison and iteration boundaries, but it must not be dereferenced.

## Pointers as function parameters

A function can receive a pointer to modify an object owned by the caller:

~~~c
void set_value(int *value)
{
    if (value != NULL) {
        *value = 100;
    }
}

int main(void)
{
    int count = 0;

    set_value(&count);

    printf("%d\n", count);
    return 0;
}
~~~

The NULL check protects against a null argument. It does not make every arbitrary pointer valid, so the caller still has a responsibility to pass a pointer to a suitable live object.

## const and pointers

The const keyword can qualify the pointed-to object or the pointer itself.

~~~c
const int *ptr;
~~~

This is a pointer to const int. The object cannot be modified through this pointer.

~~~c
int *const ptr = &value;
~~~

This is a const pointer to int. The pointer itself cannot be reassigned after initialization, but the pointed-to object may be modified.

~~~c
const int *const ptr = &value;
~~~

This is a const pointer to const int.

Understanding where const appears is important when designing APIs that should not modify caller-owned data.

## Common pointer errors

### Uninitialized pointers

A pointer declared without initialization contains an indeterminate value. Do not dereference it.

### Null dereference

Check pointers when the function contract allows NULL. Dereferencing a null pointer is undefined behavior.

### Dangling pointers

A pointer becomes dangling when the object it refers to no longer exists or its lifetime has ended.

### Returning the address of a local variable

A local automatic object ceases to exist when its function returns. Returning its address creates a dangling pointer.

### Buffer overrun

Pointer arithmetic and indexing must remain within the bounds of the same array object, or one past the end for boundary operations.

### Incorrect format specifiers

Use appropriate format specifiers for the type being printed. Pointer values can be printed with %p after converting to void * as required by the printf interface.

## Pointer safety in embedded C

Pointers are frequently used for memory-mapped registers, DMA buffers, peripheral drivers, and callback interfaces.

Follow the hardware and compiler documentation for address mapping, alignment, volatile access, cache coherency, and memory barriers. A volatile-qualified pointer does not by itself guarantee atomicity, synchronization, or cache coherency.

## Frequently asked questions

### Is a pointer just an integer?

No. A pointer is a distinct C type representing a reference to an object or function. Do not assume it can be safely manipulated as an arbitrary integer.

### Can a pointer be NULL?

Yes. NULL is a null pointer constant that can represent a pointer that does not point to an object or function.

### Are arrays and pointers identical in C?

No. Array expressions often convert to pointers, but arrays have a distinct type and size behavior. The sizeof operator on an array object differs from sizeof on a pointer.

## Further reading

- [WG14 C language drafts](https://www.open-std.org/jtc1/sc22/wg14/www/docs/)
- [cppreference: Pointers](https://en.cppreference.com/w/c/language/pointer.html)
- [CERT C Coding Standard](https://wiki.sei.cmu.edu/confluence/display/c/SEI+CERT+C+Coding+Standard)

---
