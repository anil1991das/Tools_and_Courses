---
title: "C Structures, Unions, and Enums Explained with Examples"
description: "Learn how C structures group fields, unions share storage, and enumerations define named constants for readable systems programming."
slug: "c-structs-unions-and-enums-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "C Programming"
  - "Structures"
  - "Unions"
  - "Enums"
relatedTools: []
relatedArticles:
  - "c-pointers-explained"
  - "embedded-c-bitwise-operations-guide"
draft: false
---

# C Structures, Unions, and Enums Explained with Examples

C provides several ways to model related values. Structures group multiple members into one object, unions allow different members to share storage, and enumerations define named integer constants.

These features are widely used in systems programming, embedded software, protocol parsing, and application development.

## What is a structure?

A structure is a user-defined type that groups fields, potentially with different types.

~~~c
#include <stdint.h>

struct Sensor {
    uint16_t id;
    int32_t reading;
    uint8_t status;
};
~~~

A variable can be declared using the structure type:

~~~c
struct Sensor sensor = {
    .id = 10,
    .reading = 245,
    .status = 1
};
~~~

Designated initializers make it clear which field receives each value.

## Access structure members

Use the dot operator when accessing a structure object:

~~~c
sensor.reading = 300;
~~~

Use the arrow operator when accessing a structure through a pointer:

~~~c
struct Sensor *sensor_ptr = &sensor;
sensor_ptr->status = 2;
~~~

The expression sensor_ptr->status is equivalent to (*sensor_ptr).status.

## typedef with structures

typedef can create a shorter type name:

~~~c
typedef struct {
    uint32_t id;
    float temperature;
} SensorData;

SensorData data = {
    .id = 42,
    .temperature = 28.5f
};
~~~

typedef is common in APIs and embedded code, though some projects prefer explicit struct tags. Follow the project's naming convention.

## Structure padding and alignment

The compiler may insert padding between structure members or at the end so that members satisfy alignment requirements.

Therefore, sizeof(struct Sensor) may be greater than the sum of its member sizes.

Do not assume that a structure has a portable byte layout across compilers, architectures, or build options.

If a structure is sent over a network or stored in a persistent binary format, define the serialization format explicitly rather than transmitting raw structure memory.

## What is a union?

A union allows its members to share the same storage region:

~~~c
union Data {
    uint32_t integer;
    float real;
    uint8_t bytes[4];
};
~~~

The size of a union is sufficient to contain its largest member, with any required alignment and padding.

A union is useful when a value can have one of several representations and the active member is managed carefully.

## Accessing union members safely

~~~c
union Data data;

data.integer = 100;
printf("%u\n", data.integer);
~~~

After assigning a member, use the member that is active according to the program's representation rules.

Reading a different member can have implementation-defined or representation-specific consequences under C rules. Do not use unions as a portable substitute for arbitrary type punning.

For inspecting object representations, use character types or memcpy where appropriate, and account for endianness and floating-point format.

## What is an enumeration?

An enum defines named integer constants:

~~~c
enum SystemState {
    STATE_INIT,
    STATE_READY,
    STATE_ERROR
};
~~~

By default, values are assigned starting at zero and increasing by one unless explicit values are supplied.

Use enums to make code more readable:

~~~c
enum SystemState state = STATE_READY;

if (state == STATE_ERROR) {
    handle_error();
}
~~~

The compatible integer type of an enum is implementation-defined within the language rules. Do not assume a specific width for a protocol field or hardware register without verifying the compiler and target.

## Assign explicit enum values

Explicit values are useful when the identifiers represent fixed protocol or hardware codes:

~~~c
enum DiagnosticResult {
    DIAG_OK = 0,
    DIAG_NOT_SUPPORTED = 0x11,
    DIAG_INVALID_FORMAT = 0x13
};
~~~

For externally defined values, document the source specification and ensure values remain stable.

## Bit fields

C structures can contain bit-field members:

~~~c
struct Flags {
    unsigned int ready : 1;
    unsigned int error : 1;
    unsigned int mode : 2;
};
~~~

Bit fields can be convenient for compact logical state. Their allocation order, layout, and access behavior are implementation-defined or otherwise constrained by the C implementation. They are not automatically suitable for portable hardware register definitions or network serialization.

For those cases, explicit masks and shifts are often easier to verify.

## When to use each type

| Type | Useful when |
|---|---|
| struct | Several related fields belong to one object |
| union | One storage area represents alternative data members |
| enum | A value represents one of a set of named states |
| bit field | Compact logical flags are needed and compiler layout is controlled |

## Common mistakes

### Assuming structures have no padding

Check alignment and use explicit serialization for persistent or network formats.

### Reading the wrong union member

Track which member is active and follow the C language rules for representation access.

### Assuming enum width is fixed

Use an explicitly sized integer type when a fixed-width representation is required.

### Using bit fields for portable wire formats

Bit-field layout can vary. Pack and unpack protocol data using documented masks, shifts, and byte order.

## Frequently asked questions

### Can a structure contain another structure?

Yes. Nested structures are useful for organizing related data.

### Can a union contain a structure?

Yes. A union member can be a structure, array, or another supported object type.

### Are enums always stored as int?

No. The compatible integer type is implementation-defined, subject to the C standard's constraints. Do not rely on an assumed width for external formats.

## Further reading

- [cppreference: Struct and union](https://en.cppreference.com/w/c/language/struct.html)
- [cppreference: Enumeration](https://en.cppreference.com/w/c/language/enum.html)
- [WG14 C language drafts](https://www.open-std.org/jtc1/sc22/wg14/www/docs/)

---
