---
title: "AUTOSAR Communication Stack Explained: From Signals to CAN Hardware"
description: "Follow an AUTOSAR Classic communication path through COM, PduR, CanIf, CanDrv, and the CAN controller, with a practical integration checklist."
slug: "autosar-communication-stack-overview"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR Classic"
  - "Communication Stack"
  - "CAN"
  - "BSW"
relatedTools: []
relatedArticles:
  - "autosar-classic-architecture-guide"
  - "can-vs-can-fd-explained"
draft: false
---

# AUTOSAR Communication Stack Explained: From Signals to CAN Hardware

AUTOSAR Classic separates application communication from hardware-specific transmission. A signal produced by an application Software Component can pass through several Basic Software modules before it reaches a CAN controller.

Understanding this path helps when configuring PDUs, tracing communication defects, and deciding which module to inspect during integration.

## A simplified CAN communication path

A typical transmit path is:

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

This is a conceptual path. Exact modules and routing depend on the AUTOSAR release, vendor implementation, ECU configuration, and communication design.

The receive path generally traverses the relevant modules in the opposite direction.

## Role of each module

### RTE

The Runtime Environment provides communication between Software Components and between SWCs and BSW services. It implements generated interfaces and configured communication behavior.

### COM

COM handles signal-level communication. It packs signals into I-PDUs for transmission and extracts received signals from I-PDUs. It can also support configured signal groups, update bits, timeout monitoring, and other communication features.

### PduR

The PDU Router routes I-PDUs between configured modules. It connects communication modules such as COM, CanIf, CanTp, and other upper or lower layers according to the configured routing paths.

### CanIf

The CAN Interface provides a hardware-independent interface for upper layers. It manages configured CAN controller and PDU abstractions, and forwards transmit and receive requests between upper layers and the CAN driver.

### CanDrv

The CAN Driver is part of MCAL. It controls the microcontroller's CAN hardware through the vendor-specific driver implementation.

### CAN Controller and Transceiver

The CAN controller handles CAN protocol operations such as frame transmission, reception, arbitration, and error handling. The transceiver connects the controller's logic-level signals to the physical CAN bus.

## Signal, I-PDU, and CAN frame

These terms describe different layers:

- **Signal:** an application-level value, such as vehicle speed or a switch state.
- **I-PDU:** a configured communication unit containing one or more signals or protocol data.
- **CAN frame:** the data-link frame transmitted on the bus, with identifier, control information, payload, and error-checking fields.

One I-PDU may contain several signals. The COM module is responsible for mapping configured signals into the I-PDU layout.

## Transmit flow

A simplified transmit sequence is:

1. An SWC updates a signal through its RTE interface.
2. COM updates the corresponding signal value in the I-PDU buffer.
3. A configured transmit condition or call causes COM to request transmission.
4. PduR routes the I-PDU to CanIf.
5. CanIf maps the PDU to the configured CAN controller and hardware transmit object.
6. CanDrv programs or queues the frame for transmission.
7. The CAN controller arbitrates for the bus and transmits the frame.
8. Relevant confirmation callbacks propagate status according to the configured stack path.

The exact timing may be periodic, event-triggered, mixed, or controlled by application and BSW configuration.

## Receive flow

A simplified receive sequence is:

1. The CAN controller receives a frame.
2. CanDrv reports the received hardware object or frame.
3. CanIf performs configured filtering and identifies the relevant PDU.
4. PduR routes the I-PDU to the destination module.
5. COM extracts signals and updates the configured receive buffers.
6. RTE makes the received data available to the application according to the configured interface.

For diagnostic traffic, the receive path may involve CanTp and Dcm rather than COM.

## Configuration dependencies

Communication integration involves more than writing application code. Common configuration dependencies include:

- CAN controller and channel configuration.
- CAN identifiers and frame formats.
- Hardware transmit and receive objects.
- CanIf Tx and Rx PDU configuration.
- PduR routing paths.
- COM I-PDU and signal layout.
- Signal endianness, start bit, and length.
- Update-bit and timeout behavior.
- Tx mode and periodic timing.
- Notification and confirmation callbacks.

A mismatch between two configuration layers can prevent communication even when the individual modules compile successfully.

## Debugging a missing CAN message

A useful diagnostic sequence is:

1. Confirm that the application updates the signal.
2. Verify that the COM signal buffer changes.
3. Check whether the I-PDU is triggered for transmission.
4. Confirm the PduR routing path and destination PDU.
5. Check CanIf transmit return status and controller state.
6. Inspect the CAN driver and hardware transmit object.
7. Verify the controller, transceiver, termination, and bus.
8. Check the CAN analyzer for the expected identifier and payload.

For receive defects, follow the path from the physical bus toward the application and verify filtering, routing, and signal extraction.

## Common integration mistakes

### Mixing signal IDs and PDU IDs

A signal is not a CAN identifier. The signal belongs to an I-PDU, and the configured lower layer maps that PDU to a CAN frame identifier.

### Mismatched PDU references

A COM I-PDU, PduR route, CanIf PDU, and hardware object must be connected consistently.

### Incorrect signal layout

Wrong bit position, length, endianness, or update-bit configuration can produce incorrect application values even when the CAN frame is present.

### Ignoring transmit confirmation

A request accepted by an upper module does not necessarily mean the frame was successfully transmitted on the bus. Inspect the configured confirmation and controller status.

## Frequently asked questions

### Does COM directly access the CAN controller?

No. In a typical AUTOSAR Classic CAN path, COM uses PduR and CanIf before the CAN driver and hardware.

### Is PduR responsible for packing signals?

No. COM handles signal packing and extraction. PduR routes protocol data units between configured modules.

### Is every CAN message handled by COM?

No. Diagnostic communication and other protocol paths can use different modules, such as CanTp and Dcm.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [AUTOSAR Specifications](https://www.autosar.org/standards/classic-platform/)
- [Vector: AUTOSAR Classic Platform](https://www.vector.com/int/en/know-how/autosar/)

---
