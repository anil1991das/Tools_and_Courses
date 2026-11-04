---
title: "DNS Records Explained: A, AAAA, CNAME, MX, TXT, and More"
description: "Learn what common DNS record types do, how TTL works, and which records are used for websites, email, domain verification, and service discovery."
slug: "dns-records-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Networking"
tags:
  - "DNS"
  - "Domains"
  - "Networking"
  - "Web Hosting"
relatedTools: []
relatedArticles: []
draft: false
---

# DNS Records Explained: A, AAAA, CNAME, MX, TXT, and More

The Domain Name System (DNS) helps applications find services using human-readable names such as example.com. DNS records store the information that tells resolvers how a domain should be handled.

When configuring a website, email service, verification process, or subdomain, you will often be asked to create or update a DNS record. Understanding the common record types helps avoid configuration errors.

## What is a DNS record?

A DNS record is an entry in a DNS zone. It includes a name, type, data, and other properties such as a time to live (TTL).

A simplified record can be thought of as:

~~~text
Name     Type    Value
example  A       192.0.2.10
~~~

This example maps a name to an IPv4 address. The IP address is from a documentation range, not a real hosting destination.

DNS is distributed. A recursive resolver may query authoritative name servers and cache responses according to their TTL values.

## Common DNS record types

| Record | Purpose | Example use |
|---|---|---|
| A | Maps a name to an IPv4 address | Website server address |
| AAAA | Maps a name to an IPv6 address | IPv6-enabled website |
| CNAME | Makes one name an alias of another name | Point www to a canonical hostname |
| MX | Identifies mail servers for a domain | Receive email |
| TXT | Stores text data | Domain verification and email policies |
| NS | Identifies authoritative name servers | Delegate a DNS zone |
| SOA | Stores zone authority and timing information | Zone configuration |
| CAA | Restricts which certificate authorities may issue certificates | Domain certificate policy |
| SRV | Identifies a host and port for a named service | Service discovery |
| PTR | Supports reverse DNS lookup | Map an IP address back to a name |

Record syntax and restrictions depend on the record type and DNS provider.

## A and AAAA records

An A record maps a hostname to an IPv4 address.

An AAAA record maps a hostname to an IPv6 address.

A domain can have multiple address records, allowing a name to resolve to more than one destination. The client and network choose how to use the returned addresses.

## CNAME records

A CNAME record makes one DNS name an alias for another canonical name.

For example, a subdomain such as www.example.com may point to a hosting provider's hostname. The resolver follows the alias to obtain the relevant records.

A CNAME cannot generally coexist with other record data at the same owner name under standard DNS rules. Some DNS providers offer flattening or alias-like features at the zone apex; these are provider-specific mechanisms, not ordinary CNAME records.

## MX records for email

An MX record identifies the mail servers responsible for receiving email for a domain. MX records include a priority value. Lower preference values are tried before higher values.

A domain's MX records must point to hostnames that can be resolved to addresses. Incorrect MX configuration can prevent incoming email from reaching the intended provider.

## TXT records for verification and email security

TXT records can hold arbitrary text data within DNS limits. They are commonly used for:

- Domain ownership verification.
- SPF policy publishing.
- DKIM public key information.
- DMARC policy publishing.
- Service-specific verification strings.

Email authentication records have specific syntax and evaluation rules. Adding a random TXT value does not automatically configure email security.

## TTL: Time to live

TTL controls how long a DNS response may be cached by a resolver before it needs to be queried again.

A shorter TTL can make changes eligible to be refreshed sooner, but it can increase query traffic. A longer TTL can reduce lookups but keep old data cached longer after a change.

Changing a DNS record does not force every resolver to immediately discard its cached answer. The previous TTL and resolver behavior affect when users see the new result.

## How to add or change a DNS record

1. Open the DNS management panel for the domain.
2. Identify the exact hostname or subdomain to configure.
3. Choose the record type required by the service.
4. Enter the record value exactly as supplied.
5. Set an appropriate TTL.
6. Save the record.
7. Verify the result using DNS lookup tools and test the affected service.

Be careful when replacing A, AAAA, or MX records. Incorrect values can interrupt website traffic or email delivery.

## DNS records versus domain registration and hosting

These are related but separate services:

- **Domain registration** gives you the right to use a domain name for a registration period.
- **DNS hosting** manages the records that answer queries for the domain.
- **Web hosting** serves website files or application responses.
- **Email hosting** provides mailboxes and mail services.

Changing a DNS record can direct traffic to a different provider without transferring the domain registration itself.

## Common DNS mistakes

### 1. Creating a record for the wrong hostname

A record for the root domain is different from a record for www or another subdomain. Check the exact name.

### 2. Confusing CNAME and A records

An A record contains an IPv4 address. A CNAME contains another hostname.

### 3. Publishing multiple conflicting SPF records

SPF configuration should be consolidated according to the email provider's instructions. Multiple SPF TXT records at the same domain can cause evaluation problems.

### 4. Expecting instant DNS propagation

Cached values can remain visible until their TTL expires. Test from more than one resolver when diagnosing a change.

### 5. Deleting existing records without understanding them

A DNS zone may include records required for email, verification, subdomains, and third-party services. Export or document the existing zone before making major changes.

## Frequently asked questions

### Which DNS records are needed for a website?

At minimum, the website's hostname must resolve to the hosting destination, commonly using A, AAAA, or a provider-supported alias. The required configuration depends on the hosting service.

### Does an MX record point to an IP address?

No. An MX record points to a mail server hostname. That hostname must then resolve to an IP address.

### What happens if I delete a TXT record?

The effect depends on its purpose. You may break domain verification, email authentication, or another service that relies on it.

## Further reading

- [Cloudflare: DNS record types](https://developers.cloudflare.com/dns/manage-dns-records/reference/dns-record-types/)
- [Cloudflare Learning Center: DNS records](https://www.cloudflare.com/learning/dns/dns-records/)
- [RFC 1035: Domain Names - Implementation and Specification](https://www.rfc-editor.org/rfc/rfc1035)

---
