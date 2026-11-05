---
title: "Unit Testing Embedded C: Host Tests, Hardware Abstraction, and Fakes"
description: "Learn how to unit test embedded C code on a host machine, isolate hardware dependencies, use fakes and mocks, and test boundary conditions."
slug:"unit-testing-embedded-c-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Software Testing"
tags:
  - "Embedded C"
  - "Unit Testing"
  - "Firmware Testing"
  - "Test Automation"
relatedTools: []
relatedArticles:
  - "unit-testing-vs-integration-testing-guide"
  - "mocking-and-stubbing-explained"
draft: false
---

# Unit Testing Embedded C: Host Tests, Hardware Abstraction, and Fakes

Embedded C software interacts with microcontroller peripherals, registers, interrupts, and timing-sensitive hardware. These dependencies can make automated testing difficult, especially when every test requires the physical target board.

Host-based unit testing lets developers test much of the application logic on a desktop machine. The key is to separate hardware-dependent behavior from logic that can be verified independently.

## What should be unit tested?

Good candidates for unit tests include:
- Signal scaling and conversion.
- Protocol parsing and serialization.
- State machines.
- Input validation.
- Range and limit checks.
- Error-code mapping.
- Diagnostic request construction.
- Data filtering and calculations.
- Buffer management logic.

Tests should focus on behavior with clear inputs and expected outputs. Hardware timing, electrical behavior, and peripheral integration require other test levels.

## Separate logic from hardware access

A tightly coupled function may read a memory-mapped register directly:

~~~c
uint8_t read_sensor_state(void)
{
    return (uint8_t)(*SENSOR_STATUS_REG & SENSOR_READY_MASK);
}
~~~

This function cannot be tested meaningfully on a normal host machine without replacing the hardware access.

A cleaner design places hardware access behind an interface:

~~~c
typedef struct {
    uint32_t (*read_status)(void);
} SensorIo;

uint8_t sensor_is_ready(const SensorIo *io)
{
    return (io->read_status() & SENSOR_READY_MASK) != 0u;
}
~~~

A production implementation connects the interface to the actual register or driver. A test implementation returns controlled values.

The interface should remain small and represent the operations the application needs, rather than copying an entire hardware driver into the test.

## Use fakes for hardware dependencies

A fake provides a simple implementation of a dependency.

For example, a test can supply a fake sensor read function that returns:
- A valid sample.
- A boundary value.
- An out-of-range value.
- A hardware error indication.

This allows deterministic testing of application logic without accessing the physical peripheral.

A fake should model the relevant contract. It does not prove that the actual driver or hardware behaves the same way.

## Test state machines

Embedded software often contains state machines for initialization, communication, diagnostics, and power management.

Test:
1. Valid state transitions.
2. Invalid events.
3. Timeout behavior.
4. Recovery paths.
5. Repeated events.
6. Reset behavior.
7. Unexpected event ordering.

For each test, initialize the state explicitly and verify the resulting state and externally visible actions.

Avoid relying on a previous test to set up shared global state.

## Use a C unit-testing framework

Frameworks such as Unity, CMock, CppUTest, and GoogleTest (often through C++ wrappers or adapters) can support embedded software test workflows.

The selected framework should fit:
- Compiler support.
- C language version.
- Build system.
- Host and target environments.
- Mocking requirements.
- Continuous integration setup.

Keep production C code compilable by the actual embedded toolchain, not just by the host compiler.

## Example: test a conversion function

A pure conversion function can be tested without hardware:

~~~c
#include <assert.h>
#include <stdint.h>

uint16_t raw_to_millivolts(uint16_t raw,
                           uint16_t reference_mv,
                           uint16_t max_count)
{
    if (max_count == 0u) {
        return 0u;
    }

    return (uint16_t)(((uint32_t)raw * reference_mv) /
                      max_count);
}

int main(void)
{
    assert(raw_to_millivolts(0u, 3300u, 4095u) == 0u);
    assert(raw_to_millivolts(4095u, 3300u, 4095u) == 3300u);
    assert(raw_to_millivolts(2047u, 3300u, 4095u) <= 3300u);
    assert(raw_to_millivolts(100u, 3300u, 0u) == 0u);
    return 0;
}
~~~

The example uses assertions for illustration. A project should use its selected test framework and define whether invalid inputs should return a value, set an error, or use another contract.

The intermediate 32-bit cast avoids overflow in the multiplication for the shown 16-bit input types, but production arithmetic still needs range analysis for the actual parameter widths.

## Test error paths and boundaries

For embedded code, boundary conditions often reveal important defects.

Consider:
- Zero-length buffers.
- Maximum supported payload.
- One byte above the maximum.
- Counter wraparound.
- Integer overflow.
- Null pointers where allowed by the interface.
- Invalid enum values.
- Timeout at the exact boundary.
- Repeated calls and reinitialization.
- Resource exhaustion.

Use compiler warnings and static analysis in addition to runtime tests.

## Host tests versus target tests

| Test approach | What it helps verify |
|---|---|
| Host unit test | Application logic and algorithms |
| Host integration test | Modules and interfaces in a controlled environment |
| Target unit test | Behavior with the embedded compiler and target runtime |
| Hardware-in-the-loop | Interaction with real hardware and external signals |
| System test | Complete ECU behavior against requirements |

Host tests can be fast, but differences in integer widths, compiler behavior, memory layout, endianness, and peripheral timing must be considered.

## Common mistakes

### Mocking every function

Tests can become coupled to implementation details. Replace hardware and external dependencies while verifying meaningful behavior.

### Assuming host success proves target success

The host may use a different compiler, integer model, memory layout, and runtime. Build and test with the target toolchain as well.

### Accessing hardware registers in host tests

Isolate MMIO access behind a driver or interface and provide a fake for host tests.

### Ignoring global state

Reset state between tests or provide explicit initialization. Shared global state can cause order-dependent failures.

### Testing only normal inputs

Include boundary, invalid, and failure scenarios. Embedded software often has strict resource and timing limits.

## Frequently asked questions

### Can embedded C be unit tested without a board?

Yes. Pure logic and code with abstracted hardware dependencies can be tested on a host machine.

### Do unit tests replace HIL testing?

No. Unit tests validate isolated software behavior. HIL testing verifies interactions with real hardware and external system behavior.

### Can static analysis replace unit testing?

No. Static analysis and unit testing identify different classes of issues and are complementary quality activities.

## Further reading

- [Unity Test Framework](https://github.com/ThrowTheSwitch/Unity)
- [CppUTest](https://cpputest.github.io/)
- [GoogleTest Documentation](https://google.github.io/googletest/)
- [MISRA C](https://www.misra.org.uk/)

---
