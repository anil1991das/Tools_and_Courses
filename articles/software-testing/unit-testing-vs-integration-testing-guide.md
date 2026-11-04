---
title: "Unit Testing vs Integration Testing: Differences and Examples"
description: "Understand the purpose of unit and integration tests, compare their scope and dependencies, and decide where each type fits in a test strategy."
slug: "unit-testing-vs-integration-testing-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Software Testing"
tags:
  - "Software Testing"
  - "Unit Testing"
  - "Integration Testing"
  - "Quality Assurance"
relatedTools: []
relatedArticles:
  - "software-testing-pyramid-guide"
  - "mocking-and-stubbing-explained"
draft: false
---

# Unit Testing vs Integration Testing: Differences and Examples

Software testing checks whether code behaves as expected. Different testing levels examine different parts of a system. Unit tests focus on small pieces of logic, while integration tests check whether components work correctly together.

Both are useful. They answer different questions and should not be treated as competing approaches.

## What is unit testing?

A unit test checks a small unit of behavior, often a function, method, or class, in isolation from unrelated dependencies.

For example, a function that calculates a discount can be tested with several inputs:

~~~python
def final_price(price, discount_percent):
    return price * (1 - discount_percent / 100)


def test_final_price():
    assert final_price(100, 10) == 90
    assert final_price(200, 25) == 150
    assert final_price(50, 0) == 50
~~~

This test verifies the calculation without involving a database, network, or user interface.

Good unit tests are usually fast, deterministic, and focused on one behavior. They should cover normal inputs, edge cases, and invalid inputs that the function is expected to handle.

## What is integration testing?

An integration test checks whether multiple components work together correctly.

A web API integration test might verify that:

1. An HTTP request reaches a route.
2. Input validation runs.
3. The service calls a repository.
4. The repository interacts with a test database.
5. The API returns the expected status and response.

The test may use a real test database or a controlled substitute, depending on what the integration needs to verify.

Integration tests can detect problems that isolated unit tests cannot, such as incorrect serialization, database mapping errors, or mismatched component interfaces.

## Key differences

| Aspect | Unit testing | Integration testing |
|---|---|---|
| Scope | Small unit of behavior | Multiple connected components |
| Dependencies | Often isolated or replaced | Some real dependencies are used |
| Speed | Usually faster | Usually slower |
| Failure diagnosis | Often more localized | May involve several components |
| Main purpose | Verify local logic | Verify interactions and boundaries |
| Typical environment | Test process | Test environment with configured dependencies |

The distinction is about scope and purpose. A test does not become an integration test merely because it has many assertions.

## Example: testing an order service

A unit test for an order service may replace the payment gateway and repository with controlled test doubles. It checks that the service calculates totals and requests payment correctly.

An integration test may connect the service to a real test database and verify that an order is persisted with the correct status.

A broader end-to-end test might send a user action through the application interface and verify the entire checkout flow.

Each test level provides different evidence.

## What about mocks and stubs?

Unit tests often use mocks, stubs, or fakes to control dependencies. These test doubles can make a test fast and predictable.

However, replacing every dependency can hide integration errors. A mock may behave differently from the real database or HTTP client.

Use real components when the interaction itself is important to verify. Use test doubles when you need to isolate a unit or simulate a condition that would be difficult to reproduce.

## How many tests should a project have?

There is no universal ratio. Most projects benefit from many focused unit tests, a smaller set of integration tests for important boundaries, and a carefully selected set of end-to-end tests for critical user workflows.

The appropriate balance depends on architecture, risk, runtime cost, and the reliability of the test environment.

## Common mistakes

### Testing implementation details

Tests that assert private method calls or internal variable names can break during harmless refactoring. Prefer observable behavior and public contracts.

### Mocking too much

A test suite can pass while real components fail to work together. Add integration coverage for important boundaries.

### Using production data

Use isolated test data and controlled environments. Tests should not modify real customer records or production systems.

### Making integration tests order-dependent

Each test should create or reset the state it needs. Avoid hidden dependencies on the order in which tests run.

### Treating test coverage as proof of correctness

Coverage indicates which code executed, not whether assertions meaningfully verified behavior.

## Frequently asked questions

### Are unit tests enough?

Usually not. Unit tests are valuable, but integrations, configuration, infrastructure, and user workflows also need suitable verification.

### Should integration tests use real databases?

When database behavior is part of the integration being tested, a real test database provides useful confidence. Lightweight substitutes can still be appropriate for other purposes.

### Which tests should run in CI?

Run fast, reliable checks on every change. Larger integration and end-to-end suites can run in the same pipeline or in additional stages based on execution cost and release risk.

## Further reading

- [Google Testing Blog](https://testing.googleblog.com/)
- [Martin Fowler: Unit Test](https://martinfowler.com/bliki/UnitTest.html)
- [ISTQB Glossary](https://glossary.istqb.org/)

---
