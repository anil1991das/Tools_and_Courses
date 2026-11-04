---
title: "AUTOSAR CanTp Explained: ISO-TP Segmentation over CAN"
description: "Understand how AUTOSAR CanTp segments long diagnostic messages into CAN frames using ISO-TP Single, First, Consecutive, and Flow Control frames."
slug: "autosar-cantp-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR"
  - "CanTp"
  - "ISO-TP"
  - "UDS"
  - "CAN"
relatedTools: []
relatedArticles:
  - "uds-diagnostics-explained"
  - "autosar-pdur-explained"
draft: false
---

# AUTOSAR CanTp Explained: ISO-TP Segmentation over CAN

A CAN frame has a limited payload. Diagnostic messages and other protocol data can be longer than one CAN frame, so a transport protocol is needed to split the message into smaller pieces and reassemble it at the receiver.

The AUTOSAR CAN Transport Protocol (CanTp) implements the configured ISO-TP transport behavior over CAN. It is commonly used to carry UDS diagnostic messages.

## What does CanTp do?

CanTp provides segmentation and reassembly for messages that exceed the capacity of one CAN or CAN FD frame.

Its responsibilities include:

- Transmitting Single Frames for short messages.
- Starting multi-frame transmission with a First Frame.
- Sending Consecutive Frames.
- Receiving and processing Flow Control frames.
- Reassembling received messages.
- Managing configured timing and buffer behavior.
- Reporting transmission and reception status to upper layers.

The actual behavior is based on the configured transport protocol parameters and applicable ISO specification.

## ISO-TP frame types

### Single Frame (SF)

A Single Frame carries a complete transport payload in one CAN frame.

It is used when the message fits within the available payload for the configured addressing and frame format.

### First Frame (FF)

A First Frame begins a segmented transmission. It includes information about the total message length and the initial part of the payload.

The receiver uses this information to prepare for reassembly.

### Consecutive Frame (CF)

Consecutive Frames carry the remaining message data. They include a sequence number so the receiver can detect ordering problems.

### Flow Control Frame (FC)

The receiver sends a Flow Control frame to control the sender's transmission.

Important information includes:

- Flow Status.
- Block Size (BS).
- Minimum Separation Time (STmin).

The sender must follow the flow-control parameters and timing requirements.

## Multi-frame transmission example

A simplified sequence is:

~~~text
Sender                         Receiver
  |                                |
  | -------- First Frame --------> |
  |                                |
  | <------- Flow Control -------- |
  |                                |
  | ----- Consecutive Frame -----> |
  | ----- Consecutive Frame -----> |
  | ----- Consecutive Frame -----> |
  |                                |
~~~

The exact number of frames and flow-control exchanges depends on payload length, addressing format, block size, frame payload capacity, and configured timing.

## Block Size and STmin

Block Size controls how many Consecutive Frames the sender may transmit before it must wait for another Flow Control frame.

STmin specifies the minimum separation time between Consecutive Frames under the applicable ISO-TP rules.

These values are important for receiver buffer handling and bus timing. An incorrect configuration can lead to timeouts, dropped frames, or interoperability problems.

## CanTp and PduR

CanTp uses PduR for configured routing to and from the transport protocol.

For a diagnostic receive path, a simplified flow is:

~~~text
CAN Controller
      |
    CanIf
      |
    PduR
      |
    CanTp
      |
     Dcm
~~~

For transmission, the upper diagnostic module requests transport of a message. CanTp segments it and uses the lower interface path to send CAN frames.

The exact call sequence and generated routing configuration depend on the AUTOSAR stack implementation.

## CAN FD transport considerations

CAN FD increases the data field capacity of a single frame, which can reduce the number of transport frames needed for a message.

However, CAN FD support must be configured across the controller, CAN driver, CanIf, CanTp, transceiver, and network. The transport layer also needs the correct addressing and data-length configuration.

Do not assume that enabling CAN FD in one module is sufficient for end-to-end support.

## Common CanTp integration defects

### Incorrect addressing format

Physical and functional addressing, normal and extended addressing, and configured address bytes must be consistent between tester and ECU.

### Wrong Block Size or STmin interpretation

A sender and receiver must follow the transport parameters. Incorrect timing can cause the transfer to fail.

### Buffer size too small

The upper layer or transport configuration must support the expected diagnostic message length.

### Sequence number errors

The receiver expects Consecutive Frame sequence numbers to follow the protocol rules. Lost, repeated, or reordered frames can invalidate the transfer.

### P2 timing confused with transport timing

UDS application response timing and ISO-TP transport timing are different concepts. Both must be configured and tested correctly.

## Testing CanTp

A transport test plan should include:

- Single Frame transmission and reception.
- Multi-frame transmission and reassembly.
- Flow Control handling.
- Block Size and STmin variations.
- Sequence number validation.
- Addressing modes.
- Buffer exhaustion.
- Timeout and missing-frame cases.
- CAN FD payload configurations.
- Back-to-back transfers and recovery behavior.

Use a CAN analyzer or diagnostic tool capable of decoding ISO-TP frames.

## Frequently asked questions

### Is CanTp the same as UDS?

No. UDS defines diagnostic services at the application layer. CanTp transports longer UDS messages over CAN.

### Does CanTp use PduR?

Yes. AUTOSAR uses PduR to route transport PDUs between upper and lower communication modules.

### Can CanTp transfer more than 8 bytes?

Yes. That is one of its main purposes on Classical CAN. It segments a larger message across multiple CAN frames. CAN FD can carry more data per frame, but transport support is still configured.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [ISO 15765-2: Diagnostic communication over CAN](https://www.iso.org/standard/66574.html)
- [ISO 14229: Unified Diagnostic Services](https://www.iso.org/standard/72439.html)

---
