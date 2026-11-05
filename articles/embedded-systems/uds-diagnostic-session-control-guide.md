---
title: "UDS DiagnosticSessionControl (0x10): Sessions, Timing, and State"
description: "Understand UDS service 0x10, default and extended diagnostic sessions, programming sessions, session transitions, and tester timing."
slug: "uds-diagnostic-session-control-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "UDS"
  - "Diagnostic Session"
  - "ISO 14229"
  - "Automotive Diagnostics"
relatedTools: []
relatedArticles:
  - "uds-diagnostics-explained"
  - "uds-read-dtc-information-guide"
draft: false
---

# UDS DiagnosticSessionControl (0x10): Sessions, Timing, and State

An ECU may expose different diagnostic capabilities depending on its current state. Reading basic identification data, running extended diagnostics, and programming software are not necessarily permitted under the same conditions.

UDS service DiagnosticSessionControl (SID 0x10) requests a change to the ECU's diagnostic session. The active session can affect which services are supported, what security access is required, and how the ECU manages diagnostic timing.

## What is a diagnostic session?

A diagnostic session is a logical operating mode for diagnostic communication. It defines a set of services and behaviors available to the tester.

Common session types include:

- Default Session.
- Programming Session.
- Extended Diagnostic Session.

An ECU may implement additional session types or OEM-specific behavior. The supported sessions and their identifiers must be obtained from the ECU diagnostic specification.

## Service 0x10 request

A DiagnosticSessionControl request contains:
- Service identifier 0x10.
- A subfunction that identifies the requested session.
- Additional option bits where defined by the applicable standard.

A simplified request layout is:

~~~text
10 <sessionType>
~~~

The specific session value and any option behavior must be confirmed from the ECU's diagnostic specification. Do not assume a session identifier is supported simply because it appears in a generic example.

## Positive response

The positive response SID for service 0x10 is 0x50.

A positive response can include information such as:
- The accepted session type.
- P2 server maximum timing.
- P2* server maximum timing.

The tester uses the timing information to manage the wait for diagnostic responses. The byte layout and units should be decoded according to the applicable ISO 14229 edition and ECU specification.

## P2 and P2* timing

P2 is associated with the ECU's response time for a diagnostic request. P2* provides an extended timing allowance for operations that take longer and use ResponsePending behavior.

A tester should:
1. Observe the configured or negotiated timing.
2. Wait for the response within the appropriate limit.
3. Handle a negative response with ResponsePending where defined.
4. Apply the extended timing for the remaining operation.
5. Avoid arbitrary short timeouts that cause false failures.

P2 and P2* are protocol timing concepts. A test tool's internal timeout settings must be aligned with the ECU's specification and actual transport behavior.

## Session transitions

A diagnostic tester may request a different session during a connection. The ECU may apply conditions before accepting that transition.

Conditions can include:
- Vehicle or ECU state.
- Supply voltage.
- Engine or ignition state.
- Security access.
- Programming prerequisites.
- OEM-specific preconditions.

If the request is not accepted, the ECU may return a negative response such as Conditions Not Correct or another applicable NRC.

## Session timeout and TesterPresent

Some diagnostic sessions return to the default session after an inactivity timeout.

TesterPresent (SID 0x3E) can be used to indicate that the tester remains active. The exact timeout and behavior depend on the ECU and standard.

A tester should not send TesterPresent blindly at an arbitrary rate. Use the diagnostic timing parameters and avoid unnecessary bus traffic.

## Session and security are different

Entering an extended or programming session does not automatically grant permission to access every protected service.

SecurityAccess (SID 0x27) or another OEM-defined authentication mechanism may be required for specific operations.

Session selection controls diagnostic mode. Security access controls authorization for protected functions. Both can be required.

## A practical test plan

1. Confirm the ECU's supported diagnostic sessions.
2. Request each supported session under the specified preconditions.
3. Validate positive and negative response behavior.
4. Decode P2 and P2* timing values.
5. Verify which services are allowed in each session.
6. Test required security access independently.
7. Check session timeout behavior.
8. Verify TesterPresent handling.
9. Validate transition back to default session.
10. Repeat with physical addressing and applicable functional addressing where supported.

Programming and reset-related tests should be performed only on approved bench or vehicle setups with a defined recovery process.

## Common mistakes

### Assuming every ECU supports the same sessions

Session support and restrictions are ECU-specific. Use the diagnostic specification.

### Confusing session control with security access

A diagnostic session does not necessarily grant authorization for protected functions.

### Ignoring P2 and P2* values

The tester can report false failures if it uses an unsuitable timeout.

### Treating TesterPresent as a keep-alive for every possible condition

TesterPresent follows defined diagnostic behavior. It does not replace application or network availability checks.

## Frequently asked questions

### What is UDS SID 0x10?

DiagnosticSessionControl. It requests a change to the ECU's diagnostic session.

### What is the positive response SID?

The positive response SID is 0x50.

### Does entering programming session start a software download?

No. Session control changes the diagnostic session. A download requires separate services and must follow the ECU's programming procedure.

## Further reading

- [ISO 14229: Unified Diagnostic Services](https://www.iso.org/standard/72439.html)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [Vector: UDS diagnostics overview](https://www.vector.com/int/en/know-how/protocols/diagnostics/)

---
