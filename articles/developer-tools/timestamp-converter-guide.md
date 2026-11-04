---
title: "Unix Timestamps Explained: Seconds, Milliseconds, and UTC"
description: "Understand Unix epoch time, distinguish seconds from milliseconds, and convert timestamps into readable date and time values."
slug: "timestamp-converter-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Developer Tools"
tags:
  - "Unix Timestamp"
  - "Date and Time"
  - "UTC"
  - "JavaScript"
relatedTools:
  - title: "Timestamp Converter"
    url: "/timestamp-converter"
relatedArticles: []
draft: false
---

# Unix Timestamps Explained: Seconds, Milliseconds, and UTC

Logs, APIs, databases, and operating systems often represent dates as numbers. These values are called Unix timestamps when they count time relative to the Unix epoch: 1970-01-01 00:00:00 UTC.

A timestamp is compact and easy for software to compare, but it is not immediately readable as a calendar date. The [iamnoobs Timestamp Converter](https://iamnoobs.com/timestamp-converter) helps convert a Unix timestamp into a readable date and time.

## What is a Unix timestamp?

A Unix timestamp commonly represents the number of seconds elapsed since the Unix epoch.

For example, a value such as:

~~~text
1700000000
~~~

represents a particular moment in time when interpreted as seconds from the epoch.

Some systems instead use milliseconds. JavaScript's Date APIs use milliseconds for timestamps, so the same moment is represented with a value one thousand times larger.

## Seconds versus milliseconds

| Unit | Example shape | Common use |
|---|---|---|
| Seconds | 1700000000 | Unix timestamps in many APIs and logs |
| Milliseconds | 1700000000000 | JavaScript Date values and some APIs |

A common error is passing a millisecond value to a function that expects seconds, or the other way around. The resulting date may be thousands of years away from the expected value.

## Convert a timestamp in JavaScript

JavaScript's Date constructor expects milliseconds:

~~~javascript
const timestampSeconds = 1700000000;
const date = new Date(timestampSeconds * 1000);

console.log(date.toISOString());
~~~

If the input is already milliseconds, do not multiply it again:

~~~javascript
const timestampMilliseconds = 1700000000000;
const date = new Date(timestampMilliseconds);

console.log(date.toISOString());
~~~

The ISO string is expressed in UTC and ends with the Z marker.

## Convert a timestamp online

1. Open the [iamnoobs Timestamp Converter](https://iamnoobs.com/timestamp-converter).
2. Enter the Unix timestamp in the input field.
3. Use the available conversion control to convert it into a date.
4. Review the resulting ISO date and time.
5. Confirm whether your source value was in seconds or milliseconds.

The current converter accepts Unix timestamps and returns an ISO-formatted date. Check the displayed mode and input units before interpreting the result.

## UTC and local time are not the same

UTC is a global time standard. A local timezone expresses the same instant using a region's offset and, in some locations, daylight-saving rules.

For example, an ISO timestamp ending in Z is UTC. A browser may display the same instant in the user's local timezone.

When sharing timestamps across services, storing or transmitting a UTC-based instant avoids ambiguity. Convert to local time only when displaying it to a user or when a business rule explicitly depends on local calendar time.

## Why ISO 8601 is useful

An ISO 8601 timestamp can look like:

~~~text
2026-11-04T12:30:00.000Z
~~~

The format makes the date, time, fractional seconds, and timezone indication explicit. It is widely used in JSON APIs and logs.

A date without a timezone can be ambiguous. A string such as 2026-11-04 12:30 does not by itself say whether the time is UTC or local.

## Common timestamp mistakes

### 1. Mixing seconds and milliseconds

Check the API documentation or data source. Do not assume the number's unit based only on its length.

### 2. Ignoring timezone information

A UTC timestamp and a local clock reading can show different hour values while referring to the same moment.

### 3. Confusing a date with an instant

A birthday or calendar date may not need a timezone. A log event does represent a moment in time. Choose the correct data model for the problem.

### 4. Treating a timestamp as human-readable

A timestamp needs context: unit, epoch, timezone, and sometimes the precision used.

### 5. Assuming every system uses Unix time

Some databases and applications use other epochs, date serials, or structured date types. Check the specification before converting.

## Frequently asked questions

### What is the Unix epoch?

It is 1970-01-01 at 00:00:00 UTC. Unix timestamps commonly count seconds from this reference point.

### Why is my converted date in the wrong year?

A common cause is using milliseconds where seconds are expected, or seconds where milliseconds are expected. Verify the source unit first.

### Does a Unix timestamp contain a timezone?

The standard Unix timestamp represents an instant relative to the UTC epoch. The timezone usually enters when the value is converted to a local calendar display.

## Further reading

- [MDN: Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
- [MDN: Date.now()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/now)
- [RFC 3339: Date and Time on the Internet](https://www.rfc-editor.org/rfc/rfc3339)

---

**Try it:** [iamnoobs Timestamp Converter](https://iamnoobs.com/timestamp-converter)
