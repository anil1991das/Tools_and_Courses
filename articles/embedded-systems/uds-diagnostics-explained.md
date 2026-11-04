---
title: "UDS Diagnostics Explained: Common Services and Diagnostic Sessions"
description: "Understand Unified Diagnostic Services, common UDS service identifiers, diagnostic sessions, negative responses, and the role of ISO-TP."
slug: "uds-diagnostics-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "UDS"
  - "ISO 14229"
  - "Automotive Diagnostics"
  - "CAN"
  - "Embedded Software"
relatedTools: []
relatedArticles: []
draft: false
---

# UDS Diagnostics Explained: Common Services and Diagnostic Sessions

Modern vehicles contain many electronic control units. Diagnostic tools need a standardized way to request information, read fault codes, change diagnostic modes, run tests, and support service procedures.

Unified Diagnostic Services (UDS) defines diagnostic communication services used in road vehicles. It is standardized by ISO 14229 and is commonly transported over CAN using ISO-TP, although UDS can also use other transport and network layers.

## What is UDS?

UDS is an application-layer diagnostic protocol. It defines services that a diagnostic client can request from an ECU, and the ECU's responses.

A diagnostic tester acts as the client. The vehicle ECU acts as the server for diagnostic requests.

UDS services are identified by Service Identifiers (SIDs). The SID indicates the requested service, such as DiagnosticSessionControl or ReadDataByIdentifier.

## Common UDS services

| SID | Service | Purpose |
|---|---|---|
| 0x10 | DiagnosticSessionControl | Change diagnostic session |
| 0x11 | ECUReset | Request an ECU reset |
| 0x14 | ClearDiagnosticInformation | Clear diagnostic information such as DTCs |
| 0x19 | ReadDTCInformation | Read diagnostic trouble codes and related information |
| 0x22 | ReadDataByIdentifier | Read data identified by a DID |
| 0x27 | SecurityAccess | Perform a security access procedure |
| 0x2E | WriteDataByIdentifier | Write data identified by a DID |
| 0x31 | RoutineControl | Start, stop, or request results of a routine |
| 0x34 | RequestDownload | Initiate a download transfer |
| 0x36 | TransferData | Transfer data blocks |
| 0x37 | RequestTransferExit | End a transfer operation |
| 0x3E | TesterPresent | Keep a diagnostic session active |

The exact service support, subfunctions, DIDs, routines, and access conditions depend on the ECU diagnostic specification.

## Diagnostic sessions

DiagnosticSessionControl (0x10) requests a diagnostic session.

Common session types include:

- Default Session.
- Programming Session.
- Extended Diagnostic Session.

An ECU may provide additional or manufacturer-specific session behavior. Some services are available only in certain sessions.

A tester should not assume that a service is permitted simply because the ECU responds to other diagnostic requests.

## ReadDataByIdentifier (0x22)

ReadDataByIdentifier is commonly used to retrieve ECU data.

The request contains the service identifier and one or more Data Identifiers (DIDs). A DID identifies a particular piece of diagnostic data according to the ECU's specification.

A simplified request:

~~~text
22 F1 90
~~~

Here, 0x22 is ReadDataByIdentifier and F1 90 is an example DID. The meaning of a DID is defined by the ECU or applicable diagnostic specification; do not assume every ECU uses the same mapping.

A positive response uses SID + 0x40. For 0x22, the positive response SID is 0x62.

## Negative Response

When an ECU cannot process a request, it can return a negative response message.

The negative response structure starts with:

~~~text
7F <Request SID> <NRC>
~~~

For example:

~~~text
7F 22 31
~~~

This indicates a negative response to service 0x22 with a response code of 0x31, commonly known as Request Out Of Range.

Some important negative response codes include:

| NRC | Meaning |
|---|---|
| 0x10 | General Reject |
| 0x11 | Service Not Supported |
| 0x12 | Sub-function Not Supported |
| 0x13 | Incorrect Message Length or Invalid Format |
| 0x22 | Conditions Not Correct |
| 0x31 | Request Out Of Range |
| 0x33 | Security Access Denied |
| 0x35 | Invalid Key |
| 0x78 | Response Pending |

The ECU's diagnostic specification defines the conditions under which particular NRCs are returned.

## Physical and functional addressing

UDS communication can use different addressing approaches.

**Physical addressing** targets a specific ECU.

**Functional addressing** targets a group or function, where supported by the diagnostic network and service.

A tester must use addressing rules defined for the vehicle network. Not every ECU responds to every functional request.

## UDS over CAN and ISO-TP

UDS messages can be larger than a single CAN frame. ISO-TP (ISO 15765-2) provides segmentation and reassembly over CAN.

ISO-TP uses frame types such as:

- Single Frame.
- First Frame.
- Consecutive Frame.
- Flow Control Frame.

A single-frame message fits in one transport frame. Larger messages use a first frame to indicate total length, followed by consecutive frames. The receiver sends flow control information to manage the transfer.

CAN FD can carry larger payloads per frame, but the transport implementation and configured addressing still need to be correct.

## TesterPresent and session timing

Some diagnostic sessions have timeouts. TesterPresent (0x3E) can be used to keep a session active when required.

The diagnostic tester should follow the ECU's P2 and P2* response timing parameters and session timeout behavior. A tool should not assume a fixed response time for every ECU or service.

## SecurityAccess

SecurityAccess (0x27) is commonly used to control access to protected diagnostic functions. A simplified seed-key exchange is one possible mechanism.

The ECU sends a seed, and the tester calculates a key according to an OEM-defined algorithm or authorization process. The ECU validates the key before granting the requested security level.

Do not treat seed-key as a universal algorithm. Implementations are specific to the ECU and may be protected by contractual or security requirements.

## Diagnostic testing considerations

A UDS test suite should consider:

1. Supported services and subfunctions.
2. Correct diagnostic session handling.
3. Physical and functional addressing.
4. Positive and negative response validation.
5. P2 and P2* timing.
6. TesterPresent and session timeout.
7. Security access restrictions.
8. DID, routine, and DTC definitions.
9. ISO-TP segmentation and flow control.
10. ECU reset and recovery behavior.

Always test destructive or programming operations on an approved bench or test setup. A diagnostic command can alter ECU configuration or software state.

## Common mistakes

### Confusing UDS with CAN

CAN is a data-link protocol. UDS is an application-layer diagnostic protocol. ISO-TP can carry UDS messages over CAN.

### Assuming every ECU supports every SID

UDS service support depends on the ECU and diagnostic specification.

### Ignoring negative responses

A negative response contains useful information. Test expected NRCs rather than treating every non-positive response as an unspecified failure.

### Ignoring transport timing

ISO-TP flow control and UDS response timing both matter. A correct service payload can still fail if transport or timing parameters are wrong.

## Frequently asked questions

### Is UDS only used over CAN?

No. UDS can operate over several transport protocols. CAN with ISO-TP is a common automotive implementation.

### What is the difference between a SID and a DID?

A SID identifies the diagnostic service. A DID identifies a particular data item used by services such as ReadDataByIdentifier and WriteDataByIdentifier.

### Does 0x78 mean the ECU rejected the request?

No. ResponsePending indicates that the ECU needs more time to complete the operation. The tester should continue waiting according to the specified timing behavior.

## Further reading

- [ISO 14229: Road vehicles — Unified diagnostic services](https://www.iso.org/standard/72439.html)
- [ISO 15765-2: Diagnostic communication over Controller Area Network](https://www.iso.org/standard/66574.html)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)

---
