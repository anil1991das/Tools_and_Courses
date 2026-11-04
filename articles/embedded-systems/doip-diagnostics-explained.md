---
title: "DoIP Explained: Diagnostics over Internet Protocol in Automotive ECUs"
description: "Understand Diagnostics over IP, its vehicle-discovery and routing-activation concepts, diagnostic message flow, and how DoIP relates to UDS."
slug:"doip-diagnostics-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
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

# DoIP Explained: Diagnostics over Internet Protocol in Automotive ECUs

Modern vehicles increasingly use Ethernet networks for diagnostics, software update workflows, and high-bandwidth communication. Diagnostics over Internet Protocol (DoIP) provides a standardized way to transport diagnostic messages over IP-based networks.

DoIP is commonly used to carry UDS diagnostic messages over automotive Ethernet. It separates diagnostic application services from the network transport used to reach the ECU.

## What is DoIP?

DoIP is standardized in the ISO 13400 family. It defines vehicle discovery and diagnostic communication over IP networks, including message formats and connection behavior.

A diagnostic tester communicates with a vehicle or DoIP entity. A DoIP gateway or ECU may expose diagnostic routing to one or more target addresses.

The exact architecture depends on the vehicle network and OEM implementation.

## DoIP and UDS are different layers

UDS defines diagnostic application services such as ReadDataByIdentifier, ReadDTCInformation, and DiagnosticSessionControl.

DoIP provides the network transport and vehicle discovery mechanisms used to deliver diagnostic messages over IP.

A simplified stack is:

~~~text
Diagnostic Tester
       |
      UDS
       |
      DoIP
       |
      TCP / IP
       |
 Automotive Ethernet
       |
 ECU or DoIP Gateway
~~~

The UDS request semantics remain distinct from the DoIP message framing and routing.

## Vehicle identification and discovery

DoIP supports vehicle discovery mechanisms so a diagnostic tester can identify a reachable DoIP entity.

Discovery may use UDP-based messages, including vehicle identification requests and responses, according to the applicable ISO 13400 version and network configuration.

A tester should validate the response and identify the correct vehicle or entity before attempting diagnostic communication.

Do not rely on an IP address alone when several vehicles or diagnostic gateways may be present on the same network.

## Routing activation

Before diagnostic messages are exchanged over a DoIP TCP connection, routing activation is commonly performed.

The tester requests activation using its logical address and any required activation information. The DoIP entity responds with an activation result.

Routing activation establishes whether the diagnostic tester is permitted to use the connection for the requested diagnostic route.

OEM policy and ISO 13400 requirements determine the exact addressing and authorization conditions.

## Diagnostic message flow

A simplified DoIP diagnostic sequence is:

1. Tester discovers the vehicle or DoIP entity.
2. Tester opens a TCP connection to the relevant DoIP endpoint.
3. Tester performs routing activation.
4. Tester sends a diagnostic message containing source and target logical addresses.
5. DoIP entity routes the payload to the target ECU or diagnostic function.
6. The diagnostic response is returned through the configured route.

The diagnostic message payload can contain UDS services. DoIP also defines message types and acknowledgements relevant to transport handling.

## Logical addresses

DoIP uses logical addresses to identify diagnostic entities such as testers, gateways, and ECUs.

These are not IP addresses and are not the same as CAN identifiers. IP addresses route packets at the network layer, while DoIP logical addresses identify diagnostic communication endpoints within the DoIP message format.

The logical address mapping is defined by the vehicle's diagnostic architecture and configuration.

## TCP and UDP usage

DoIP uses different transport protocols for different purposes.

UDP is commonly used for discovery and certain announcement messages. TCP is commonly used for diagnostic communication after connection and routing activation.

Do not assume every DoIP message uses TCP or that a successful IP ping confirms diagnostic routing is available.

## DoIP versus diagnostics over CAN

| Consideration | UDS over CAN | UDS over DoIP |
|---|---|---|
| Network | CAN / CAN FD | IP over Automotive Ethernet |
| Transport | ISO-TP | DoIP |
| Addressing | CAN-based physical or functional addressing | DoIP logical addresses |
| Bandwidth | Limited by CAN network | Higher potential throughput |
| Connection behavior | Transport protocol session | TCP connection and routing activation |
| Typical use | ECU diagnostics over CAN | Ethernet-based vehicle diagnostics and gateways |

Both approaches can coexist in a vehicle. A gateway may route diagnostic requests between networks.

## Troubleshooting DoIP

Check the following in order:

1. Physical Ethernet link and PHY status.
2. IP address, subnet, VLAN, and routing.
3. Vehicle discovery response.
4. TCP connection establishment.
5. DoIP header and message type.
6. Routing activation result.
7. Tester and target logical addresses.
8. Diagnostic payload and UDS service response.
9. Gateway routing and target ECU availability.

This sequence avoids confusing a network-layer failure with a UDS application-level rejection.

## Security considerations

- Restrict access to diagnostic networks and endpoints.
- Use the OEM-approved diagnostic network and tester configuration.
- Apply network segmentation and gateway policy.
- Authenticate or authorize diagnostic access according to vehicle requirements.
- Protect software programming operations.
- Monitor unexpected connection attempts.
- Do not expose diagnostic services to untrusted networks.

A reachable TCP endpoint does not automatically mean that a tester is authorized to perform every diagnostic operation.

## Common mistakes

### Treating DoIP as a UDS service

DoIP transports diagnostic messages. UDS defines the application services inside those messages.

### Skipping routing activation

A TCP connection can exist while diagnostic routing has not been activated.

### Confusing logical address and IP address

They are separate identifiers used at different layers.

### Assuming ping success proves DoIP readiness

ICMP reachability does not confirm TCP service availability, routing activation, or ECU diagnostic support.

## Frequently asked questions

### Is DoIP based on Ethernet?

Yes. It transports diagnostic communication over IP networks, commonly automotive Ethernet.

### Does DoIP replace UDS?

No. It can carry UDS messages. The diagnostic application layer remains separate.

### Is DoIP faster than UDS over CAN?

DoIP can offer higher transport bandwidth because it uses Ethernet/IP infrastructure. Actual diagnostic performance depends on the ECU, gateway, network, tester, and service processing time.

## Further reading

- [ISO 13400: Road vehicles — Diagnostics over Internet Protocol](https://www.iso.org/standard/76616.html)
- [ISO 14229: Unified Diagnostic Services](https://www.iso.org/standard/72439.html)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)

---
