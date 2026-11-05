---
title: "DoIP Explained: Diagnostics over Internet Protocol in Vehicles"
description: "Understand Diagnostics over IP, its vehicle communication path, logical addressing, routing activation, diagnostic messages, and testing considerations."
slug: "doip-diagnostics-over-ip-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "DoIP"
  - "UDS"
  - "Automotive Ethernet"
  - "Diagnostics"
  - "ISO 13400"
relatedTools: []
relatedArticles:
  - "uds-diagnostics-explained"
  - "automotive-ethernet-explained"
draft: false
---

# DoIP Explained: Diagnostics over Internet Protocol in Vehicles

Vehicle diagnostics traditionally used CAN-based communication, often with ISO-TP carrying UDS messages. As vehicle networks adopt Ethernet, diagnostic communication can also use Internet Protocol.

Diagnostics over Internet Protocol (DoIP) defines how diagnostic messages are transported over IP networks in road vehicles. It is standardized by ISO 13400 and is commonly used for high-bandwidth diagnostic access and vehicle gateway communication.

## What is DoIP?

DoIP is a diagnostic transport protocol designed for vehicle networks using IP communication. It supports discovery and communication with diagnostic entities and transports diagnostic messages between a tester and an ECU or gateway.

DoIP does not replace UDS service definitions. UDS still defines diagnostic services such as reading data, reading DTCs, and controlling diagnostic sessions.

A simplified communication path is:

~~~text
Diagnostic Tester
       |
   Ethernet/IP
       |
    Vehicle
    Gateway
       |
  DoIP Routing
       |
    Target ECU
       |
      UDS
~~~

The actual topology depends on vehicle architecture and gateway implementation.

## Vehicle identification and discovery

DoIP includes mechanisms for discovering vehicle entities and identifying diagnostic communication endpoints.

A tester may use vehicle identification requests and responses to locate a vehicle or DoIP entity on the network.

The exact discovery method, supported request types, and network behavior are defined by the applicable ISO 13400 version and vehicle implementation.

## Logical addressing

DoIP uses logical addresses to identify diagnostic entities, such as a tester, gateway, or target ECU.

Logical addressing is separate from the IP address and TCP/UDP port used to reach the DoIP entity.

A diagnostic tester needs the correct target logical address and the appropriate routing activation or connection procedure before sending diagnostic payloads.

## Routing activation

Routing activation establishes permission for a tester to exchange diagnostic messages through a DoIP entity.

The activation sequence is defined by the applicable protocol version and implementation. A request can be rejected if the tester is not permitted, the activation type is unsupported, or another condition is not satisfied.

Successful network connectivity does not automatically imply that diagnostic routing is active.

## Diagnostic messages

After the communication path is established, DoIP can carry diagnostic messages containing UDS payloads.

The diagnostic service itself is still interpreted by the target ECU. For example:
- 0x10 can request DiagnosticSessionControl.
- 0x22 can request ReadDataByIdentifier.
- 0x19 can request ReadDTCInformation.

The UDS request and response behavior remains subject to the ECU diagnostic specification.

## TCP and UDP roles

DoIP uses IP networking and defines communication behavior using transport protocols, including UDP for certain discovery and identification functions and TCP for diagnostic data communication in common deployments.

The exact port numbers, payload types, and communication sequence must be checked against the applicable ISO 13400 version and implementation.

Do not assume that every DoIP message uses the same transport protocol.

## DoIP versus UDS over CAN

| Aspect | UDS over CAN | UDS over DoIP |
|---|---|---|
| Network | CAN | Ethernet/IP |
| Transport | ISO-TP commonly used | DoIP |
| Addressing | CAN identifiers and transport addressing | IP endpoint and DoIP logical addresses |
| Bandwidth | Limited by CAN/CAN FD | Can use higher Ethernet bandwidth |
| Diagnostic service | UDS | UDS |
| Typical integration | CAN diagnostic path | Ethernet gateway and IP diagnostic path |

Both can expose similar UDS services. The transport, addressing, network setup, and timing behavior differ.

## Testing DoIP communication

A diagnostic test plan can include:

1. Vehicle discovery and identification.
2. IP address and network configuration.
3. DoIP entity response validation.
4. Routing activation and rejection cases.
5. Tester and target logical-address checks.
6. Diagnostic message payload validation.
7. TCP connection and disconnect behavior.
8. UDS positive and negative responses.
9. P2/P2* timing behavior at the diagnostic layer.
10. Gateway routing and ECU availability.
11. Connection recovery and multiple tester behavior where specified.

Use the correct vehicle network configuration and approved diagnostic access method. Do not send programming or control requests to a production vehicle without authorization.

## Common troubleshooting symptoms

### Vehicle discovery fails

Check the network interface, IP configuration, VLAN, firewall, discovery method, and whether the DoIP entity is available.

### TCP connection succeeds but diagnostics fail

Verify routing activation, logical addresses, payload format, and target ECU availability.

### Routing activation is rejected

Check tester address, activation type, gateway permissions, and vehicle-specific preconditions.

### UDS response is negative

A transport connection can be healthy while the ECU rejects the diagnostic service. Inspect the NRC and diagnostic specification.

### Diagnostic response times out

Separate TCP/network timeout behavior from UDS P2/P2* timing and ECU application processing.

## Security considerations

DoIP connectivity can expose sensitive diagnostic functions. Protect the vehicle network with appropriate segmentation, authentication or authorization mechanisms defined by the implementation, firewall policy, and controlled tester access.

Do not treat Ethernet connectivity as permission to access every ECU. Diagnostic security and programming authorization remain essential.

## Frequently asked questions

### Is DoIP the same as UDS?

No. DoIP transports diagnostic messages over IP. UDS defines the diagnostic services carried within those messages.

### Does DoIP use Ethernet?

Yes. It uses IP communication over an underlying network, commonly automotive Ethernet.

### Can DoIP replace CAN diagnostics?

It can provide an additional diagnostic path, but many vehicle architectures support both. The selected diagnostic path depends on the ECU network architecture and service requirements.

## Further reading

- [ISO 13400: Road vehicles — Diagnostic communication over Internet Protocol](https://www.iso.org/standard/55282.html)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [Vector: DoIP overview](https://www.vector.com/int/en/know-how/protocols/diagnostics/)

---
