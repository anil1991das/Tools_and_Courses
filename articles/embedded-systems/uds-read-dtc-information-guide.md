---
title: "UDS ReadDTCInformation (0x19): Read Diagnostic Trouble Codes"
description: "Learn the purpose of UDS service 0x19, common subfunctions, DTC status bytes, status masks, and practical diagnostic testing considerations."
slug: "uds-read-dtc-information-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "UDS"
  - "DTC"
  - "Diagnostics"
  - "ISO 14229"
relatedTools: []
relatedArticles:
  - "uds-diagnostics-explained"
  - "uds-diagnostic-session-control-guide"
draft: false
---

# UDS ReadDTCInformation (0x19): Read Diagnostic Trouble Codes

Vehicle ECUs monitor sensors, actuators, communication signals, and internal operating conditions. When a diagnostic fault is detected, the ECU may store a Diagnostic Trouble Code (DTC) and status information that helps technicians and diagnostic tools understand the issue.

UDS service ReadDTCInformation (SID 0x19) provides standardized ways to request DTC-related information from an ECU.

## What is a DTC?

A Diagnostic Trouble Code represents a detected or stored fault condition. The exact code allocation, description, failure criteria, and environmental data are defined by applicable standards and vehicle-specific diagnostic specifications.

A DTC is not simply a text message. It is an identifier associated with diagnostic behavior, status, and sometimes snapshot or extended-record data.

## Service 0x19 overview

The request begins with service identifier 0x19, followed by a subfunction and any required parameters.

Different subfunctions request different diagnostic information. Support varies by ECU and diagnostic specification.

Examples of commonly used subfunctions include:

| Subfunction | Common name | Purpose |
|---|---|---|
| 0x01 | ReportNumberOfDTCByStatusMask | Request a count of DTCs matching a status mask |
| 0x02 | ReportDTCByStatusMask | Request DTCs matching a status mask |
| 0x04 | ReportDTCSnapshotRecordByDTCNumber | Request snapshot data for a DTC |
| 0x06 | ReportDTCExtendedDataRecordByDTCNumber | Request extended data for a DTC |
| 0x0A | ReportSupportedDTC | Request supported DTC information |

The exact request and response layouts must be checked against the applicable ISO 14229 edition and ECU diagnostic specification.

## DTC status byte

A DTC can be associated with a status byte that indicates diagnostic state. Common status bits include concepts such as:

- test failed.
- test failed this operation cycle.
- pending status.
- confirmed status.
- test not completed since clear.
- test failed since clear.
- warning indicator requested.
- test not completed this operation cycle.

The meaning and availability of each bit depend on the applicable standard and ECU implementation. A tester should not assume every ECU supports every status bit.

## Status mask

A status mask lets a diagnostic client select DTCs with relevant status conditions.

For example, a tester may request DTCs whose status matches a chosen bit mask. The ECU applies the defined matching behavior and returns the supported information.

When analyzing results, distinguish between:
- The mask requested by the tester.
- The status-availability mask supported by the ECU.
- The status byte reported for an individual DTC.

Confusing these values can lead to incorrect test expectations.

## Example diagnostic flow

A simplified tester workflow may be:

1. Enter the diagnostic session required by the ECU.
2. Send a ReadDTCInformation request with a supported subfunction.
3. Receive and parse the positive response.
4. Interpret the DTC count or DTC records.
5. Compare returned records with expected diagnostic memory state.
6. Check the ECU's response timing and negative-response behavior.

The request bytes and expected record layout depend on the chosen subfunction and ECU specification. Use the approved diagnostic description rather than copying a generic payload into a vehicle test.

## Positive and negative responses

For a positive response to SID 0x19, the response SID is 0x59. The response also contains subfunction-specific information.

A negative response has the general structure:

~~~text
7F 19 NRC
~~~

Possible negative responses include:
- Service not supported.
- Subfunction not supported.
- Incorrect message length or invalid format.
- Conditions not correct.
- Request out of range.
- Response pending, where applicable.

The ECU's specification determines which responses are appropriate for each condition.

## Snapshot and extended data

Some diagnostic implementations store additional information when a DTC is set.

Snapshot records may capture selected data values around the fault event. Extended data records may contain additional diagnostic context, such as counters or other manufacturer-defined information.

The data identifier, record number, content, and scaling are defined by the diagnostic specification. Do not infer meanings from raw bytes without the relevant data description.

## Test cases for service 0x19

A validation plan can include:

1. Supported subfunction returns the expected response.
2. Unsupported subfunction returns the expected NRC.
3. Status mask selects the correct DTCs.
4. Returned DTC count matches the actual matching records.
5. DTC status bytes are interpreted correctly.
6. Snapshot record request returns the configured data.
7. Extended data record request returns the configured data.
8. Incorrect length and out-of-range parameters are handled correctly.
9. Behavior is verified in the permitted diagnostic session.
10. Response timing and transport segmentation are validated.

Use controlled fault injection and approved test procedures. Avoid creating real-world vehicle faults or clearing diagnostic information on a production vehicle without authorization.

## Common mistakes

### Treating the status mask as an individual DTC status

The requested filter and the returned DTC status byte serve different purposes.

### Assuming every DTC has snapshot data

Snapshot and extended records depend on ECU diagnostic configuration and fault behavior.

### Ignoring diagnostic session requirements

A service may be limited to specific sessions or security levels.

### Parsing the response without the ECU specification

DTC format, status support, snapshot records, and extended data need the applicable diagnostic description.

## Frequently asked questions

### What is UDS SID 0x19?

It is ReadDTCInformation, a service used to request DTC counts, status, and related records.

### What is the positive response SID?

For service 0x19, the positive response service identifier is 0x59.

### Does service 0x19 clear DTCs?

No. Clearing diagnostic information is a separate UDS service, commonly SID 0x14.

## Further reading

- [ISO 14229: Unified Diagnostic Services](https://www.iso.org/standard/72439.html)
- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [SAE J2012: Diagnostic Trouble Code Definitions](https://www.sae.org/standards/content/j2012_202303/)

---
