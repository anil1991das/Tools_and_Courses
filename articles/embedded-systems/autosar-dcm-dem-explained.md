---
title: "AUTOSAR DCM and DEM Explained: Diagnostic Communication and Event Management"
description: "Understand the roles of Diagnostic Communication Manager and Diagnostic Event Manager, how they work together, and how UDS faults are handled in AUTOSAR Classic."
slug: "autosar-dcm-dem-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR"
  - "DCM"
  - "DEM"
  - "UDS"
  - "Diagnostics"
relatedTools: []
relatedArticles:
  - "uds-diagnostics-explained"
  - "autosar-cantp-explained"
draft: false
---

# AUTOSAR DCM and DEM Explained: Diagnostic Communication and Event Management

Automotive ECUs need to detect faults, store diagnostic information, and communicate that information to a diagnostic tester. AUTOSAR Classic separates these responsibilities into modules such as Diagnostic Communication Manager (DCM) and Diagnostic Event Manager (DEM).

DCM handles diagnostic communication services. DEM manages diagnostic events and associated diagnostic trouble code information. They work together, but they have different responsibilities.

## What is DCM?

The Diagnostic Communication Manager implements diagnostic communication services and manages diagnostic request processing.

It commonly handles services such as:

- DiagnosticSessionControl (0x10).
- ECUReset (0x11).
- ClearDiagnosticInformation (0x14).
- ReadDTCInformation (0x19).
- ReadDataByIdentifier (0x22).
- SecurityAccess (0x27).
- CommunicationControl (0x28), where configured.
- RoutineControl (0x31).
- RequestDownload and transfer services, where configured.
- TesterPresent (0x3E).

Supported services, subfunctions, and access rules are defined by the ECU diagnostic specification and configuration.

## What is DEM?

The Diagnostic Event Manager collects and manages diagnostic events reported by software components and BSW modules.

Its responsibilities can include:

- Event status management.
- Debouncing and confirmation of events.
- Operation-cycle handling.
- Event memory management.
- DTC association.
- Freeze-frame and extended-data storage, where configured.
- Aging and healing behavior.
- Interfaces for diagnostic event reporting and retrieval.

The exact feature set depends on the AUTOSAR version, vendor implementation, and diagnostic configuration.

## How DCM and DEM interact

A simplified diagnostic flow is:

~~~text
Diagnostic Tester
       |
      UDS
       |
  Transport Layer
       |
      DCM
       |
      DEM
       |
 Diagnostic Events
       |
    DTC Memory
~~~

A tester sends a UDS request to DCM. When the request concerns diagnostic trouble codes, DCM obtains relevant information from DEM and constructs the diagnostic response.

When an application detects a fault, it reports the configured event to DEM. DEM evaluates the event status according to its configuration and manages the related diagnostic information.

## Diagnostic events and DTCs

An event represents a detected fault condition in the ECU software. A Diagnostic Trouble Code (DTC) is the externally visible diagnostic identifier associated with diagnostic information.

A single DTC may be associated with one or more internal events according to the diagnostic design. Do not assume that one software event always maps one-to-one to one DTC.

## Event debouncing

Fault signals may be noisy or temporary. Debouncing helps prevent a brief signal fluctuation from being treated immediately as a confirmed failure.

Common debouncing approaches include:

- Counter-based debouncing.
- Time-based debouncing.
- Monitor-internal debouncing.

The correct method and thresholds depend on the diagnostic requirements. Excessive debouncing delay can postpone fault detection, while insufficient filtering can produce false DTCs.

## DTC status and operation cycles

DEM maintains status information for diagnostic events and DTCs. UDS ReadDTCInformation can expose selected status and record information according to the ECU configuration.

Operation cycles define relevant periods of ECU operation for event processing, aging, and related diagnostic behavior.

The diagnostic requirements should define when cycles start and stop, how event status changes, and how DTC aging is evaluated.

## DCM diagnostic sessions and security

DCM manages diagnostic sessions and access restrictions. A service may be permitted only in a particular session or after an applicable security or authentication step.

DCM configuration also defines timing, subfunction support, DID access, routine access, and other diagnostic behavior.

The tester and ECU must follow the same diagnostic specification.

## Common integration problems

### Event reported but no DTC appears

Check event mapping, debounce configuration, operation cycle, enable conditions, event status, and event memory settings.

### DTC read response is unexpected

Verify DTC format, status mask, record configuration, supported subfunctions, and DCM-to-DEM integration.

### ClearDiagnosticInformation does not clear expected entries

Check the requested DTC group, event memory configuration, access conditions, and whether the relevant events are currently active.

### Service rejected in a diagnostic session

Confirm session permissions, security level, conditions, and configured service support.

## Testing DCM and DEM

A useful validation plan includes:

- Supported diagnostic services and subfunctions.
- Session transitions and timeouts.
- Positive and negative responses.
- Security and access restrictions.
- DID and routine access.
- Event reporting and debouncing.
- DTC status-bit behavior.
- Operation-cycle handling.
- Freeze-frame and extended-data access.
- DTC clearing and aging.
- Transport timing and multi-frame behavior.

Test destructive diagnostic operations on an approved bench or controlled vehicle. Follow the OEM diagnostic specification.

## Frequently asked questions

### Is DCM responsible for detecting faults?

Usually no. Application or BSW monitors detect and report events to DEM. DCM handles the diagnostic communication interface.

### Does DEM send UDS messages directly?

DEM manages diagnostic event information. DCM handles UDS service requests and responses, using DEM services for relevant diagnostic data.

### Is every DEM event visible as a DTC?

Not necessarily. The configured event-to-DTC mapping and diagnostic requirements determine what is exposed to a tester.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [ISO 14229: Unified Diagnostic Services](https://www.iso.org/standard/72439.html)
- [AUTOSAR Specifications](https://www.autosar.org/standards/classic-platform/)

---
