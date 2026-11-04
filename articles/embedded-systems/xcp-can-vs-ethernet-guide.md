---
title: "XCP on CAN vs XCP on Ethernet: Choosing a Measurement Transport"
description: "Compare XCP transport over CAN and Ethernet, including bandwidth, bus loading, setup, measurement timing, and practical selection criteria."
slug: "xcp-can-vs-ethernet-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "XCP"
  - "XCP on CAN"
  - "XCP on Ethernet"
  - "ECU Measurement"
relatedTools: []
relatedArticles:
  - "xcp-protocol-explained"
draft: false
---

# XCP on CAN vs XCP on Ethernet: Choosing a Measurement Transport

XCP separates measurement and calibration services from the transport used to exchange protocol packets. This makes it possible to use a common measurement workflow over different vehicle and development networks.

Two commonly used transport options are XCP on CAN and XCP on Ethernet. They support the same broad purpose, but the available bandwidth, network integration, and practical constraints are different.

## What is XCP transport?

XCP defines protocol commands and data exchange for measurement and calibration. A transport mapping specifies how those protocol packets travel over a communication network.

The ECU implements an XCP slave. A calibration or measurement tool acts as the master. The tool connects, negotiates capabilities, configures measurements, and accesses supported calibration objects.

Transport selection does not change the meaning of an XCP measurement object, but it affects the packet size, throughput, latency, and configuration.

## XCP on CAN

XCP on CAN carries XCP packets using CAN frames.

Advantages include:

- Reuse of an existing CAN interface and vehicle network.
- Convenient access on development benches.
- Availability of CAN monitoring and analysis tools.
- Suitability for lower-bandwidth measurement and calibration tasks.

Constraints include:

- Limited payload per CAN or CAN FD frame.
- Shared bus bandwidth with other traffic.
- Arbitration and bus load affect timing.
- DAQ throughput can be limited by configured packet size and event rate.

When a CAN network is shared with application communication, measurement traffic must be planned so it does not interfere with required communication timing.

## XCP on Ethernet

XCP on Ethernet carries XCP packets over an Ethernet-based transport.

Potential advantages include:

- Higher throughput than a typical Classical CAN measurement link.
- Better support for larger measurement data volumes.
- Easier separation of development measurement traffic from low-bandwidth control buses in some architectures.
- Support for high-rate DAQ use cases where the ECU and network are designed accordingly.

Constraints include:

- Ethernet interface and network configuration.
- IP addressing and transport setup.
- Switch and network capacity.
- ECU CPU and memory requirements.
- Security and access-control considerations.

Ethernet does not guarantee unlimited measurement bandwidth. The ECU implementation, link rate, packet processing, and network topology remain constraints.

## Compare the transports

| Consideration | XCP on CAN | XCP on Ethernet |
|---|---|---|
| Typical bandwidth | Lower | Higher |
| Network medium | CAN or CAN FD | Ethernet |
| Shared traffic | Often shares bus with ECU communication | Depends on network architecture |
| Setup | CAN channel and XCP configuration | Ethernet interface, IP/network and XCP configuration |
| DAQ use | Suitable within bus capacity | Better suited to high-volume acquisition |
| Latency behavior | CAN arbitration and bus load matter | Switches, queues, and network load matter |
| Common use | Bench and vehicle CAN measurement | High-rate development and calibration |

Exact rates and limits depend on the ECU, transport implementation, bus configuration, and test environment. Do not use a generic throughput number as a guaranteed XCP capacity.

## DAQ configuration affects performance

XCP Data Acquisition (DAQ) can group measurement values into configured data packets.

The actual acquisition rate depends on:

- Number of DAQ lists.
- Number and size of ODT entries.
- Event-channel rate.
- ECU task scheduling and measurement implementation.
- Transport packet size.
- Available network bandwidth.
- Tool processing and logging capacity.

A transport with higher raw bandwidth may still be limited by ECU memory, CPU load, or the measurement task's event frequency.

## Estimating measurement load

Before enabling a large DAQ configuration, estimate the data rate.

A simplified estimate is:

~~~text
Data rate ≈ Samples per second × Bytes per sample
~~~

For multiple variables, sum their contribution and include protocol overhead, packet headers, timestamps, and transport framing.

The estimate is only a planning aid. Validate the actual traffic with a network analyzer and ECU performance measurements.

## How to choose

Choose XCP on CAN when the measurement requirements fit the available CAN bandwidth, the ECU already exposes XCP on the CAN channel, and bench or vehicle access is the primary objective.

Choose XCP on Ethernet when high-rate acquisition or larger measurement volumes are needed and the ECU provides a suitable Ethernet XCP implementation.

For either option, confirm ECU access policy, measurement object availability, A2L consistency, tool support, and network capacity.

## Common mistakes

### Assuming Ethernet removes all DAQ limits

The ECU still has limits on memory, CPU, event rate, and data handling.

### Ignoring bus load on CAN

XCP traffic competes for bus access with other CAN frames. Validate application communication under measurement load.

### Using the wrong A2L file

The transport may connect successfully while measurements remain incorrect because the A2L does not match the software build.

### Comparing nominal link rates only

Consider effective payload throughput, packet overhead, scheduler behavior, and tool-side logging.

## Frequently asked questions

### Can the same XCP commands be used over CAN and Ethernet?

The XCP protocol services are designed to be transport-independent, but supported features and configuration can differ by transport implementation.

### Is XCP on Ethernet always better?

No. It is useful for higher bandwidth, but it may require additional ECU hardware, software configuration, and network setup. CAN can be sufficient for many measurement tasks.

### Does XCP on CAN support CAN FD?

Some implementations support XCP over CAN FD. Confirm ECU, tool, driver, and configuration support rather than assuming it from CAN FD hardware capability alone.

## Further reading

- [ASAM MCD-1 XCP](https://www.asam.net/standards/detail/mcd-1-xcp/)
- [ASAM MCD-2 MC (A2L)](https://www.asam.net/standards/detail/mcd-2-mc/)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)

---
