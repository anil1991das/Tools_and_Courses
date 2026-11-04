---
title: "CAN vs CAN FD: Differences in Data Rate, Payload, and Frames"
description: "Compare Classical CAN and CAN FD, including payload size, bit-rate switching, frame structure, compatibility, and automotive use cases."
slug: "can-vs-can-fd-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "CAN"
  - "CAN FD"
  - "Automotive"
  - "Embedded Systems"
  - "Vehicle Networking"
relatedTools: []
relatedArticles: []
draft: false
---

# CAN vs CAN FD: Differences in Data Rate, Payload, and Frames

Controller Area Network (CAN) is widely used in vehicles and industrial embedded systems to allow electronic control units (ECUs) to exchange messages. As vehicle systems need to transfer more data, Classical CAN's payload limit can become restrictive.

CAN Flexible Data-Rate (CAN FD) extends the CAN protocol to support larger payloads and an optional faster data phase. It keeps important CAN concepts such as message-based communication and arbitration while improving data capacity.

## What is Classical CAN?

Classical CAN is a multi-master serial communication protocol. Nodes transmit frames on a shared bus, and message arbitration determines which frame wins when multiple nodes begin transmitting at the same time.

A Classical CAN data frame supports up to 8 data bytes.

CAN uses identifiers to represent message priority and meaning. Depending on the frame format, identifiers can be 11 bits (standard) or 29 bits (extended).

The identifier is not necessarily a unique sender address. CAN is message-oriented: nodes decide which messages to transmit and which identifiers to accept.

## What is CAN FD?

CAN FD extends the Classical CAN data frame format. It supports:

- Payload lengths up to 64 bytes.
- Optional bit-rate switching (BRS) for a faster data phase.
- A different CRC structure suitable for the larger payload.
- Compatibility considerations that require appropriate CAN FD-capable controllers and network design.

CAN FD retains the arbitration phase at the nominal bit rate. When bit-rate switching is enabled, the data phase can use a higher bit rate before returning to the nominal rate for the end of the frame.

## Classical CAN versus CAN FD

| Feature | Classical CAN | CAN FD |
|---|---|---|
| Maximum payload | 8 bytes | 64 bytes |
| Arbitration | CAN arbitration | CAN arbitration |
| Data-phase speed | Nominal bit rate | Can be faster with BRS |
| CRC | Classical CAN CRC format | Updated CRC handling |
| DLC interpretation | Up to 8 data bytes | Encodes payload lengths up to 64 bytes |
| Controller support | Classical CAN controller | CAN FD-capable controller required |
| Network compatibility | Classical CAN nodes | Requires careful coexistence and configuration |

The actual achievable bit rates depend on controller, transceiver, topology, cable length, network timing, and signal-integrity constraints.

## CAN FD frame structure

A CAN FD frame contains fields similar to a Classical CAN frame, but it includes control information that identifies the FD format and optional bit-rate switching.

Important CAN FD control bits include:

- **FDF:** identifies the frame as CAN FD.
- **BRS:** enables bit-rate switching for the data phase.
- **ESI:** indicates the error state of the transmitter.

CAN FD also uses a larger CRC field than Classical CAN, with the format depending on the payload length.

## Why can the CAN FD data phase be faster?

During arbitration, all nodes must agree on the winning message using the nominal timing. After arbitration has completed, CAN FD can switch to a faster bit rate for the data phase when BRS is enabled.

The faster phase reduces transfer time for the payload, but it imposes stricter timing and physical-layer requirements.

The nominal and data bit rates must be configured consistently with the network design and supported by all participating nodes.

## DLC and payload length

The Data Length Code (DLC) indicates the payload size, but CAN FD uses specific DLC values for payload lengths greater than 8 bytes.

For CAN FD, DLC values 9 through 15 map to payload lengths of 12, 16, 20, 24, 32, 48, and 64 bytes respectively.

This is important when interpreting raw CAN FD frames in test tools, bus analyzers, and software drivers.

## Compatibility considerations

A Classical CAN controller cannot correctly handle every CAN FD frame. In a mixed network, a Classical CAN node may interpret an FD frame as an error because it does not understand the FD format.

Network architecture must therefore consider controller capability, transceiver support, gateway behavior, and how Classical CAN and CAN FD segments are connected.

Do not assume that replacing one ECU with a CAN FD ECU automatically makes the entire bus CAN FD compatible.

## Where CAN FD is useful

CAN FD can be useful when an ECU needs to transfer more data per frame, such as:

- More signals or packed application data.
- Diagnostic or calibration data.
- Firmware update transport.
- Sensor and control information.
- Gateway communication between network segments.

It does not replace automotive Ethernet for every high-bandwidth use case. Network selection depends on latency, bandwidth, determinism, topology, cost, and system requirements.

## Testing CAN and CAN FD communication

A practical test setup should verify:

1. Nominal bit rate and sample-point configuration.
2. Data bit rate and BRS behavior where used.
3. Standard and extended identifiers.
4. Payload lengths across supported DLC values.
5. Correct CRC and error handling.
6. Bus load and timing under expected traffic.
7. Behavior when Classical CAN and CAN FD nodes coexist.
8. Error frames, bus-off handling, and recovery behavior.

Use a CAN interface and analyzer that support CAN FD, and ensure the selected transceiver and physical network are suitable for the configured bit rates.

## Common mistakes

### Assuming a higher data bit rate is always safe

The data phase is constrained by physical-layer characteristics and network timing. Validate signal integrity and configuration.

### Misinterpreting DLC

CAN FD DLC values 9 to 15 do not directly represent 9 to 15 bytes. They map to defined payload lengths.

### Connecting unsupported Classical CAN nodes without analysis

An FD frame may be seen as an error by a Classical CAN-only controller. Design mixed networks carefully.

### Treating CAN FD as a completely different network architecture

CAN FD extends the CAN frame format and retains arbitration concepts, but controller, transceiver, and timing requirements must be updated.

## Frequently asked questions

### Can CAN FD send 64 bytes in one frame?

Yes. CAN FD supports payloads up to 64 bytes.

### Does every CAN FD frame use bit-rate switching?

No. BRS is optional. A CAN FD frame can use the nominal bit rate for the data phase when BRS is not enabled.

### Can a Classical CAN analyzer decode CAN FD?

Only if the analyzer interface and software support CAN FD. A Classical CAN-only interface cannot reliably decode FD frames.

## Further reading

- [CiA: CAN FD](https://www.can-cia.org/can-knowledge/can-fd-the-basic-idea/)
- [Bosch: CAN FD protocol information](https://www.bosch-semiconductors.com/ip-modules/can-protocols/)
- [ISO 11898-1: Road vehicles — Controller area network](https://www.iso.org/standard/63648.html)

---
