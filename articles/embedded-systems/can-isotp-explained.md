---
title: "CAN ISO-TP Explained: Single Frames, Segmentation, and Flow Control"
description: "Understand ISO-TP transport over CAN, frame types, addressing, flow control, timing parameters, and common diagnostic communication failures."
slug: "can-isotp-explained"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "ISO-TP"
  - "CAN"
  - "UDS"
  - "Automotive Diagnostics"
relatedTools: []
relatedArticles:
  - "uds-diagnostics-explained"
  - "autosar-canif-cantp-explained"
draft: false
---

# CAN ISO-TP Explained: Single Frames, Segmentation, and Flow Control

CAN is a frame-based communication protocol with a limited payload per data frame. Automotive diagnostic messages can be larger than one CAN frame, so a transport protocol is needed to split a message into multiple frames and reconstruct it at the receiver.

ISO-TP, standardized in ISO 15765-2, provides this transport mechanism. It is widely used to carry Unified Diagnostic Services (UDS) messages over CAN.

## Why ISO-TP is needed

A diagnostic request may contain only a few bytes and fit inside one CAN frame. Other operations, such as reading a large data record or transferring software, may require a much larger payload.

ISO-TP provides:

- Segmentation of longer messages.
- Reassembly at the receiving node.
- Flow control between sender and receiver.
- Addressing options.
- Timing and error-handling rules.

ISO-TP transports the application payload. It does not define the diagnostic meaning of a UDS service.

## ISO-TP frame types

The protocol uses four primary PCI frame types. The Protocol Control Information (PCI) identifies the frame type and carries transport metadata.

### Single Frame (SF)

A Single Frame carries a complete transport payload in one CAN frame. It is used when the payload fits the configured single-frame capacity.

### First Frame (FF)

A First Frame announces a message that requires segmentation. It carries the total message length and the initial portion of the payload.

### Consecutive Frame (CF)

Consecutive Frames carry the remaining payload bytes. The receiver uses sequence information to detect ordering and missing frames.

### Flow Control (FC)

A Flow Control frame is sent by the receiver to regulate the sender's transmission of Consecutive Frames. It can communicate a block size and minimum separation time.

## Segmented transfer sequence

A typical multi-frame transfer follows this sequence:

~~~text
Sender                         Receiver
  |                                |
  |---------- First Frame -------->|
  |<--------- Flow Control --------|
  |------- Consecutive Frame ----->|
  |------- Consecutive Frame ----->|
  |<--------- Flow Control --------|  (when required)
  |------- Consecutive Frame ----->|
  |                                |
~~~

The exact number of frames depends on the message length, CAN frame format, addressing mode, and flow-control settings.

## Block Size and STmin

Two important Flow Control values are:

- **Block Size (BS):** the number of Consecutive Frames that may be sent before the sender must wait for another Flow Control frame. A value of zero indicates that no additional Flow Control frame is required for the remaining block under the applicable protocol rules.
- **STmin:** the minimum separation time between Consecutive Frames.

STmin values have protocol-defined encodings. Implementations must interpret them according to the selected ISO-TP version and supported timing behavior.

Incorrect BS or STmin handling can lead to transfer failures, timing violations, or buffer overruns.

## Addressing formats

ISO-TP supports addressing formats such as normal addressing, extended addressing, and mixed addressing, with specific identifier and address-byte behavior.

The sender and receiver must agree on:

- CAN identifier.
- Standard or extended CAN identifier format.
- Addressing mode.
- Transmit and receive addressing information.
- Classical CAN or CAN FD frame configuration.
- Maximum supported message length.

A correct UDS payload sent with the wrong addressing configuration will not be interpreted as the intended message.

## ISO-TP over CAN FD

CAN FD supports larger data payloads than Classical CAN. ISO-TP implementations can use CAN FD frame capabilities when supported and configured.

The transport implementation must still agree on frame format, data length, addressing, and timing. Do not assume that enabling CAN FD in the driver automatically makes all ISO-TP configuration correct.

## Timing and timeout handling

ISO-TP defines timing requirements for transport operations. Common timeout concepts include waiting for a Flow Control frame and waiting for Consecutive Frames.

The names and parameter configuration depend on the applicable ISO version and AUTOSAR implementation.

When debugging a timeout, capture both directions of the bus traffic and correlate them with the transport-layer state. A missing Flow Control frame, an incorrect separation time, or a receiver buffer problem can all produce a failed transfer.

## Common causes of ISO-TP failure

- Incorrect CAN identifiers or addressing mode.
- Sender and receiver using different frame formats.
- Receiver not sending Flow Control.
- Unsupported or incorrectly configured STmin.
- Incorrect block-size handling.
- Sequence-number mismatch.
- Transport timeout.
- Insufficient buffer size.
- CAN controller or transceiver errors.
- Application not accepting the reassembled message.

## Debugging checklist

1. Verify physical CAN communication and bit timing.
2. Confirm request and response identifiers.
3. Check standard versus extended addressing.
4. Identify the Single Frame or First Frame.
5. Inspect Flow Control status and parameters.
6. Verify Consecutive Frame sequence numbers.
7. Check transport timeouts and buffers.
8. Compare the reassembled payload with the intended UDS message.
9. Review the diagnostic application response timing.

A bus analyzer provides evidence about what actually appeared on the network. Internal transport traces help identify whether the software accepted and processed each frame.

## Frequently asked questions

### Is ISO-TP the same as UDS?

No. ISO-TP provides transport over CAN. UDS defines diagnostic application services such as ReadDataByIdentifier and ReadDTCInformation.

### Does every UDS request require multiple frames?

No. Short requests and responses can use Single Frames. Segmentation is required only when the payload exceeds the configured single-frame capacity.

### Can ISO-TP be used with CAN FD?

Yes, where the implementation and configuration support CAN FD. The frame format, payload length, and transport behavior must be configured consistently.

## Further reading

- [ISO 15765-2: Diagnostic communication over CAN](https://www.iso.org/standard/66574.html)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [Linux Kernel: ISO-TP](https://docs.kernel.org/networking/iso15765-2.html)

---
