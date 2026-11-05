---
title: "Python Classes and Object-Oriented Programming Explained"
description: "Learn Python classes, objects, constructors, instance methods, inheritance, composition, and encapsulation with practical examples."
slug: "python-classes-and-oop-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "Object-Oriented Programming"
  - "Classes"
  - "Inheritance"
relatedTools: []
relatedArticles:
  - "python-dataclasses-guide"
  - "python-type-hints-guide"
draft: false
---

# Python Classes and Object-Oriented Programming Explained

Object-oriented programming (OOP) organizes software around objects that combine data and behavior. Python supports classes, inheritance, composition, and other object-oriented techniques.

Classes are useful when an application has concepts with state and operations. They are not required for every problem; functions and simple data structures can be clearer for straightforward tasks.

## Define a class

A class describes how objects of a particular kind are created and behave.

~~~python
class User:
    def __init__(self, name: str, email: str) -> None:
        self.name = name
        self.email = email

    def display_name(self) -> str:
        return self.name


user = User("Asha", "asha@example.com")
print(user.display_name())
~~~

The __init__ method initializes a newly created instance. self refers to the current instance and is passed automatically when an instance method is called through an object.

## Instance attributes and class attributes

Instance attributes belong to each object:

~~~python
class Device:
    def __init__(self, serial_number):
        self.serial_number = serial_number
~~~

Class attributes are defined on the class and are shared through the class namespace:

~~~python
class Device:
    category = "sensor"
~~~

Be careful with mutable class attributes such as lists or dictionaries. Mutating a shared class-level list can affect every instance that uses it.

## Instance, class, and static methods

An instance method receives self and works with instance state.

A class method uses the @classmethod decorator and receives cls. It is often used for alternative constructors or class-level operations.

A static method uses @staticmethod and receives no automatic instance or class argument.

Choose the method type according to the responsibility rather than using decorators only for style.

## Inheritance

Inheritance allows a class to extend or specialize another class.

~~~python
class Vehicle:
    def start(self):
        return "Vehicle started"


class ElectricCar(Vehicle):
    def charge(self):
        return "Charging"
~~~

ElectricCar inherits the start method and adds charge.

Inheritance can express an is-a relationship, but deep inheritance hierarchies can make software difficult to understand and change.

## Composition

Composition builds a class using other objects rather than inheriting their behavior.

~~~python
class Engine:
    def start(self):
        return "Engine started"


class Car:
    def __init__(self, engine):
        self.engine = engine

    def start(self):
        return self.engine.start()
~~~

A Car has an Engine. Composition can make dependencies easier to replace and test.

## Encapsulation and properties

Python uses naming conventions and descriptors rather than strict private fields in the same way as some languages.

A leading underscore signals that an attribute is intended for internal use. Double leading underscores trigger name mangling, but they do not create absolute privacy.

A property can provide controlled access:

~~~python
class Temperature:
    def __init__(self, celsius):
        self.celsius = celsius

    @property
    def celsius(self):
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Below absolute zero")
        self._celsius = value
~~~

The property exposes an attribute-like interface while validating updates.

## Special methods

Python classes can implement special methods to integrate with language operations.

Examples include:
- __str__ for a user-friendly string representation.
- __repr__ for a developer-oriented representation.
- __len__ for length.
- __iter__ for iteration.
- __eq__ for equality behavior.
- __enter__ and __exit__ for context management.

Implement special methods only when the behavior is meaningful and consistent with Python expectations.

## When to use classes

Classes are useful when:
- Data and behavior belong together.
- Multiple objects need independent state.
- A domain concept has clear operations.
- A component needs a replaceable interface.
- Resource ownership has a defined lifecycle.

For a simple transformation, a function may be easier to test and reuse than a class.

## Common mistakes

### Putting everything into one class

Classes should have clear responsibilities. Split unrelated behavior into functions or collaborating objects.

### Using inheritance only for code reuse

Composition may be simpler when there is no genuine is-a relationship.

### Overusing getters and setters

Python often favors direct attribute access unless validation or computed behavior is needed.

### Sharing mutable class attributes unintentionally

Use instance attributes for state that belongs to each object.

## Frequently asked questions

### Is Python a pure object-oriented language?

No. Python supports object-oriented, procedural, and functional programming styles.

### What is self?

self is the conventional name for the instance parameter of an instance method. It is passed automatically when the method is invoked through an instance.

### Does Python have private variables?

Python uses conventions and name mangling, not strict private access enforcement. A leading underscore signals an implementation detail.

## Further reading

- [Python Tutorial: Classes](https://docs.python.org/3/tutorial/classes.html)
- [Python Data Model](https://docs.python.org/3/reference/datamodel.html)
- [Python: abc module](https://docs.python.org/3/library/abc.html)

---
