---
title: "MQTT Protocol Explained: Publish, Subscribe, Topics, and QoS"
description: "Learn how MQTT brokers and clients communicate, how topics and quality-of-service levels work, and where MQTT fits in IoT systems."
slug: "mqtt-protocol-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and IoT"
tags:
  - "MQTT"
  - "IoT"
  - "Messaging"
  - "Embedded Systems"
  - "Networking"
relatedTools: []
relatedArticles: []
draft: false
---

# MQTT Protocol Explained: Publish, Subscribe, Topics, and QoS

Internet of Things (IoT) systems often contain small devices that send telemetry to a server or receive commands from an application. A communication protocol should support lightweight message exchange without requiring every device to know the address of every other device.

MQTT is a lightweight publish-subscribe messaging protocol commonly used in IoT and telemetry systems.

## What is MQTT?

MQTT is a client-server messaging protocol. Clients connect to a broker, publish messages to topics, and subscribe to topics to receive matching messages.

The broker receives messages from publishers and distributes them to subscribers based on topic filters.

This decouples publishers and subscribers. A sensor does not need to know which dashboard or service consumes its data.

## Publish-subscribe model

A basic MQTT architecture includes:

- **Publisher:** sends a message to a topic.
- **Broker:** receives, routes, and delivers messages.
- **Subscriber:** subscribes to topics and receives messages.
- **Topic:** a hierarchical name used to route messages.

Example:

~~~text
Topic: factory/line1/temperature
Payload: 28.5
~~~

A sensor publishes the temperature value. A monitoring application subscribes to the topic and receives the message through the broker.

## MQTT topics

Topics are hierarchical strings separated by forward slashes.

Examples:

~~~text
home/living-room/temperature
vehicle/ecu1/diagnostics/status
factory/line2/motor/current
~~~

A subscription may use wildcards:

- + matches one topic level.
- # matches multiple topic levels and is generally used at the end of a filter.

For example:

~~~text
factory/+/temperature
~~~

matches one level under factory followed by temperature.

A topic hierarchy should be designed consistently. Avoid embedding secrets or personal information in topic names.

## Quality of Service (QoS)

MQTT defines three QoS levels:

| QoS | Delivery behavior | Trade-off |
|---|---|---|
| 0 | At most once | Lowest protocol overhead; message may be lost |
| 1 | At least once | Retries may cause duplicates |
| 2 | Exactly once at the MQTT protocol delivery level | More protocol exchanges and overhead |

QoS is about protocol delivery behavior between MQTT endpoints. It does not guarantee that an application has processed and permanently stored a business event exactly once.

Applications should still consider message identifiers, idempotency, duplicate handling, and end-to-end acknowledgement requirements.

## Retained messages

A publisher can send a retained message. The broker stores the latest retained message for a topic and provides it to new subscribers when they subscribe to that topic.

This is useful for publishing the most recent device state, such as whether a sensor is online or what its current mode is.

Retained data should be updated or cleared deliberately. A stale retained value can mislead subscribers.

## Last Will and Testament

An MQTT client can configure a Last Will and Testament (LWT) message. If the broker detects that the client disconnected unexpectedly, it can publish the configured message.

This is commonly used to indicate that a device has become unavailable.

The exact timing and behavior depend on keep-alive settings, broker behavior, and the connection failure.

## MQTT connection and keep-alive

A client establishes a connection to the broker and negotiates protocol settings. The keep-alive mechanism helps the broker detect an unresponsive client.

MQTT can operate over TCP or other specified transports and can be protected with TLS. Authentication and authorization policies should be configured at the broker.

## MQTT versus HTTP

| Feature | MQTT | HTTP |
|---|---|---|
| Communication pattern | Publish-subscribe via broker | Request-response |
| Common use | Telemetry and event messaging | Web APIs and resource access |
| Message routing | Topic-based | URL/resource-based |
| Connection model | Long-lived client-broker connection is common | Often request-oriented, though connections can be reused |
| Delivery options | QoS levels | Application-defined behavior |

Neither protocol is universally better. MQTT is useful for messaging across distributed devices, while HTTP is natural for resource APIs and web interaction.

## Security considerations

- Use TLS when confidentiality and transport authentication are required.
- Require authentication for clients.
- Apply topic-level authorization.
- Use unique credentials for devices where practical.
- Rotate credentials and revoke compromised devices.
- Avoid publishing sensitive values to broadly subscribed topics.
- Restrict broker network access.
- Monitor connection and publish patterns.

A broker that permits anonymous access and unrestricted topic publishing can become an attack path.

## Where MQTT is useful

MQTT is used in:

- Smart home telemetry.
- Industrial monitoring.
- Remote sensor networks.
- Fleet and asset monitoring.
- Device status and event notification.
- IoT gateways and cloud integration.

It should not automatically be selected for hard real-time or safety-critical control loops. Latency, availability, broker dependency, network failure, and safety architecture must be evaluated.

## Common mistakes

### Treating QoS 2 as end-to-end exactly-once business processing

QoS 2 controls MQTT message delivery between protocol participants. It does not guarantee exactly-once execution in every application component.

### Using one shared credential for every device

Unique credentials make it easier to revoke one device without replacing credentials across the entire fleet.

### Ignoring retained messages

Retained messages can persist after a publisher disconnects. Clear or update them when the state becomes invalid.

### Using broad topic permissions

A device should publish and subscribe only to the topics it requires.

### Assuming broker delivery means application success

A message reaching a client does not prove that the receiving application completed the intended operation. Use application-level acknowledgement where required.

## Frequently asked questions

### Does MQTT require an internet connection?

No. MQTT can operate on a local network. The clients need connectivity to the configured broker.

### Can MQTT work on constrained devices?

Yes. MQTT is designed as a lightweight messaging protocol, although memory, CPU, network, and TLS requirements still depend on the client implementation and deployment.

### Is MQTT secure by default?

No. Security depends on transport protection, broker authentication, authorization, credential handling, and network configuration.

## Further reading

- [OASIS MQTT Version 5.0 Specification](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)
- [OASIS MQTT Version 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.html)
- [Eclipse Paho MQTT](https://eclipse.dev/paho/)

---
