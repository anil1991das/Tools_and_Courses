---
title: "Automotive Ethernet Explained: 100BASE-T1, 1000BASE-T1, PHY, and MAC"
description: "Understand single-pair automotive Ethernet, the roles of MAC and PHY, link segments, switches, and common validation considerations."
slug:"automotive-ethernet-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "Automotive Ethernet"
  - "100BASE-T1"
  - "1000BASE-T1"
  - "Ethernet"
  - "Automotive Networking"
relatedTools: []
relatedArticles:
  - "tcp-vs-udp-explained"
  - "can-vs-can-fd-explained"
  - "uds-diagnostics-explained"
draft: false
---

# Automotive Ethernet Explained: 100BASE-T1, 1000BASE-T1, PHY, and MAC

Modern vehicles contain cameras, radar sensors, infotainment systems, gateways, domain controllers, and high-performance compute units. These systems can require more bandwidth than traditional low-speed vehicle networks provide.

Automotive Ethernet adapts Ethernet technology to vehicle environments, including single-pair physical layers designed for automotive cabling, electromagnetic compatibility, and topology requirements.

## What is Automotive Ethernet?

Automotive Ethernet is a family of Ethernet technologies and system specifications used in vehicle networks.

A key feature is single-pair Ethernet physical layers, which can carry Ethernet data over one balanced twisted pair rather than the multi-pair cabling used by conventional office Ethernet.

Examples include:

- 100BASE-T1: 100 Mbit/s single-pair Ethernet.
- 1000BASE-T1: 1 Gbit/s single-pair Ethernet.
- Higher-speed single-pair variants for more demanding applications.

The selected PHY, channel, connectors, cable, and network architecture must meet the applicable standard and vehicle requirements.

## What is 100BASE-T1?

100BASE-T1 is a 100 Mbit/s automotive Ethernet physical-layer technology standardized through IEEE 802.3.

It uses one balanced pair and supports full-duplex communication over a point-to-point link segment.

Compared with conventional 100BASE-TX, which typically uses two pairs, 100BASE-T1 is designed for vehicle cabling constraints.

The actual supported segment length and performance depend on the compliant PHY, cable, connector, and channel requirements. Do not assume that any generic twisted pair will work.

## What is 1000BASE-T1?

1000BASE-T1 provides 1 Gbit/s Ethernet over a single balanced pair. It is used when higher bandwidth is needed, such as for camera systems, compute platforms, and backbone links.

The PHY and channel requirements differ from 100BASE-T1. Components and cabling should be selected and validated for the intended Ethernet variant.

## MAC and PHY: what is the difference?

Ethernet communication is commonly divided into Media Access Control (MAC) and Physical Layer (PHY) functions.

- **MAC:** handles Ethernet frame transmission and reception, addressing, and link-layer control functions.
- **PHY:** handles physical signaling, encoding, transmission, reception, and link-related functions.

An automotive Ethernet ECU commonly uses an Ethernet MAC integrated into the SoC or controller and an external automotive Ethernet PHY connected through an interface such as MII, RMII, RGMII, SGMII, or another supported interface.

The specific interface depends on the SoC and PHY datasheets.

## Switches and network topology

Automotive Ethernet networks commonly use Ethernet switches to connect multiple ECUs.

A simplified topology:

~~~text
Camera ECU ----Radar ECU ------ Ethernet Switch ---- Gateway
Compute ECU ---/
Infotainment --/
~~~

The switch forwards Ethernet frames based on its forwarding information. The exact network architecture may include VLANs, routing, time synchronization, quality-of-service policies, and other system-level functions.

A switched Ethernet network differs from a shared CAN bus. Each link segment and switch port must be configured and validated as part of the network design.

## Automotive Ethernet versus CAN and CAN FD

| Feature | CAN / CAN FD | Automotive Ethernet |
|---|---|---|
| Communication model | Message-oriented bus | Ethernet frames over point-to-point links and switches |
| Typical data rates | Up to 1 Mbit/s Classical CAN in common configurations; CAN FD data phase can be faster | 100 Mbit/s, 1 Gbit/s and higher variants |
| Payload | CAN FD up to 64 bytes per data frame | Ethernet MTU commonly much larger |
| Topology | Shared bus | Switched network |
| Common use | Control signals and diagnostics | Cameras, radar, gateways, backbone, high-bandwidth data |
| Protocol stack | CAN-based protocols | IP, TCP, UDP, SOME/IP, DoIP and other Ethernet protocols as required |

Both technologies remain useful. Vehicle network design selects protocols according to bandwidth, latency, determinism, cost, safety, and functional requirements.

## Protocols above Automotive Ethernet

Ethernet provides data-link and physical connectivity. Higher layers define application communication.

Common technologies include:

- IPv4 and IPv6.
- UDP and TCP.
- SOME/IP for service-oriented communication.
- DoIP for diagnostics over IP.
- Time synchronization protocols such as IEEE 802.1AS in suitable architectures.

Not every ECU uses all of these protocols. Their selection depends on the vehicle architecture and OEM specification.

## Sleep and wake-up considerations

Vehicle networks have strict power-management requirements. Automotive Ethernet PHYs and network systems may support sleep and wake-up functions defined by applicable specifications and industry agreements.

For example, the OPEN Alliance publishes specifications and test material related to automotive Ethernet, including sleep/wake behavior for supported technologies.

PHY support alone does not guarantee the complete vehicle network wake-up function. System-level configuration, power management, and ECU integration must also be correct.

## Automotive Ethernet testing

A validation plan may include:

- Link establishment and link recovery.
- PHY configuration and status.
- MAC frame transmission and reception.
- Error counters and packet loss.
- Throughput and latency.
- VLAN and priority handling where used.
- Cable and connector channel compliance.
- EMC and electrical behavior.
- Sleep and wake-up sequences where supported.
- Time synchronization where required.
- Interoperability between PHYs and switches.

Use suitable automotive Ethernet test equipment and follow the applicable PHY, channel, and system test specifications.

## Common mistakes

### Treating Automotive Ethernet as just ordinary Ethernet with a different cable

Automotive Ethernet has specific physical-layer and channel requirements. Validate the complete link segment and supported PHY configuration.

### Confusing MAC and PHY responsibilities

The MAC handles Ethernet framing functions; the PHY implements the physical signaling. Debug using the relevant interface and status information.

### Assuming CAN and Ethernet have the same topology

A switched Ethernet network and a shared CAN bus have different media access, frame handling, and network architecture.

### Ignoring sleep/wake and power modes

Vehicle network behavior includes low-power and wake-up requirements. Validate them as part of system integration.

### Testing only throughput

A link can pass data at the expected rate and still fail timing, interoperability, EMC, or power-management requirements.

## Frequently asked questions

### Is 100BASE-T1 full duplex?

Yes. 100BASE-T1 is designed for full-duplex communication over a single balanced pair.

### Does Automotive Ethernet use IP?

It can. Ethernet is the link technology; IP, TCP, UDP, SOME/IP, and DoIP are higher-layer protocols used where the architecture requires them.

### Can Automotive Ethernet replace CAN?

Not in every application. CAN and CAN FD remain widely useful for control and communication tasks where their cost, simplicity, and properties fit the requirements.

## Further reading

- [OPEN Alliance: Automotive Ethernet Specifications](https://opensig.org/automotive-ethernet-specifications/)
- [NXP: TJA1103 100BASE-T1 PHY](https://docs.nxp.com/bundle/TJA1103/page/topics/general_description.html)
- [IEEE 802.3 Ethernet Working Group](https://www.ieee802.org/3/)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)

---
