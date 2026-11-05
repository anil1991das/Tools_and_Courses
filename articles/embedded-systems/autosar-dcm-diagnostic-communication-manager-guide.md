---
title: "AUTOSAR DCM Explained: Diagnostic Communication Manager and UDS Services"
description: "Understand the AUTOSAR Diagnostic Communication Manager, diagnostic request processing, service configuration, session and security handling, and PduR integration."
slug:"autosar-dcm-diagnostic-communication-manager-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR"
  - "DCM"
  - "UDS"
  - "Diagnostics"
  - "BSW"
relatedTools: []
relatedArticles:
  - "uds-diagnostics-explained"
  - "autosar-canif-cantp-explained"
draft: false
---

# AUTOSAR DCM Explained: Diagnostic Communication Manager and UDS Services

Automotive ECUs need to support diagnostic communication for manufacturing, service, troubleshooting, and software maintenance. In AUTOSAR Classic, the Diagnostic Communication Manager (DCM) is a key Basic Software module that processes diagnostic requests and implements configured diagnostic service behavior.

DCM is commonly associated with UDS services standardized by ISO 14229. It works with other communication and diagnostic modules to receive requests, evaluate conditions, and return responses.

## What is DCM?

DCM is the Diagnostic Communication Manager in AUTOSAR Classic. It handles diagnostic communication services according to configured protocol, service, session, security, and application-specific behavior.

A diagnostic tester sends a request. DCM receives the request through the configured communication path, checks whether the request is valid and permitted, invokes the appropriate service or application interface, and creates a response.

The exact supported services and behavior are defined by the ECU configuration and OEM diagnostic specification.

## Typical diagnostic communication path

For UDS over CAN, a simplified path is:

~~~text
Diagnostic Tester
       |
      CAN
       |
    CanIf
       |
    CanTp
       |
     PduR
       |
      DCM
       |
Application / Diagnostic Data
~~~

Some module routing details vary with AUTOSAR versions and configuration. For DoIP, the lower transport and network path is different, while DCM may still process the UDS service payload.

## DCM service processing

A typical request-processing flow includes:

1. Receive a diagnostic request.
2. Identify the diagnostic protocol and service.
3. Validate message length and format.
4. Check current diagnostic session.
5. Check security level and access conditions.
6. Evaluate application or vehicle preconditions.
7. Execute the service or call the configured application interface.
8. Create a positive or negative response.
9. Return the response through the configured routing path.

Some operations may be asynchronous. DCM can use pending-response behavior when an operation needs more time, according to the configured service and timing requirements.

## Diagnostic sessions

DCM manages diagnostic session behavior. Common UDS sessions include:
- Default Session.
- Programming Session.
- Extended Diagnostic Session.

The set of available services and subfunctions can depend on the active session.

A tester should verify the supported sessions and the session transition behavior in the ECU diagnostic specification.

## Security access

Diagnostic services can be restricted by security level or other authorization rules.

UDS SecurityAccess (0x27) is one common mechanism. DCM manages the configured security access behavior and coordinates with the relevant application or security implementation.

Do not assume that every ECU uses the same seed-key algorithm or security sequence. OEM-specific algorithms and access rules must be obtained through authorized project documentation.

## Service configuration

DCM configuration may include:
- Supported diagnostic protocols.
- Diagnostic services and subfunctions.
- Diagnostic sessions.
- Security levels.
- Data Identifiers (DIDs).
- Routine identifiers.
- DTC access.
- Read and write data callbacks.
- Timing parameters.
- Request and response PDU references.
- Functional and physical addressing behavior.

A configuration mismatch can prevent a request from reaching the intended handler even when the tester sends a correctly formatted UDS message.

## DIDs and application data

For services such as ReadDataByIdentifier (0x22) and WriteDataByIdentifier (0x2E), DCM uses configured DIDs to identify diagnostic data.

The actual data may be provided by application callbacks or generated interfaces. The DID number, length, scaling, read/write permission, and conditions come from the ECU diagnostic specification.

The application should validate data and operating conditions before allowing changes to important ECU state.

## Negative response handling

DCM can return a negative response when a request is unsupported or cannot be processed.

Examples include:
- Service Not Supported.
- Sub-function Not Supported.
- Incorrect Message Length or Invalid Format.
- Conditions Not Correct.
- Request Out Of Range.
- Security Access Denied.
- Response Pending.

The correct NRC depends on the request and current ECU conditions. Test cases should validate the expected NRC rather than simply expecting any negative response.

## DCM testing strategy

A practical DCM test plan can include:

1. Supported and unsupported service requests.
2. Correct and incorrect message lengths.
3. Session transition behavior.
4. Service availability by session.
5. Security access success and failure.
6. DID read and write access.
7. Routine execution and result reporting.
8. DTC read and clear behavior as configured.
9. Preconditions and NRC handling.
10. P2 and P2* timing behavior.
11. TesterPresent and session timeout.
12. Functional versus physical addressing.
13. Transport-layer segmentation and recovery.
14. Reset and programming procedure behavior where authorized.

Test destructive services only on approved bench setups with a known recovery procedure.

## Debugging a DCM issue

When a UDS request receives no expected response:
- Confirm the request is visible on the physical network.
- Verify transport-layer reassembly.
- Check PduR routing and DCM Rx PDU configuration.
- Inspect DCM protocol and service configuration.
- Confirm session and security state.
- Check preconditions and application callback results.
- Inspect response generation and Tx routing.
- Validate transport timing and response transmission.

A bus trace alone may show that a message arrived, but it cannot prove that DCM accepted or processed the request internally.

## Common mistakes

### Assuming DCM defines every DID

The ECU's diagnostic specification defines DID meaning, length, access, and application behavior. DCM implements the configured diagnostic handling.

### Confusing DCM with CanTp

DCM processes diagnostic services. CanTp transports segmented diagnostic messages over CAN.

### Ignoring session and security restrictions

A valid service request can be rejected because the ECU is in the wrong session or security level.

### Returning the wrong NRC

Map failures to the response codes required by the standard and diagnostic specification.

### Debugging only the physical bus

Trace routing, DCM state, callbacks, and response generation to locate the failure.

## Frequently asked questions

### Is DCM part of AUTOSAR BSW?

Yes. DCM is a Basic Software diagnostic module in the AUTOSAR Classic architecture.

### Does DCM implement UDS?

DCM provides the configured diagnostic service handling for UDS in AUTOSAR Classic. Its supported services and behavior depend on configuration and implementation.

### Can DCM work with DoIP?

Yes. A DoIP diagnostic path can transport UDS messages to a diagnostic application such as DCM, depending on the ECU architecture and configured stack.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [ISO 14229: Unified Diagnostic Services](https://www.iso.org/standard/72439.html)
- [ISO 15765-2: Diagnostic communication over CAN](https://www.iso.org/standard/66574.html)
- [ISO 13400: Diagnostics over IP](https://www.iso.org/standard/55282.html)

---
