---
title: "AUTOSAR CanIf and CanTp Explained: CAN Interface and Transport Protocol"
description: "Learn the roles of CanIf and CanTp in AUTOSAR Classic, how they interact with the CAN driver and PduR, and when segmented transport is required."
slug: "autosar-canif-cantp-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR"
  - "CanIf"
  - "CanTp"
  - "CAN"
  - "ISO-TP"
relatedTools: []
relatedArticles:
  - "autosar-communication-stack-explained"
  - "can-isotp-explained"
draft: false
---

# AUTOSAR CanIf and CanTp Explained: CAN Interface and Transport Protocol

AUTOSAR Classic separates CAN hardware access from upper communication services. CanIf provides a hardware-independent CAN interface, while CanTp transports messages that may be larger than one CAN frame.

These modules are often encountered together in ECU communication and diagnostic integration, but they solve different problems.

## What is CanIf?

CanIf is the CAN Interface module in AUTOSAR Classic. It provides an abstraction between upper communication modules and the CAN driver.

CanIf handles configured CAN controller and PDU-related interfaces. It participates in transmit requests, receive indications, transmit confirmations, controller mode handling, and other configured interactions.

Its purpose is to reduce the dependency of upper layers on a specific CAN controller driver.

## What is CanTp?

CanTp is the CAN Transport Protocol module. It supports transmission and reception of segmented messages over CAN according to the configured transport protocol.

A normal CAN data frame has a limited payload. When an application-layer message is larger than the available payload, transport segmentation divides it into multiple frames and reassembles it at the receiver.

For diagnostics, CanTp commonly transports UDS messages over CAN using ISO 15765-2 behavior.

## Typical diagnostic transmit path

A simplified diagnostic request path can look like:

~~~text
Diagnostic application / Dcm
             |
            PduR
             |
           CanTp
             |
           PduR
             |
           CanIf
             |
          CanDrv
             |
        CAN Controller
~~~

The precise routing calls and direction depend on whether the ECU is acting as a diagnostic server or client and on the configured AUTOSAR stack.

CanTp uses lower-layer CAN interface services to send and receive transport frames. PduR connects upper and lower modules according to the configured PDU routes.

## ISO-TP frame types

A common ISO-TP transfer uses these frame types:

- Single Frame (SF): carries a complete short message in one CAN frame.
- First Frame (FF): announces a message that needs segmentation and carries the first part.
- Consecutive Frame (CF): carries subsequent data segments.
- Flow Control (FC): tells the sender how to continue, including block-size and separation-time information where applicable.

The exact encoding depends on the addressing format and protocol version.

## Flow control and timing

The receiver can use a Flow Control frame to regulate how many Consecutive Frames the sender may transmit before waiting for additional permission.

Important parameters include:

- Block Size (BS).
- Separation Time (STmin).
- Transport timeouts.
- Addressing format.
- CAN frame format and configured data length.
- Buffer sizes and maximum message length.

An incorrect timing configuration can cause a transfer to fail even when the CAN bus is physically healthy.

## Single Frame versus segmented transfer

If a diagnostic message fits into the configured Single Frame capacity, it can be transmitted without segmentation.

Larger messages require First Frame, Flow Control, and Consecutive Frame handling.

CAN FD can carry larger data payloads than Classical CAN, but the transport-layer configuration still needs to match the ECU's supported frame format and protocol behavior.

## What CanIf configuration must match

Important CanIf configuration elements commonly include:

- CAN controller references.
- Hardware transmit and receive objects.
- Tx and Rx PDU mappings.
- CAN identifier and identifier format.
- Controller and transceiver modes.
- Notification references.
- PDU length and frame handling.

The controller and driver configuration must also support the intended CAN or CAN FD behavior.

## Debugging a failed diagnostic transfer

If a diagnostic request does not complete, separate the investigation into layers:

1. Confirm that the diagnostic service request is created correctly.
2. Verify Dcm and PduR routing.
3. Check whether CanTp starts the transfer.
4. Inspect Single Frame or First Frame transmission.
5. Verify that Flow Control is received and interpreted.
6. Check Block Size, STmin, and timeout values.
7. Inspect CanIf Tx and Rx paths.
8. Check CAN driver, controller, transceiver, and physical bus.
9. Verify the reassembled UDS payload and response timing.

A bus trace is essential, but it should be correlated with internal module callbacks and status information.

## Common mistakes

### Confusing CanIf with CanTp

CanIf provides the CAN hardware-independent interface. CanTp handles segmentation and reassembly for longer transport messages.

### Debugging only the UDS payload

A correct UDS service request can still fail because of ISO-TP addressing, flow control, timing, or PDU routing.

### Ignoring receive-side flow control

The sender needs valid flow control information when a segmented transfer requires it.

### Using incompatible frame configurations

Classical CAN and CAN FD have different frame capabilities. Verify that controller, driver, CanIf, and CanTp configurations agree.

### Assuming the same transport settings fit every ECU

OEM and ECU specifications define timing, addressing, message length, and supported diagnostic behavior.

## Frequently asked questions

### Does CanTp replace CanIf?

No. CanTp uses the lower CAN interface path. They have separate responsibilities.

### Is CanTp only used for UDS?

No. It can transport other configured protocol payloads that require segmentation over CAN, though UDS is a common automotive use case.

### CanTp transmit a message larger than one CAN frame?

Yes. It segments and reassembles messages using the configured transport protocol and timing parameters.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [ISO 15765-2: Diagnostic communication over CAN](https://www.iso.org/standard/66574.html)
- [ISO 14229: Unified Diagnostic Services](https://www.iso.org/standard/72439.html)

---
