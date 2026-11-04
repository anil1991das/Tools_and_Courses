---
title: "AUTOSAR COM Module Explained: Signals, I-PDUs, Packing, and Timing"
description: "Learn how the AUTOSAR COM module packs signals into I-PDUs, extracts received signals, and manages transmission modes and signal notifications."
slug: "autosar-com-module-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR COM"
  - "I-PDU"
  - "Signals"
  - "BSW"
relatedTools: []
relatedArticles:
  - "autosar-communication-stack-overview"
  - "autosar-pdur-explained"
draft: false
---

# AUTOSAR COM Module Explained: Signals, I-PDUs, Packing, and Timing

The AUTOSAR COM module provides signal-based communication services for application software and other BSW modules. It hides much of the low-level PDU handling from the application and supports configured signal packing, extraction, transmission, and reception behavior.

COM is commonly used for periodic and event-driven communication over networks such as CAN, LIN, and Ethernet, depending on the configured stack.

## What does COM do?

COM operates on configured I-PDUs and signals. Its responsibilities commonly include:

- Packing signals into I-PDUs for transmission.
- Extracting signals from received I-PDUs.
- Managing signal and I-PDU buffers.
- Triggering configured transmissions.
- Supporting signal groups and update bits.
- Handling configured timeout monitoring and notifications.
- Applying configured transfer properties such as invalid values or initial values where supported.

The exact feature set and behavior depend on the AUTOSAR release and vendor implementation.

## Signal and I-PDU relationship

An I-PDU can contain one or more signals.

For example, a vehicle-status I-PDU might contain:

| Signal | Example size | Purpose |
|---|---:|---|
| Vehicle speed | 16 bits | Current speed value |
| Ignition state | 2 bits | Ignition status |
| Door status | 4 bits | Door-open indicators |
| Alive counter | 4 bits | Message sequence supervision |

These sizes are illustrative. Real signal positions, ranges, scaling, and encoding are defined by the communication specification and ECU configuration.

COM uses generated or configured metadata to place signal values into their defined bit positions.

## Transmit behavior

An application or another configured producer updates a signal through its RTE interface or a BSW API.

COM updates the corresponding I-PDU buffer. Transmission may be initiated according to the configured I-PDU transmission mode, such as:

- Periodic transmission.
- Direct transmission following an event.
- Mixed transmission behavior.
- Minimum delay time or repetition behavior, where configured.

COM passes the I-PDU to PduR for routing. The lower communication stack then handles the relevant network-specific transmission.

## Receive behavior

When an I-PDU is received through the lower stack and routed to COM, COM extracts its configured signals and updates the receive buffers.

The application can obtain the updated values through RTE interfaces or configured notifications.

The receive behavior can include timeout monitoring. If an expected I-PDU does not arrive within the configured time, COM can notify an upper layer or apply configured timeout behavior.

## Signal groups

A signal group allows related signals to be handled as a logical group. It can provide a consistent access model for multiple signals that belong together in the same I-PDU context.

The exact access and buffering behavior depends on the configured COM model and generated RTE interfaces.

## Update bits and invalid values

Some communication specifications use update bits to indicate whether a signal or group has been updated. COM can be configured to manage such metadata.

An invalid value may be defined for a signal to represent that the data is unavailable or not valid. The meaning of the invalid value is part of the signal specification and application behavior.

Do not confuse an invalid value with a transport error. A frame can be received correctly while an individual signal carries an invalid or unavailable value.

## Endianness and bit positions

Signal layout defines where bits appear in the I-PDU. A mismatch in byte order, start bit, bit length, or signedness can produce incorrect values.

When a signal looks wrong, compare:

1. The source communication matrix or network description.
2. The generated COM configuration.
3. The bytes observed on the bus.
4. The signal extraction result at the application interface.

Use the conventions of the specific AUTOSAR toolchain and network description format. Bit-position conventions may differ between tools and formats.

## How COM interacts with PduR

COM is concerned with signals and I-PDUs. PduR routes I-PDUs between configured modules.

For a CAN transmit path, COM typically passes an I-PDU to PduR, which routes it toward CanIf. For a receive path, PduR routes an incoming I-PDU from CanIf to COM.

COM does not select the physical CAN transceiver or directly program the CAN controller.

## Common COM integration defects

### Incorrect signal start bit or length

The frame can appear on the bus while values are extracted incorrectly. Verify signal layout carefully.

### Incorrect transmission mode

A signal buffer may update without an I-PDU being transmitted at the expected time. Check periodic, direct, or mixed configuration and triggering behavior.

### Timeout configuration mismatch

A receiver may report a timeout when the sender's transmission cycle, timeout value, or network behavior is inconsistent.

### Wrong I-PDU routing

COM can create the correct I-PDU but still fail to reach the expected lower module if PduR or destination configuration is wrong.

### Stale application data

A received I-PDU can be updated while the application reads a different interface, group, or buffer. Verify the generated RTE mapping and access sequence.

## Testing COM behavior

A COM test plan should cover:

- Signal packing and extraction.
- Boundary values and signedness.
- Endianness and bit positions.
- Update-bit behavior.
- Invalid values.
- Tx modes and cycle timing.
- Receive timeout behavior.
- Signal group access.
- Notifications and confirmations.
- Integration with PduR and the lower communication interface.

## Frequently asked questions

### Does COM know the CAN identifier?

COM works with configured I-PDUs and signal mappings. Lower layers such as CanIf connect the configured PDU to the CAN controller and identifier.

### Does COM perform diagnostic segmentation?

No. Transport protocols such as CanTp handle segmentation for supported diagnostic communication paths.

### Can one I-PDU contain multiple signals?

Yes. An I-PDU commonly contains several configured signals that COM packs or extracts.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [AUTOSAR Specifications](https://www.autosar.org/standards/classic-platform/)
- [Vector: AUTOSAR Communication](https://www.vector.com/int/en/know-how/technologies/autosar/)

---
