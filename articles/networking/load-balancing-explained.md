---
title: "Load Balancing Explained: Distribute Traffic Across Servers"
description: "Learn how load balancers distribute requests, compare common algorithms, understand health checks and failover, and identify deployment trade-offs."
slug: "load-balancing-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Networking"
tags:
  - "Load Balancing"
  - "Networking"
  - "Scalability"
  - "Infrastructure"
relatedTools: []
relatedArticles:
  - "cdn-caching-explained"
  - "tcp-vs-udp-explained"
draft: false
---

# Load Balancing Explained: Distribute Traffic Across Servers

A single application server may eventually reach the limits of its CPU, memory, network, or connection capacity. A load balancer distributes incoming traffic across multiple backend servers so that the application can handle more requests and avoid depending on one instance.

Load balancing is used in web applications, APIs, databases, and distributed infrastructure.

## What is a load balancer?

A load balancer receives client traffic and forwards requests to one or more backend servers according to a configured policy.

A simplified architecture:

~~~text
             Clients
                |
          Load Balancer
          /     |     \
       Server Server Server
          A     B      C
~~~

The load balancer can improve availability and distribute workload. It does not automatically make the application scalable; the backend architecture and data layer must also support multiple instances.

## Layer 4 and Layer 7 load balancing

### Layer 4

A Layer 4 load balancer operates using transport-level information such as IP addresses and TCP or UDP ports.

It generally does not need to inspect HTTP paths or application headers to make its routing decision.

### Layer 7

A Layer 7 load balancer understands application protocols such as HTTP. It may route traffic based on hostnames, URL paths, headers, cookies, or other application-level information.

For example, requests to /api may be routed to API servers while /images may be routed to a static-content service.

Layer 7 routing offers more application awareness but may require additional processing and protocol configuration.

## Common load-balancing algorithms

| Algorithm | How it works | Typical consideration |
|---|---|---|
| Round robin | Sends requests to backends in sequence | Simple when capacity is similar |
| Weighted round robin | Assigns more traffic to higher-weight servers | Useful for different server capacities |
| Least connections | Prefers a backend with fewer active connections | Useful when connection duration varies |
| Least response time | Considers response behavior and load | Depends on accurate measurements |
| IP hash | Uses a client address-derived value | Can provide affinity but may skew distribution |
| Random | Selects a backend randomly | Simple and may work well at scale with suitable assumptions |

The best algorithm depends on workload, server capabilities, request duration, and state-management requirements.

## Health checks

A load balancer can periodically check whether a backend is healthy.

A health check might test a TCP connection or call an HTTP endpoint such as /health.

Health checks should reflect whether a server can safely receive traffic. A check that depends on every downstream service can remove all application instances during a temporary dependency issue.

Use separate liveness and readiness concepts where the platform supports them.

## Session persistence and sticky sessions

Some applications store user state in local server memory. If a user's next request reaches another server, that server may not have the required session state.

Sticky sessions, also known as session affinity, try to route a client to the same backend for a period.

A more scalable design often stores shared session state or uses stateless tokens where appropriate. Sticky sessions can still be useful, but they may reduce flexibility and create uneven load.

## TLS termination

A load balancer may terminate TLS, decrypting client traffic and forwarding requests to backend servers.

The link between the load balancer and backend must also be designed securely. Depending on the threat model, it may need TLS, mutual authentication, and restricted network access.

Be careful with forwarded headers such as X-Forwarded-For and Forwarded. Applications should trust these headers only from known proxies that set them correctly.

## Failover and availability

If a backend becomes unhealthy, the load balancer can stop sending new traffic to it. Existing connections and in-flight requests may still fail.

High availability also requires attention to:

- Load balancer redundancy.
- DNS and routing.
- Backend capacity during failures.
- Health-check intervals and thresholds.
- Deployment and maintenance procedures.
- Database and storage availability.
- Monitoring and alerting.

A load balancer itself can become a single point of failure unless the architecture provides redundancy.

## Load balancing and scaling

Horizontal scaling adds more application instances. A load balancer distributes requests among them.

However, scaling application servers does not necessarily scale:

- Database throughput.
- Cache capacity.
- Message brokers.
- External APIs.
- Shared file storage.
- Network bandwidth.

Measure the entire system to identify the actual bottleneck.

## Common mistakes

### Assuming round robin means equal CPU load

Requests can have different processing costs. Equal request counts do not guarantee equal resource usage.

### Ignoring backend state

Local in-memory session state can make requests depend on a particular instance.

### Using poor health checks

A health check that is too strict can mark healthy servers unavailable. A check that is too shallow can keep broken servers in rotation.

### Trusting client-supplied forwarded headers

Only trust proxy headers from known infrastructure. Otherwise, clients may spoof their apparent address or scheme.

### Forgetting connection draining

During deployments or maintenance, allow existing requests to complete where possible instead of abruptly terminating connections.

## Frequently asked questions

### Is a reverse proxy the same as a load balancer?

A reverse proxy accepts requests and forwards them to upstream servers. Many reverse proxies also provide load balancing, TLS termination, caching, or routing.

### Can load balancing improve security?

It can provide a control point for filtering, rate limiting, and hiding backend addresses, but it is not a substitute for application security.

### Does adding more servers always improve performance?

No. The database, network, lock contention, or external dependencies may become the bottleneck. Capacity planning and measurement are required.

## Further reading

- [NGINX: HTTP Load Balancing](https://nginx.org/en/docs/http/load_balancing.html)
- [HAProxy Documentation](https://www.haproxy.com/documentation/)
- [Google Cloud: Load balancing overview](https://cloud.google.com/load-balancing/docs/load-balancing-overview)

---
