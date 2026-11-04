---
title: "Test-Driven Development Explained: Red, Green, Refactor"
description: "Learn the TDD cycle, write tests before implementation, refactor safely, and understand when test-driven development is useful."
slug: "test-driven-development-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Software Testing"
tags:
  - "TDD"
  - "Software Testing"
  - "Unit Testing"
  - "Software Design"
relatedTools: []
relatedArticles:
  - "software-testing-pyramid-guide"
draft: false
---

# Test-Driven Development Explained: Red, Green, Refactor

Test-Driven Development (TDD) is a software development practice in which a developer writes a failing test before writing the implementation that makes it pass.

The repeated cycle is often described as **Red, Green, Refactor**. TDD encourages small steps, clear behavior, and a feedback loop that supports design and regression testing.

## What is TDD?

TDD changes the order in which code and tests are created.

Instead of writing an implementation and adding tests afterward, the developer first describes an expected behavior in an automated test.

The test initially fails because the behavior does not exist. The implementation is then changed until the test passes. After that, the code can be refactored while the test suite checks that the behavior remains correct.

## The Red, Green, Refactor cycle

### Red: write a failing test

Start with a small requirement.

Suppose a function should determine whether a number is even:

~~~python
def test_is_even():
    assert is_even(4) is True
    assert is_even(5) is False
~~~

The test fails because is_even has not been implemented.

### Green: write the smallest implementation

Implement enough code to pass the test:

~~~python
def is_even(number):
    return number % 2 == 0
~~~

Run the tests again. The expected behavior should now pass.

### Refactor: improve the code

Refactor the implementation or test structure while keeping behavior unchanged.

For a small function, there may be little to refactor. In larger code, this step can improve naming, duplication, interfaces, or internal structure.

Run the tests again after refactoring.

## Why write the test first?

Writing the test first encourages developers to think about the expected behavior and how the code will be used.

It can help:

- Clarify requirements.
- Expose missing edge cases.
- Encourage small interfaces.
- Provide a regression test for each behavior.
- Support refactoring with fast feedback.

TDD does not guarantee good design or complete test coverage. The quality of the tests and the understanding of requirements still matter.

## Work in small increments

A common TDD mistake is writing a large test suite before implementing everything. The cycle is more effective when each step is small.

For example, a discount function may be developed in stages:

1. Full price when discount is zero.
2. Reduced price for a valid percentage.
3. Reject a negative discount.
4. Reject a discount above the supported range.
5. Define rounding behavior.

Each test describes one meaningful behavior. The implementation grows in response to the tests.

## TDD and refactoring

A passing test suite provides some confidence when code structure changes. It is important that tests validate behavior rather than internal implementation details.

Tests that assert private function call order or internal variable names can make refactoring unnecessarily difficult.

Use mocks when an interaction is part of the contract, not just to inspect every line of implementation.

## TDD versus test-after development

Both approaches can produce good tests. TDD emphasizes using tests as a design and feedback tool before and during implementation. Test-after development adds tests after code has been written.

Teams may combine both approaches, especially when working with legacy code, hardware dependencies, or requirements that are difficult to express in a test first.

## Where TDD can be challenging

TDD may be more difficult when:

- Requirements are unclear or rapidly changing.
- The system depends on hardware that is unavailable.
- Visual design or user research must happen first.
- The behavior is difficult to isolate.
- A suitable test environment has not been built.

In these cases, use exploratory testing, prototypes, integration tests, or hardware simulation to learn about the system before deciding on the next test.

## TDD in embedded software

TDD can be used for pure logic, protocol parsing, state machines, signal conversion, and error handling. Hardware-dependent code can be separated behind interfaces so that much of the logic can be tested on a host machine.

Target testing and hardware-in-the-loop testing remain important for timing, peripheral configuration, electrical behavior, and hardware-specific integration.

## Common mistakes

### Writing tests that merely repeat the implementation

A test should express expected behavior independently. Copying the same logic into the test can reproduce the same defect.

### Making tests too large

Large tests provide slower and less precise feedback. Break behavior into small cases with clear names.

### Skipping refactoring

TDD includes the refactor step. Passing tests are an opportunity to improve code structure safely.

### Treating TDD as a universal rule

It is a practice, not a requirement for every task. Use it where a test-first feedback loop adds value.

## Frequently asked questions

### Does TDD mean writing all tests before coding?

No. TDD is an iterative cycle: write one failing test, implement enough to pass, refactor, and repeat.

### Does TDD eliminate the need for integration tests?

No. Unit-level TDD does not prove that databases, networks, hardware, or deployed components work together.

### Can TDD be used with C?

Yes. C code can be tested using frameworks such as Unity, CppUTest, or GoogleTest through C++ wrappers, depending on the project. Hardware-specific parts may need fakes, simulators, or target tests.

## Further reading

- [Martin Fowler: Test-Driven Development](https://martinfowler.com/bliki/TestDrivenDevelopment.html)
- [Kent Beck: Test-Driven Development by Example](https://www.pearson.com/en-us/subject-catalog/p/test-driven-development-by-example/P200000000138/9780321146533)
- [Google Testing Blog](https://testing.googleblog.com/)

---
