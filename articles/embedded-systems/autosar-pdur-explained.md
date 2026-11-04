---
title: "AUTOSAR PduR Explained: Routing I-PDUs Between Communication Modules"
description: "Understand the role of the AUTOSAR PDU Router, routing paths, transmit and receive direction, gateway behavior, and common configuration issues."
slug: "autosar-pdur-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR"
  - "PduR"
  - "I-PDU"
  - "Communication Stack"
relatedTools: []
relatedArticles:
  - "autosar-communication-stack-overview"
  - "autosar-com-module-explained"
draft: false
---

# AUTOSAR PduR Explained: Routing I-PDUs Between Communication Modules

The PDU Router (PduR) is an important module in the AUTOSAR Classic communication stack. It routes protocol data units between configured upper and lower communication modules.

PduR helps separate communication services from network-specific interfaces. It also supports configured routing between communication paths and, in suitable configurations, gateway communication.

## What is a PDU?

A Protocol Data Unit (PDU) is a data unit exchanged between protocol layers. In AUTOSAR communication, an I-PDU can contain application signals or protocol data.

Different modules use different PDU abstractions. COM handles signal-oriented I-PDUs, while transport protocols handle segmented or transport-layer communication.

A PDU reference in configuration is not the same thing as a CAN identifier. Lower layers map configured PDUs to network-specific transmission resources.

## Role of PduR

PduR provides configured routing between modules such as:

- COM.
- CanIf.
- CanTp.
- LinIf.
- FrIf.
- SoAd.
- Other supported communication modules according to the stack configuration.

The actual module connections depend on the ECU architecture and AUTOSAR release.

PduR normally uses generated routing configuration. It does not decide application meaning or perform signal packing.

## Transmit routing

A simplified CAN transmit route can be:

~~~text
COM
 |
PduR
 |
CanIf
 |
Can Driver
~~~

COM requests transmission of an I-PDU. PduR identifies the configured destination and forwards the request to CanIf.

For diagnostic transport, the source and destination modules may differ. For example, an upper diagnostic module can use PduR to route a transport PDU toward CanTp.

## Receive routing

A receive path routes an incoming PDU from the lower interface toward the appropriate upper module.

A simplified signal receive route is:

~~~text
CAN Driver
 |
CanIf
 |
PduR
 |
COM
~~~

For a diagnostic PDU, PduR may route the received data to CanTp rather than COM.

Correct routing depends on configured source PDU identifiers, destination module references, and the route direction.

## Gateway routing

Some ECUs route communication between network channels. PduR can support configured gateway behavior, where a received I-PDU is forwarded to another network interface or destination module.

Gateway designs need careful consideration of:

- Source and destination PDU configuration.
- Buffering and routing type.
- Timing and transmission confirmation.
- Network availability.
- PDU length and format compatibility.
- Routing activation or filtering where supported.
- Error and timeout behavior.

The precise support depends on the AUTOSAR release and vendor implementation.

## Routing configuration concepts

Common configuration concepts include:

- Source module and source PDU.
- Destination module and destination PDU.
- Routing path or routing path group.
- Communication interface versus transport protocol routing.
- Trigger transmit and transmit confirmation behavior.
- Buffering and gateway properties.

Configuration tools generate the PduR tables and related code. Manually editing generated routing code is generally not a substitute for correcting the source configuration.

## Debugging a PDU that does not reach its destination

A systematic sequence is:

1. Verify that the source module issues the expected request or receives the PDU.
2. Confirm the source PDU identifier and module configuration.
3. Check that the PduR routing path exists and is enabled as expected.
4. Verify the destination PDU identifier and module reference.
5. Inspect return values and configured callbacks.
6. Check the destination module's state and transmit resources.
7. Trace the PDU at the next lower or upper layer.

Use module trace, generated configuration, and communication analyzer data together. A PduR routing issue can look like a driver problem if the investigation starts too low in the stack.

## Common configuration mistakes

### Confusing source and destination IDs

PDU identifiers can be local to modules. Confirm the correct source and destination references rather than assuming all modules share one identifier namespace.

### Routing a diagnostic PDU to COM

Diagnostic messages generally use the transport and diagnostic path, not the application signal path. Verify the intended upper module.

### Missing transmit confirmation

A request can be routed successfully but fail later. Confirm that the lower module's completion status is propagated as expected.

### Inconsistent PDU lengths

The PDU length and destination configuration must be compatible. A mismatch can produce rejection, truncation, or integration errors.

## Testing PduR

Validate:

- Each configured routing path.
- Correct source-to-destination mapping.
- Tx and Rx direction.
- Gateway forwarding.
- PDU length and buffer behavior.
- Confirmation and indication callbacks.
- Error handling when destinations are unavailable.
- Routing group or activation behavior where applicable.

## Frequently asked questions

### Does PduR modify signal values?

No. Signal packing and extraction are handled by modules such as COM. PduR's main role is routing PDUs.

### Is PduR only used for CAN?

No. It can connect several configured communication modules. The available paths depend on the ECU configuration and AUTOSAR release.

### Does PduR decide the CAN ID?

No. The CAN interface configuration and lower driver mapping determine how a PDU is associated with a CAN controller transmission resource and identifier.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [AUTOSAR Specifications](https://www.autosar.org/standards/classic-platform/)
- [Vector: AUTOSAR Basic Software](https://www.vector.com/int/en/know-how/technologies/autosar/)

---
