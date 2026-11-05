---
title: "AUTOSAR COM Signals and I-PDU Routing: Configuration Essentials"
description: "Understand AUTOSAR COM signals, I-PDUs, signal groups, update bits, transmission modes, and the routing configuration needed for reliable ECU communication."
slug: "autosar-com-signal-pdu-routing-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR"
  - "COM"
  - "I-PDU"
  - "PduR"
  - "CAN Communication"
relatedTools: []
relatedArticles:
  - "autosar-communication-stack-explained"
  - "autosar-classic-architecture-guide"
draft: false
---

# AUTOSAR COM Signals and I-PDU Routing: Configuration Essentials

AUTOSAR Classic applications commonly exchange network data as signals. The COM module packs those signals into I-PDUs for transmission and extracts them from received I-PDUs.

A signal definition alone is not enough to produce communication on a vehicle bus. The signal must be mapped into an I-PDU, the PDU must be routed correctly, and the lower layers must be configured to transmit or receive it.

## Signal, signal group, and I-PDU

A **signal** represents an individual data value, such as vehicle speed, a temperature, or a status bit.

A **signal group** groups related signals that may need to be accessed or transferred together according to the COM configuration.

An **I-PDU** is the communication unit handled by the AUTOSAR communication stack. It contains one or more signals or signal groups packed according to the network layout.

For example, a CAN frame with an 8-byte payload may carry several application signals at defined bit positions. The network description and AUTOSAR configuration must agree on those positions and formats.

## Signal packing configuration

Important signal properties include:

- Start bit.
- Signal length.
- Byte order.
- Data type and signedness.
- Initial value.
- Invalid value or substitution behavior, where configured.
- Update bit.
- Transfer property and timeout behavior.

An incorrect start bit or byte order can result in a frame that is transmitted successfully but interpreted incorrectly by the receiving ECU.

Always compare the generated configuration with the authoritative network description, such as the OEM-approved DBC or ARXML data.

## Transmission modes

COM can be configured to transmit I-PDUs according to different modes.

Common concepts include:

- Periodic transmission.
- Direct transmission triggered by a signal update or configured event.
- Mixed transmission behavior.
- Repetition or minimum-delay behavior for selected configurations.

The exact behavior depends on the AUTOSAR version, COM configuration, and vendor implementation.

A signal write does not necessarily trigger an immediate bus transmission. The I-PDU's configured transmission mode and timing conditions determine when it is sent.

## Update bits and signal freshness

An update bit can indicate whether a signal or group has been updated since the previous transmission or reception event, according to the configured semantics.

Update bits are useful when receivers need to distinguish a newly updated value from a value that has not changed.

They must be configured consistently at both ends. A mismatch in update-bit location or interpretation can cause the receiver to treat valid data as stale or unchanged.

## PDU routing through PduR

PduR routes communication PDUs between configured modules. For a normal COM-to-CAN transmit path, the routing is typically:

~~~text
COM -> PduR -> CanIf -> CAN Driver
~~~

The receive path generally travels in the opposite direction.

For diagnostic communication, routing may connect Dcm and CanTp through PduR. The route and buffer requirements differ from signal-based COM transmission.

Check the source and destination PDU identifiers, module references, and generated routing tables. Names in configuration files are helpful, but runtime PDU identifiers and configured interfaces determine the actual path.

## I-PDU groups

COM I-PDU groups provide a way to control groups of communication I-PDUs. Depending on the configuration, group activation and deactivation can be used during ECU startup, shutdown, or communication-mode changes.

When a PDU is not transmitted, verify whether its group is started and whether its related communication mode is active.

The application may write a signal correctly while the associated I-PDU group remains stopped.

## Reception and timeout supervision

On reception, COM extracts configured signal values from the I-PDU. Reception timeout supervision can detect that an expected I-PDU has not arrived within the configured time.

The resulting behavior may involve notification, invalidation, replacement, or other configured handling.

A timeout does not by itself prove that the bus is faulty. It may result from:

- The sender not transmitting.
- Incorrect CAN ID or frame format.
- Rx filtering.
- PduR routing errors.
- Incorrect timeout configuration.
- Communication mode or PDU group state.
- Bus-off or transceiver issues.

Trace the complete receive path before assigning the cause.

## A configuration review checklist

For a transmit PDU:

1. Confirm the signal is mapped to the intended I-PDU.
2. Verify bit position, length, byte order, and data type.
3. Check initial values and update-bit behavior.
4. Confirm the I-PDU group is active.
5. Review transmission mode and timing parameters.
6. Verify PduR destination mapping.
7. Confirm CanIf and hardware object configuration.
8. Validate the transmitted payload using a bus analyzer.

For a receive PDU, verify the matching information and inspect the path from CanIf through PduR and COM to the RTE.

## Common mistakes

### Mixing signal names with PDU identifiers

A signal is not the same as an I-PDU or a CAN identifier. Each represents a different level of the communication configuration.

### Ignoring the network description

The sender and receiver must agree on the signal layout. Check the approved communication matrix and generated configuration.

### Assuming a timeout always means a bus problem

Timeouts can arise at different layers. Inspect Rx indication, routing, signal extraction, and supervision configuration.

### Forgetting I-PDU group state

Communication startup and shutdown behavior may control whether a configured PDU is active.

### Testing only the raw frame

A correct CAN frame does not guarantee that the application receives the expected signal. Verify both bus-level content and application-level value.

## Frequently asked questions

### Can one I-PDU contain multiple signals?

Yes. COM commonly packs multiple signals and signal groups into an I-PDU according to the configured layout.

### What is the difference between a signal and an I-PDU?

A signal represents an application data value. An I-PDU is the communication unit that carries one or more signals through the stack.

### Does PduR interpret signal values?

PduR routes PDUs between modules. Signal packing and extraction are handled by COM.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [AUTOSAR Specifications](https://www.autosar.org/standards/classic-platform/)
- [Vector: AUTOSAR and ECU communication resources](https://www.vector.com/int/en/know-how/technologies/autosar/)

---
