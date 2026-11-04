---
title: "Mocking and Stubbing Explained: Test Doubles in Software Testing"
description: "Understand mocks, stubs, fakes, and spies, learn when to isolate dependencies, and avoid brittle tests that verify too much implementation detail."
slug: "mocking-and-stubbing-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Software Testing"
tags:
  - "Mocking"
  - "Stubbing"
  - "Unit Testing"
  - "Test Automation"
relatedTools: []
relatedArticles:
  - "unit-testing-vs-integration-testing-guide"
draft: false
---

# Mocking and Stubbing Explained: Test Doubles in Software Testing

A unit under test may depend on a database, network service, file system, clock, or another module. Requiring every dependency for every test can make the suite slow and difficult to control.

Test doubles replace or observe dependencies so a test can focus on a specific behavior. Mocks and stubs are two common kinds of test doubles, but the terms are not interchangeable.

## What is a test double?

A test double is a substitute used in place of a real dependency during a test.

Common categories include:

- **Dummy:** passed to code but not used.
- **Stub:** returns predefined data for a test.
- **Fake:** provides a lightweight working implementation, such as an in-memory repository.
- **Mock:** verifies expected interactions or calls.
- **Spy:** records interactions so the test can inspect them.

Different testing libraries use these terms in different ways. The important point is to understand whether the replacement supplies behavior, records calls, or verifies interactions.

## What is a stub?

A stub provides a controlled response.

For example, a unit test for an order service may use a payment gateway stub that always returns a successful test result.

~~~python
class PaymentStub:
    def charge(self, amount):
        return {"approved": True, "reference": "TEST-001"}
~~~

The test can focus on how the order service handles an approved payment without contacting a real payment provider.

A stub is useful when the test needs predictable dependency output.

## What is a mock?

A mock can verify that a dependency was called in an expected way.

For example, a test may verify that an email service receives the correct recipient after an order is created.

~~~python
from unittest.mock import Mock

email_sender = Mock()

email_sender.send("customer@example.com", "Order confirmed")

email_sender.send.assert_called_once_with(
    "customer@example.com",
    "Order confirmed"
)
~~~

The mock checks an interaction contract. It does not prove that the real email provider accepted or delivered the message.

## What is a fake?

A fake is a working but simplified implementation.

An in-memory repository can store objects in a list or dictionary. It provides enough behavior for tests without needing a database server.

Fakes can be useful, but their behavior may differ from the real implementation. Keep integration tests that exercise the actual database or service adapter.

## What is a spy?

A spy wraps or observes an object and records how it is used.

It can help verify calls while retaining real behavior. Spy semantics differ across test libraries, so consult the framework documentation.

## When should dependencies be replaced?

Use test doubles when a dependency is:

- Slow or expensive.
- External to the system under test.
- Nondeterministic.
- Difficult to configure for a specific failure.
- Dangerous to call in a test, such as a real payment or email endpoint.
- Outside the behavior that the unit test is intended to verify.

Do not replace every dependency automatically. If an interaction is the purpose of the test, a real component may provide better evidence.

## Avoid over-mocking

A test can become brittle when it verifies every internal function call. Refactoring the implementation may break the test even though the externally visible behavior remains correct.

Prefer testing observable outcomes and important collaboration contracts. Verify interactions when they are part of the behavior, such as not charging a payment twice or sending a message only after a successful commit.

## Mocking asynchronous dependencies

An asynchronous HTTP client can be replaced with a controlled response, allowing tests to verify success, timeout, and error handling without relying on a network service.

Tests should cover both resolved and rejected promises or futures. Ensure the fake or mock follows the actual dependency's asynchronous interface.

## Mocking time and randomness

Time-dependent logic can be hard to test if it reads the system clock directly. Inject a clock or use a test framework's time-control facilities.

Similarly, inject a deterministic random source where reproducible tests are needed. Do not replace cryptographically secure randomness with a predictable generator in production code.

## Common mistakes

### Mocking the code under test

A test that replaces the behavior it intends to validate provides little confidence. Replace external dependencies, not the core operation being tested.

### Verifying too many calls

Tests become coupled to implementation details when every internal call is asserted. Focus on meaningful behavior.

### Using fakes without contract checks

A fake database repository may not behave exactly like the real database. Add integration tests against the actual persistence adapter.

### Accidentally reaching production services

Use separate credentials, test endpoints, network restrictions, and explicit configuration so test suites cannot trigger real operations.

### Treating mocks as proof of integration

A mock verifies an expected interaction according to the test setup. It does not prove that the real dependency implements the same contract.

## Frequently asked questions

### Are mocks and stubs the same?

Both are test doubles. A stub provides predefined responses; a mock is commonly used to verify expected interactions.

### Should unit tests use mocks?

Use them when isolation or control is useful. Simple pure functions often need no mocks, and some units are better tested with a fake or real lightweight dependency.

### Can integration tests use mocks?

Yes, but an integration test should still include the real components whose interaction it is intended to verify. Mock only the parts outside that boundary.

## Further reading

- [Martin Fowler: Test Double](https://martinfowler.com/bliki/TestDouble.html)
- [Python: unittest.mock](https://docs.python.org/3/library/unittest.mock.html)
- [Google Testing Blog](https://testing.googleblog.com/)

---
