---
title: "CAN Bus-Off Explained: Error Counters, Recovery, and ECU Diagnostics"
description: "Learn what CAN bus-off means, how error counters work, common causes, and how embedded software should handle recovery safely."
slug:"can-bus-off-error-handling-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "CAN"
  - "Bus-Off"
  - "Automotive"
  - "Error Handling"
  - "Embedded Systems"
relatedTools: []
relatedArticles:
  - "can-vs-can-fd-explained"
  - "autosar-canif-explained"
draft: false
---

# CAN Bus-Off Explained: Error Counters, Recovery, and ECU Diagnostics

CAN controllers monitor communication errors and adjust internal error counters as frames are transmitted and received. When a node accumulates enough transmit errors, it can enter the bus-off state and stop participating in normal bus communication.

Understanding bus-off is important for diagnosing wiring faults, timing mismatches, transceiver problems, and communication-stack behavior.

## What is CAN bus-off?

CAN defines error confinement states for nodes:

- Error Active.
- Error Passive.
- Bus-Off.

A node in Error Active can participate normally and may signal errors actively.

A node in Error Passive has exceeded an error-counter threshold and has more restricted error signaling behavior.

A Bus-Off node is prevented from transmitting normal CAN frames according to the protocol's error-confinement rules.

The state is a protective mechanism that helps prevent a persistently faulty node from disrupting the network.

## Transmit and receive error counters

CAN controllers maintain error counters used for error confinement. The Transmit Error Counter (TEC) and Receive Error Counter (REC) are adjusted according to protocol-defined conditions.

As errors accumulate, a node may transition from Error Active to Error Passive and then to Bus-Off.

The exact counter updates are specified by the CAN protocol. Do not treat every error event as a simple increment of both counters.

## Common causes of bus-off

Potential causes include:

- Incorrect nominal bit rate.
- Incorrect sample point or bit timing.
- Missing or incorrect bus termination.
- CAN_H and CAN_L wiring problems.
- Transceiver not enabled or in the wrong mode.
- Electrical noise or signal-integrity issues.
- A disconnected bus or faulty connector.
- Controller configuration mismatch.
- Faulty hardware or incorrect oscillator configuration.

A bus-off event is an observable symptom, not proof of one specific root cause.

## Diagnose bus-off systematically

Start by collecting the controller status and error counters, then inspect the physical network.

A useful sequence:

1. Record TEC, REC, and controller error state.
2. Capture the CAN bus using an analyzer.
3. Confirm bit rate and timing configuration.
4. Verify termination and cable topology.
5. Check transceiver supply, standby, and enable pins.
6. Inspect connector and wire integrity.
7. Compare behavior with known-good ECU hardware.
8. Review driver, CanIf, and network-management behavior.

Use an oscilloscope or suitable physical-layer analyzer when waveform quality is suspect. A software trace alone may not reveal electrical faults.

## Bus-off recovery

A CAN controller does not necessarily return to normal communication immediately after bus-off.

Recovery behavior is governed by the CAN protocol and may be coordinated by the driver and network-management configuration.

In AUTOSAR Classic, the CAN driver reports controller error-state changes through configured interfaces. CanIf and higher modules can react according to the configured bus-off notification and recovery strategy.

The application or network-management system should follow the OEM requirements for recovery, retries, and fault reporting.

## Why immediate repeated restart can be harmful

A faulty physical connection or timing configuration may cause the controller to re-enter Bus-Off immediately after recovery.

Repeated restart attempts can create unstable network behavior and hide the underlying problem.

Use bounded or policy-controlled recovery, record diagnostic information, and report persistent faults. The required retry policy is system-specific.

## AUTOSAR bus-off handling

A conceptual AUTOSAR Classic flow is:

~~~text
CAN Controller
     |
  CAN Driver
     |
 Bus-Off Notification
     |
    CanIf
     |
 Network Management / ECU Policy
     |
 Controller Recovery Request
~~~

Exact callbacks, state transitions, and recovery ownership depend on the AUTOSAR version and vendor integration.

Check the configuration for controller mode handling, bus-off notifications, recovery timing, and any network-management coordination.

## Testing bus-off behavior

An approved test plan may include:

- Controlled physical bus fault injection.
- Incorrect timing configuration in a laboratory setup.
- Controller error-state transition.
- TEC and REC monitoring.
- Bus-off notification propagation.
- Recovery timing and state transition.
- Persistent fault behavior.
- Diagnostic event reporting.
- Communication restoration after the fault is removed.

Do not inject faults onto a production vehicle network or safety-critical system without authorization and a controlled procedure.

## Common mistakes

### Assuming bus-off is always a software defect

Wiring, termination, transceiver power, bit timing, and electrical noise are frequent areas to inspect.

### Restarting without recording diagnostic evidence

Capture counters, controller state, and bus traces before resetting the controller.

### Confusing Error Passive with Bus-Off

They are different CAN error-confinement states and have different protocol behavior.

### Ignoring application-level recovery

Restoring the CAN controller does not necessarily restore higher-layer communication, network management, or application state automatically.

## Frequently asked questions

### Can a bus-off node receive CAN frames?

The node is restricted from normal bus participation while bus-off. Exact receive and controller behavior should be checked against the controller and implementation documentation.

### Does CAN FD change bus-off behavior?

CAN FD uses the CAN error-confinement concepts, but frame format and timing configuration differ. The relevant controller and protocol specification should be followed.

### Can a CAN analyzer identify the cause of bus-off?

It can help identify error frames, missing acknowledgements, traffic patterns, and bus timing. Electrical measurements may still be required to find the root cause.

## Further reading

- [ISO 11898-1: Road vehicles — Controller area network](https://www.iso.org/standard/63648.html)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [CiA: CAN knowledge](https://www.can-cia.org/can-knowledge/)

---
