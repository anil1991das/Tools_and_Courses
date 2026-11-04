---
title: "AUTOSAR CanIf Explained: CAN Interface, PDU Mapping, and Controller Modes"
description: "Learn how CanIf connects upper communication modules to the CAN driver, maps PDUs, handles controller modes, and reports receive and transmit events."
slug: "autosar-canif-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR"
  - "CanIf"
  - "CAN"
  - "MCAL"
  - "BSW"
relatedTools: []
relatedArticles:
  - "autosar-communication-stack-overview"
  - "autosar-pdur-explained"
draft: false
---

# AUTOSAR CanIf Explained: CAN Interface, PDU Mapping, and Controller Modes

The CAN Interface (CanIf) is the abstraction layer between upper communication modules and the CAN driver. It provides a hardware-independent interface for transmitting and receiving CAN frames and managing configured controller and PDU resources.

CanIf is a key integration point when an AUTOSAR ECU communicates over Classical CAN or CAN FD.

## What does CanIf do?

CanIf commonly handles:

- Transmit and receive PDU abstraction.
- Mapping configured PDU handles to CAN hardware resources.
- Controller mode management interfaces.
- Transceiver-related interface coordination where configured.
- Notification of transmit confirmation and receive indication.
- Filtering and dispatching received frames according to configuration.
- Error and controller status reporting through configured callbacks.

The exact responsibilities and supported features depend on AUTOSAR release, MCAL, and vendor configuration.

## CanIf in the communication stack

A simplified signal transmit path is:

~~~text
COM
 |
PduR
 |
CanIf
 |
Can Driver (MCAL)
 |
CAN Controller
~~~

On reception, the lower driver reports received data to CanIf. CanIf identifies the configured receive PDU and notifies the upper layer, often through PduR.

## PDU mapping

Upper modules use configured PDU identifiers or handles. CanIf maps these to lower-layer CAN hardware objects and controller resources.

Configuration commonly includes:

- CAN controller reference.
- CAN ID and frame format.
- Transmit or receive direction.
- Hardware object reference.
- PDU length and CAN FD properties where applicable.
- Upper-layer callback or handle.
- Filtering configuration.

The configured CAN ID is distinct from the PDU handle used by upper software modules.

## Controller modes

CanIf provides interfaces for controlling the CAN controller mode through the CAN driver, such as starting, stopping, or entering a sleep-related mode where supported by the stack and hardware.

The state transition must be coordinated with network management and ECU state behavior.

If the controller is stopped or not started, a transmit request may not behave as expected. Always inspect the current controller state and lower-driver return status.

## Transmit flow

A typical transmit operation includes:

1. An upper module requests transmission using a configured PDU handle.
2. CanIf resolves the configured transmit PDU.
3. CanIf maps the request to a controller and hardware transmit object.
4. CanIf invokes the CAN driver's transmit service.
5. The controller attempts to transmit the frame.
6. A transmit confirmation is reported through the configured callback path.

A successful API return may indicate that a request was accepted by the lower layer, not necessarily that the frame has completed on the physical bus.

## Receive flow

A typical receive sequence includes:

1. The CAN controller receives a frame.
2. The CAN driver obtains the frame or hardware object information.
3. CanIf checks configured controller, hardware object, and receive PDU mapping.
4. The configured upper-layer indication is called.
5. PduR or another configured recipient forwards the PDU to the destination.

Hardware filtering and software filtering behavior depend on the controller and driver configuration.

## CAN FD considerations

For CAN FD, CanIf configuration and the lower driver must support the FD frame format and required payload lengths. The controller, transceiver, network timing, and analyzer must also be compatible.

A configuration mismatch between the PDU's frame type, controller mode, and driver capabilities can prevent correct transmission or reception.

## Common CanIf debugging checks

- Is the correct controller configured?
- Is the controller started?
- Does the transmit PDU handle map to the intended CAN ID?
- Is the correct hardware transmit object assigned?
- Is the PDU length valid for Classical CAN or CAN FD?
- Does the driver accept the transmit request?
- Is the transmit confirmation callback reached?
- Does the receive filter accept the expected CAN ID?
- Are controller and transceiver modes correct?

## Common mistakes

### Confusing CanIf PDU handles with CAN identifiers

The upper-layer handle is used in the software interface. The CAN identifier is the frame identifier configured for bus communication.

### Assuming transmit accepted means transmitted

Check the completion confirmation and the actual bus trace.

### Incorrect controller reference

A valid PDU can be mapped to the wrong CAN controller or hardware object.

### Ignoring FD configuration

CAN FD requires compatible configuration across the stack and physical network.

## Frequently asked questions

### Is CanIf part of MCAL?

No. CanIf is an ECU abstraction communication module above the MCAL CAN Driver.

### Does CanIf pack application signals?

No. COM handles signals and I-PDUs. CanIf handles the CAN interface abstraction.

### Does CanIf communicate with the transceiver?

It may use configured CAN transceiver interface services and driver integration, depending on the architecture. The physical transceiver control is handled through the relevant driver and hardware.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [AUTOSAR Specifications](https://www.autosar.org/standards/classic-platform/)
- [Vector: CAN and AUTOSAR](https://www.vector.com/int/en/know-how/technologies/autosar/)

---
