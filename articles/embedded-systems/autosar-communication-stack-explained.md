---
title: "AUTOSAR Communication Stack Explained: From Signals to CAN Frames"
description: "Follow an AUTOSAR Classic communication path from application signals through COM, PduR, CanIf, and the CAN driver to the bus."
slug: "autosar-communication-stack-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR Classic"
  - "Communication Stack"
  - "CAN"
  - "BSW"
  - "Embedded Software"
relatedTools: []
relatedArticles:
  - "autosar-classic-architecture-guide"
  - "can-vs-can-fd-explained"
draft: false
---

# AUTOSAR Communication Stack Explained: From Signals to CAN Frames

In an automotive ECU, application software usually works with signals and software interfaces rather than directly writing CAN controller registers. AUTOSAR Classic provides standardized modules that move communication data between application software and the vehicle network.

Understanding this path makes it easier to trace missing signals, incorrect payloads, unexpected transmission timing, and receive-side integration problems.

## A simplified transmit path

A typical AUTOSAR Classic CAN transmit path can be represented as:

~~~text
Application SWC
      |
     RTE
      |
     COM
      |
    PduR
      |
   CanIf
      |
   CanDrv
      |
 CAN Controller
      |
 CAN Transceiver
      |
     CAN Bus
~~~

The exact path depends on the ECU configuration and communication type. Diagnostic transport, network management, and other communication mechanisms may use additional modules.

## Application software and RTE

The application layer contains Software Components (SWCs). An SWC accesses communication data through ports and interfaces defined in the AUTOSAR description.

The Runtime Environment (RTE) provides generated interfaces that connect SWCs to one another and to BSW services.

For signal-based communication, an application runnable may write a sender-receiver data element. The RTE maps that operation to the configured communication infrastructure.

The application should not assume that a write operation immediately causes a CAN frame to be transmitted. COM transmission modes, update bits, filtering, timing, and PDU configuration affect when data reaches the bus.

## COM: signal and I-PDU handling

The AUTOSAR COM module provides signal-based communication services. It packs application signals into I-PDUs for transmission and extracts received signal values from I-PDUs.

Important configuration concepts include:

- Signal length and start bit.
- Signal byte order and bit position.
- Initial values and invalid values.
- Update-bit configuration.
- I-PDU grouping and activation.
- Transmission mode and repetition behavior.
- Reception timeout and invalidation behavior.

A signal can be correctly written by an SWC yet still be absent from a CAN frame if its I-PDU is inactive, its transmission condition is not met, or its routing and lower-layer configuration is incorrect.

## PduR: routing protocol data units

The PDU Router (PduR) routes I-PDUs between upper and lower communication modules according to configuration.

It can connect COM to CanIf for normal signal communication and route diagnostic or transport protocol PDUs between relevant modules.

PduR is configuration-driven. The source PDU, destination PDU, module interfaces, and routing paths must be consistent.

When troubleshooting, inspect generated PDU identifiers and routing configuration rather than assuming that a PDU's name alone determines its runtime path.

## CanIf: hardware-independent CAN interface

CanIf provides a standardized interface between upper communication modules and the CAN driver.

It handles configured CAN transmit and receive objects, controller and transceiver-related interfaces, PDU identifiers, and notification paths according to the implementation and configuration.

CanIf helps upper layers avoid direct dependency on a specific CAN controller driver.

Important items to verify include:

- Controller and hardware object mapping.
- Tx and Rx PDU configuration.
- CAN identifier and frame format.
- DLC and payload handling.
- Controller mode and transceiver mode.
- Tx confirmation and Rx indication callbacks.

## CanDrv and hardware

The CAN driver is part of the microcontroller abstraction. It interacts with the CAN controller registers or controller IP through the vendor-specific implementation.

The controller handles low-level frame transmission, reception, arbitration, and error-state behavior according to its hardware capabilities.

A transceiver converts the controller's logic-level interface to the physical CAN bus signals. Incorrect transceiver enable, standby, or power configuration can prevent communication even when software has configured the CAN controller correctly.

## Receive path

A receive path generally moves in the reverse direction:

~~~text
CAN Bus
   |
CAN Transceiver
   |
CAN Controller
   |
  CanDrv
   |
 CanIf
   |
  PduR
   |
  COM
   |
  RTE
   |
Application SWC
~~~

The driver detects a received frame and reports it through the configured interface. CanIf identifies the configured receive PDU, PduR routes it, and COM extracts signals. The RTE makes the resulting data available to the application according to the configured interface.

A received frame can be visible in a bus analyzer but not reach an SWC because of filtering, incorrect CAN IDs, missing Rx PDU configuration, PDU routing, signal mapping, or timeout behavior.

## A practical debugging sequence

When a transmit signal is missing:

1. Confirm that the SWC runnable executes and writes the expected value.
2. Check the RTE interface and generated mapping.
3. Inspect the COM signal value and I-PDU activation state.
4. Verify COM transmission mode, trigger conditions, and repetition settings.
5. Confirm PduR routing and destination PDU identifiers.
6. Check CanIf Tx PDU and hardware object configuration.
7. Inspect CAN controller mode, driver status, and transceiver state.
8. Verify the frame on the physical bus with a suitable analyzer.

For receive problems, begin at the bus and follow the path upward. Confirm the frame ID, frame format, DLC, payload, and receive hardware object before examining COM signal extraction.

## Common integration mistakes

### Mismatched PDU identifiers

Generated module configuration must agree on the PDU mapping. An incorrect identifier can break the path even when the signal definition looks correct.

### Incorrect signal packing

Signal start bit, length, byte order, and data type must match the network description and peer ECU expectations.

### Assuming application writes are immediate transmissions

COM transmission behavior is configured. A signal update may be sent periodically, on change, on request, or according to another configured mode.

### Ignoring hardware state

A correctly configured software stack cannot transmit if the controller is stopped, the transceiver is in standby, or the physical bus is faulty.

### Debugging only one layer

Use trace points and status information across the complete path. A failure at one module can look like a problem in another layer.

## Frequently asked questions

### Does COM send CAN frames directly?

No. In a typical AUTOSAR Classic CAN path, COM handles signals and I-PDUs, while PduR and CanIf route data toward the CAN driver.

### Where is CanTp used?

CanTp transports segmented diagnostic or other transport-protocol messages over CAN. It is not normally needed for a standard short COM signal PDU.

### Is every AUTOSAR communication stack identical?

No. The architecture is standardized, but module versions, configuration, hardware mapping, vendor implementation, and OEM requirements vary.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [AUTOSAR Specifications](https://www.autosar.org/standards/classic-platform/)
- [ISO 11898: Controller Area Network](https://www.iso.org/standard/63648.html)

---
