---
title: "TCP vs UDP: Key Differences and When to Use Each"
description: "Compare TCP and UDP connection behavior, reliability, ordering, overhead, and common applications in networking."
slug: "tcp-vs-udp-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Networking"
tags:
  - "TCP"
  - "UDP"
  - "Networking"
  - "Transport Protocols"
relatedTools: []
relatedArticles: []
draft: false
---

# TCP vs UDP: Key Differences and When to Use Each

TCP and UDP are transport-layer protocols used by applications to communicate over IP networks. Both use port numbers to identify application endpoints, but they provide different communication behavior.

Understanding the difference helps when working with web applications, streaming, real-time communication, embedded devices, and network troubleshooting.

## What is TCP?

Transmission Control Protocol (TCP) provides a reliable, ordered byte stream between two endpoints.

TCP uses mechanisms such as connection establishment, sequence numbers, acknowledgements, retransmission, flow control, and congestion control. These mechanisms help deliver bytes reliably and in order, subject to connection and network conditions.

A typical TCP connection begins with a handshake:

1. The client sends SYN.
2. The server responds with SYN-ACK.
3. The client sends ACK.

After the connection is established, both endpoints can exchange data.

TCP does not preserve application message boundaries. If an application sends two writes, the receiver may read the bytes in different-sized chunks. The application protocol must define how messages are framed.

## What is UDP?

User Datagram Protocol (UDP) provides a minimal datagram transport. It sends independent datagrams without establishing a connection in the same way as TCP.

UDP does not guarantee delivery, ordering, or duplicate protection. Applications that need these properties must implement appropriate mechanisms themselves or use a protocol built on UDP.

UDP has a small header and can be useful when low latency, message boundaries, or application-controlled reliability are important.

## TCP versus UDP

| Feature | TCP | UDP |
|---|---|---|
| Communication model | Connection-oriented byte stream | Connectionless datagrams |
| Delivery guarantee | Reliable delivery mechanisms | No delivery guarantee |
| Ordering | Ordered byte stream | No ordering guarantee |
| Retransmission | Built-in retransmission behavior | Application/protocol responsibility |
| Flow control | Yes | No built-in transport flow control |
| Congestion control | Yes | No built-in congestion control in UDP itself |
| Message boundaries | Not preserved | Datagram boundaries preserved |
| Header overhead | Larger than UDP | Small fixed header |

The choice is not simply "TCP is slow and UDP is fast." Real performance depends on the application, network, congestion, packet size, and protocol design.

## Common TCP use cases

TCP is commonly used where reliable ordered delivery is useful:

- Web traffic using HTTP/1.1 and HTTP/2 over TCP.
- Secure shell (SSH).
- File transfer protocols.
- Database connections.
- Many email protocols.
- Application protocols that expect a stream of bytes.

A TCP connection is not inherently encrypted. HTTPS and SSH use additional security mechanisms to protect data.

## Common UDP use cases

UDP is used when datagram behavior is suitable:

- DNS queries, depending on response size and protocol behavior.
- Real-time voice and video applications.
- Online games.
- Network discovery and telemetry.
- QUIC, the transport protocol used by HTTP/3.

A UDP-based application may add acknowledgements, retransmissions, ordering, or congestion control at a higher layer. QUIC is a good example of a transport protocol that uses UDP while providing reliable streams and modern connection features.

## Why HTTP/3 uses QUIC over UDP

HTTP/3 uses QUIC, which runs over UDP. QUIC provides connection management, encryption integration, reliable streams, and congestion control above UDP.

This does not mean HTTP/3 is an unreliable protocol. UDP supplies the datagram delivery mechanism, while QUIC implements the transport features required by HTTP/3.

## Ports and sockets

Both TCP and UDP use port numbers to distinguish application endpoints.

For example, a service may listen on TCP port 443 for HTTPS over TCP, while a QUIC-based HTTP/3 service commonly uses UDP port 443.

A port number alone does not identify the application protocol with certainty. Firewalls, server configuration, and application negotiation determine what is actually running.

## How to choose between TCP and UDP

Choose based on the application requirements:

- Need a reliable ordered stream and do not want to implement transport reliability yourself? TCP may be appropriate.
- Need independent datagrams and can tolerate loss or implement custom recovery? UDP may be appropriate.
- Need reliable multiplexed streams over UDP? Consider a protocol such as QUIC rather than building reliability from scratch.
- Need to follow an existing standard? Use the transport required by that protocol.

Do not choose UDP solely because a workload is described as real-time. Packet loss, congestion, security, and application recovery still need to be considered.

## Common networking mistakes

### 1. Assuming UDP is always faster

UDP avoids some TCP mechanisms, but an application may need to implement its own recovery, ordering, and congestion behavior. The best choice depends on the whole protocol.

### 2. Assuming TCP sends one message per write

TCP is a byte stream. Application code must handle partial reads and define message boundaries.

### 3. Treating TCP as encryption

TCP does not encrypt content. Use TLS or another suitable security protocol when confidentiality and authentication are required.

### 4. Treating UDP as inherently insecure

Neither protocol provides application authentication or confidentiality by itself. Security depends on the protocol built on top and its configuration.

## Frequently asked questions

### Can TCP and UDP use the same port number?

Yes. TCP and UDP port spaces are separate. A host can have a TCP service and a UDP service using the same numeric port.

### Does UDP guarantee that a packet arrives?

No. UDP itself does not guarantee delivery or ordering. Applications must decide how to handle lost or delayed datagrams.

### Is HTTP/3 based on TCP or UDP?

HTTP/3 uses QUIC, which runs over UDP. QUIC provides reliable streams and other transport features above UDP.

## Further reading

- [RFC 9293: Transmission Control Protocol](https://www.rfc-editor.org/rfc/rfc9293)
- [RFC 768: User Datagram Protocol](https://www.rfc-editor.org/rfc/rfc768)
- [RFC 9000: QUIC: A UDP-Based Multiplexed and Secure Transport](https://www.rfc-editor.org/rfc/rfc9000)
- [MDN: Overview of HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview)

---
