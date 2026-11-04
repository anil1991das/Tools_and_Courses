---
title: "XCP Protocol Explained: Measurement, Calibration, and DAQ"
description: "Learn how the Universal Measurement and Calibration Protocol supports ECU measurement and calibration, including CONNECT, DAQ, and calibration workflows."
slug: "xcp-protocol-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "XCP"
  - "ASAM"
  - "ECU Calibration"
  - "Automotive"
  - "Embedded Software"
relatedTools: []
relatedArticles:
  - "uds-diagnostics-explained"
  - "autosar-communication-stack-overview"
draft: false
---

# XCP Protocol Explained: Measurement, Calibration, and DAQ

Automotive development requires engineers to observe internal ECU variables and tune calibration parameters. The Universal Measurement and Calibration Protocol (XCP) provides a standardized way to access measurement and calibration data from an ECU through a configured transport layer.

XCP is commonly used during development, test, and calibration workflows. It is not the same as UDS diagnostics, even though both may use the same physical network.

## What is XCP?

XCP is an ASAM standard for measurement and calibration. Its design separates the protocol's application layer from the transport layer.

Depending on the ECU and tool configuration, XCP can operate over transports such as:

- CAN.
- Ethernet.
- FlexRay in supported environments.
- Other standardized or vendor-supported transport mappings.

The available transport and feature set depend on the implementation and ECU description.

## Measurement and calibration

Measurement reads selected internal ECU variables. Calibration changes supported parameters that influence software behavior.

Examples include:

- Sensor values.
- Control-loop variables.
- State-machine values.
- Timing measurements.
- Calibration maps and constants.
- Development or validation counters.

Access is controlled by the ECU implementation and security configuration. Not every internal variable or memory location is exposed.

## XCP master and slave

A measurement and calibration tool acts as the XCP master. The ECU provides the XCP slave implementation.

The master establishes a connection, negotiates supported capabilities, and requests measurement or calibration operations.

A typical connection sequence may include:

1. CONNECT.
2. Communication parameter negotiation.
3. Memory or address information handling.
4. DAQ list configuration for measurement.
5. Calibration access where supported.

Exact commands and sequence depend on the XCP version, transport, and ECU implementation.

## What is DAQ?

Data Acquisition (DAQ) allows an XCP master to configure the ECU to transmit selected measurement data.

A configured DAQ list can contain Object Descriptor Tables (ODTs), which describe the data organization for transmission.

The exact DAQ configuration and timestamp behavior depend on the protocol implementation and transport constraints.

DAQ can be useful for collecting fast-changing ECU variables with less repeated request overhead than polling every value individually.

## Polling versus DAQ

Polling requests a value from the ECU when the tool needs it. DAQ configures the ECU to send selected measurement data according to defined events or acquisition settings.

| Polling | DAQ |
|---|---|
| Master requests values | ECU transmits configured data |
| Suitable for occasional measurements | Useful for recurring or event-driven measurement |
| Repeated request overhead | Can group several values in data packets |
| Timing depends on request/response cycle | Timing depends on event and DAQ configuration |

Both methods have use cases. DAQ capacity is limited by ECU resources, transport bandwidth, event rates, and configuration.

## Calibration access

XCP calibration commands can read and write supported ECU memory areas according to the implementation and access permissions.

Calibration data structures and conversion information are commonly described using A2L files.

An A2L file describes measurement and calibration objects, addresses, data types, conversion methods, and other metadata needed by calibration tools.

The A2L description must match the actual software build and memory layout. A mismatch can lead to incorrect values or unsafe write operations.

## XCP on CAN and Ethernet

XCP uses transport-specific packet handling.

XCP on CAN maps protocol packets onto CAN frames and must account for CAN payload size and bus loading.

XCP on Ethernet uses Ethernet transport and can support higher data throughput, subject to the network and ECU implementation.

Selecting a transport depends on measurement bandwidth, timing, network availability, and tool support.

## XCP versus UDS

| XCP | UDS |
|---|---|
| Focus on measurement and calibration | Focus on vehicle diagnostics and service procedures |
| Commonly used in development and calibration | Commonly used in diagnostics, maintenance, and programming workflows |
| Uses A2L descriptions for measurement/calibration objects | Uses DIDs, routines, DTCs, and diagnostic services |
| DAQ supports configured measurement acquisition | Diagnostic requests generally follow UDS service semantics |

They solve different problems and may coexist on the same ECU.

## Security and operational considerations

- Restrict XCP access to approved tools and networks.
- Use access protection or security mechanisms supported by the implementation.
- Control write access to calibration memory.
- Validate A2L and software version matching.
- Monitor bus load and acquisition rate.
- Avoid calibration writes on production or safety-critical systems without an approved procedure.
- Store measurement files according to project confidentiality and data-handling rules.

## Common XCP integration problems

### Incorrect A2L file

A wrong address, data type, or conversion method can produce misleading measurements.

### DAQ bandwidth exceeded

Too many variables or a high event rate can exceed available transport capacity and create packet loss or timing issues.

### Unsupported calibration access

The ECU may restrict memory ranges, write access, or calibration features.

### Transport mismatch

The tool and ECU must agree on the transport mapping and communication configuration.

## Frequently asked questions

### Is XCP a diagnostic protocol?

XCP is primarily used for measurement and calibration. UDS is the common diagnostic service protocol.

### What is an A2L file?

An A2L file describes measurement and calibration objects and their memory and conversion information for compatible tools.

### Can XCP run over CAN?

Yes. XCP on CAN is a common transport mapping, although the available bandwidth and DAQ capacity are constrained by the CAN network.

## Further reading

- [ASAM MCD-1 XCP](https://www.asam.net/standards/detail/mcd-1-xcp/)
- [ASAM MCD-2 MC (A2L)](https://www.asam.net/standards/detail/mcd-2-mc/)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)

---
