---
title: "CAN Bus Arbitration and Error Handling Explained"
description: "Understand CAN identifier arbitration, dominant and recessive bits, error detection, error counters, error-passive state, and bus-off recovery."
slug: "can-bus-arbitration-and-error-handling"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "CAN"
  - "CAN Bus"
  - "Error Handling"
  - "Automotive Networking"
relatedTools: []
relatedArticles:
  - "can-vs-can-fd-explained"
  - "autosar-communication-stack-explained"
draft: false
---

# CAN Bus Arbitration and Error Handling Explained

Controller Area Network (CAN) is designed for communication among multiple nodes on a shared bus. Several nodes can attempt transmission at the same time, but the protocol provides a non-destructive arbitration mechanism that selects one frame without corrupting the winning message.

CAN also includes error detection and fault confinement mechanisms. These features help nodes detect communication problems and limit the effect of faulty transmitters.

## Dominant and recessive bits

CAN uses two logical bus states:

- Dominant bit: represented as logic 0.
- Recessive bit: represented as logic 1.

When a dominant and recessive bit are transmitted at the same time, the dominant state appears on the bus.

This wired-AND-like behavior enables bitwise arbitration. The electrical implementation depends on the CAN physical layer and transceiver.

## How arbitration works

When multiple nodes start transmitting, they monitor the bus while sending the arbitration field.

A node that transmits a recessive bit but reads a dominant bit loses arbitration. It stops transmitting and becomes a receiver, while the winning node continues.

This is non-destructive because the arbitration field is designed so that losing nodes do not corrupt the winning frame.

For standard identifiers with the same frame format, a numerically lower identifier generally has higher priority because it contains dominant bits earlier in the arbitration field.

Extended identifiers and the interaction between standard and extended frame formats have additional arbitration rules.

## Why identifier priority matters

CAN is message-oriented. The identifier is used for arbitration and message identification, not necessarily as a unique sender address.

A high-priority message should use an identifier that reflects the system's timing and network design requirements.

If a network carries too many high-priority messages, lower-priority messages can experience significant delay. Network design should consider worst-case response time and bus load, not just average utilization.

## CAN error detection

CAN defines several mechanisms to detect transmission errors, including:

- Bit monitoring.
- Bit stuffing checks.
- Frame checks.
- Acknowledgement checks.
- Cyclic redundancy check (CRC).

When a node detects an error, it transmits an error flag according to its error state and the protocol rules. The affected frame is not accepted as a valid frame and may be retransmitted.

## Error-active and error-passive states

CAN controllers track transmit and receive error counters.

A node can be in states such as:

- Error-active: it can transmit active error flags.
- Error-passive: it has exceeded an error threshold and uses passive error signaling.
- Bus-off: it is prevented from transmitting on the bus due to excessive transmit errors.

The controller maintains error counters according to protocol rules. Exact register visibility and recovery behavior depend on the CAN controller and driver.

## What is bus-off?

Bus-off is a fault-confinement state that prevents a node from disturbing network communication after excessive transmission errors.

A bus-off node must follow the applicable recovery rules and system design. Some controllers and software stacks support automatic recovery; others require explicit software handling.

Do not immediately force repeated recovery attempts without understanding the underlying issue. A faulty transceiver, wiring short, wrong bit timing, or incorrect network configuration may cause the controller to return to bus-off.

## Common causes of CAN errors

- Incorrect nominal bit rate or sample point.
- Missing or incorrect termination.
- Wiring faults or poor connector contact.
- Transceiver in standby or disabled mode.
- Excessive bus length or stubs for the selected bit timing.
- Electrical noise or grounding problems.
- Incompatible frame configuration.
- A node transmitting at an incorrect bit rate.

Check physical and configuration issues before assuming that an application payload is responsible.

## Termination and physical topology

A conventional high-speed CAN bus generally uses termination at the two physical ends of the main bus, commonly with 120-ohm resistors, subject to the network's physical-layer design.

The combined resistance measured across CAN_H and CAN_L with power removed is often around 60 ohms for a correctly terminated two-end network. This is a practical check, not a substitute for the vehicle wiring specification.

Avoid arbitrary star topologies and long stubs unless supported by the network design and bit timing.

## Practical debugging workflow

1. Confirm the configured bit rate and timing for all nodes.
2. Verify that the transceiver is enabled and powered.
3. Check physical wiring and termination.
4. Capture bus traffic with an appropriate analyzer.
5. Inspect error frames and controller error counters.
6. Determine whether one node or the whole network is affected.
7. Check bus-off and recovery handling in the driver and application.
8. Re-test under representative network load and environmental conditions.

A CAN analyzer's error-frame display can help localize a failure, but correlate it with controller status and electrical measurements.

## Common mistakes

### Treating arbitration loss as a bus error

Losing arbitration is normal. A node that loses arbitration retries when the bus becomes available.

### Assuming the lowest priority identifier transmits first

For the same arbitration format, lower numeric identifiers generally have higher priority. Check the exact standard/extended format interaction.

### Ignoring bus-off recovery policy

Recovery should follow the controller, AUTOSAR, and system requirements. Repeated uncontrolled restarts can hide a persistent fault.

### Blaming software before checking wiring

CAN communication depends on electrical termination, transceivers, cable topology, and timing configuration.

## Frequently asked questions

### Does CAN arbitration destroy the losing frame?

No. Arbitration is non-destructive. A node that loses arbitration stops transmitting and can retry later.

### Why does a CAN controller enter bus-off?

It enters bus-off after excessive transmit errors according to the CAN fault-confinement rules. The root cause may be electrical, timing-related, or due to network configuration.

### Does CAN ACK mean the intended ECU received the message?

No. The ACK slot indicates that at least one node correctly received the frame at the data-link level. It does not confirm that a particular application processed the message.

## Further reading

- [CiA: CAN Knowledge](https://www.can-cia.org/can-knowledge/)
- [Bosch CAN Protocol](https://www.bosch-semiconductors.com/ip-modules/can-protocols/)
- [ISO 11898-1: Controller Area Network](https://www.iso.org/standard/63648.html)

---
