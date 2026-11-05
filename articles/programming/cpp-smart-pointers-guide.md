---
title: "C++ Smart Pointers Explained: unique_ptr, shared_ptr, and weak_ptr"
description: "Learn how C++ smart pointers manage object lifetimes, compare unique ownership and shared ownership, and avoid common memory-management problems."
slug: "cpp-smart-pointers-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "C++"
  - "Smart Pointers"
  - "Memory Management"
  - "RAII"
relatedTools: []
relatedArticles:
  - "c-memory-management-guide"
draft: false
---

# C++ Smart Pointers Explained: unique_ptr, shared_ptr, and weak_ptr

C++ provides smart pointers in the standard library to manage dynamically allocated objects through ownership-aware wrappers.

They are part of the Resource Acquisition Is Initialization (RAII) approach, where resource lifetime is tied to object lifetime. Smart pointers reduce the need for manual delete calls and help make ownership visible in APIs.

The most common smart pointers are unique_ptr, shared_ptr, and weak_ptr.

## Why use smart pointers?

Raw pointers can represent non-owning references, but they do not express ownership automatically. Manual memory management can lead to:
- Memory leaks.
- Double deletion.
- Use-after-free.
- Unclear ownership transfer.
- Exception-safety problems.

Smart pointers automate the release behavior for owned objects when their managing wrapper is destroyed.

Include the memory header:

~~~cpp
#include <memory>
~~~

## std::unique_ptr: exclusive ownership

unique_ptr represents one exclusive owner of an object.

~~~cpp
#include <memory>

auto value = std::make_unique<int>(42);
~~~

When the unique_ptr goes out of scope, it destroys the managed object automatically.

A unique_ptr cannot be copied, because copying would create two exclusive owners. It can be moved to transfer ownership.

~~~cpp
auto first = std::make_unique<int>(10);
auto second = std::move(first);
~~~

After the move, second owns the object. first is empty.

Use unique_ptr as the default smart pointer for dynamic ownership when one owner is sufficient.

## std::shared_ptr: shared ownership

shared_ptr represents shared ownership. Multiple shared_ptr objects can refer to the same managed object.

~~~cpp
auto first = std::make_shared<int>(100);
auto second = first;
~~~

The object remains alive while at least one shared owner remains.

A shared_ptr typically uses a control block to track strong and weak references. This adds overhead compared with unique_ptr, so shared ownership should be used when the domain really requires it.

## std::weak_ptr: non-owning observation

weak_ptr observes an object managed by shared_ptr without increasing its strong reference count.

~~~cpp
auto owner = std::make_shared<int>(25);
std::weak_ptr<int> observer = owner;

if (auto value = observer.lock()) {
    // The object is still alive.
}
~~~

lock() attempts to create a shared_ptr. If the object has already been destroyed, the result is empty.

weak_ptr is useful for breaking ownership cycles and representing optional observation of shared objects.

## Avoid shared_ptr ownership cycles

Imagine object A owns B through shared_ptr, while B also owns A through shared_ptr. Each object keeps the other alive, so neither reference count reaches zero.

A weak_ptr on one side can break the cycle:

~~~cpp
struct Node {
    std::shared_ptr<Node> child;
    std::weak_ptr<Node> parent;
};
~~~

The child can own its parent relationship without keeping the parent alive indefinitely.

## Prefer make_unique and make_shared

Factory functions provide clear construction syntax:

~~~cpp
auto device = std::make_unique<Device>();
auto session = std::make_shared<Session>();
~~~

make_shared commonly combines object and control-block allocation, improving allocation efficiency. The lifetime and allocation behavior can differ from constructing shared_ptr directly from a separately allocated raw pointer.

Do not create multiple independent smart pointers from the same raw owning pointer. That can result in multiple deletions of the same object.

## Custom deleters

A smart pointer can manage resources other than ordinary objects by using a custom deleter.

For example, a C library may provide a handle that must be released with a specific function. A custom deleter can ensure the correct cleanup operation is called.

RAII wrappers are often useful for file handles, sockets, locks, and operating-system resources.

## Smart pointers and function parameters

Choose parameter types according to ownership semantics:

- T& or const T& for a required non-owning reference.
- T* for an optional or non-owning pointer when that convention is appropriate.
- unique_ptr<T> by value when ownership should transfer.
- shared_ptr<T> by value when shared ownership is intentionally extended.
- weak_ptr<T> when observing a shared object without owning it.

Do not pass shared_ptr everywhere simply because an object happens to be managed by one. A reference may express the dependency more clearly.

## Smart pointers in embedded systems

Smart pointers can be useful in embedded C++ projects, but their cost and behavior must match the platform's requirements.

Consider:
- Dynamic allocation policy.
- Heap fragmentation.
- Runtime and library support.
- Worst-case execution time.
- Code size.
- Exception policy.
- Ownership and object lifetime.

Some safety-critical or resource-constrained projects prohibit or tightly control dynamic allocation. In such cases, static storage, fixed pools, or project-specific handles may be more appropriate.

## Common mistakes

### Using shared_ptr for every object

Shared ownership adds reference-counting overhead and can obscure lifecycle design. Prefer unique ownership unless sharing is required.

### Creating shared_ptr from the same raw pointer twice

This creates separate control blocks and can lead to double deletion. Transfer an existing smart pointer or use make_shared.

### Forgetting ownership cycles

Mutually owning shared_ptr objects can keep resources alive indefinitely. Use weak_ptr for non-owning links.

### Using get() to transfer ownership

get() returns a non-owning raw pointer. It does not release ownership from the smart pointer.

### Assuming smart pointers make all code thread-safe

Shared-pointer reference-count operations have defined thread-safety properties, but concurrent access to the managed object's state still requires synchronization.

## Frequently asked questions

### Which smart pointer should I use by default?

Use unique_ptr for exclusive ownership. Use shared_ptr when shared lifetime is genuinely needed, and weak_ptr for non-owning observation of a shared object.

### Does unique_ptr support arrays?

Yes. unique_ptr<T[]> supports dynamically allocated arrays, though standard containers such as vector are often a more convenient choice.

### Do smart pointers replace every raw pointer?

No. Raw pointers and references remain useful for non-owning access, interoperability, and low-level APIs. Make ownership explicit.

## Further reading

- [cppreference: std::unique_ptr](https://en.cppreference.com/w/cpp/memory/unique_ptr.html)
- [cppreference: std::shared_ptr](https://en.cppreference.com/w/cpp/memory/shared_ptr.html)
- [C++ Core Guidelines: Resource Management](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-resource)

---
