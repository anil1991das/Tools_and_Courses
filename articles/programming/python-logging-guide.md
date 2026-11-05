---
title: "Python Logging Explained: Log Levels, Handlers, Formatters, and Files"
description: "Learn how Python's logging module records application events, configure log levels and handlers, and build useful logs for debugging and production."
slug: "python-logging-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "Logging"
  - "Debugging"
  - "Observability"
relatedTools: []
relatedArticles:
  - "python-exception-handling-guide"
draft: false
---

# Python Logging Explained: Log Levels, Handlers, Formatters, and Files

Logging records events that happen while an application runs. It helps developers investigate failures, understand application behavior, and monitor production systems.

Python includes the logging module in its standard library. It supports severity levels, multiple output destinations, reusable configuration, and integration between application modules.

## Why use logging instead of print?

print() is useful for quick exploration, but it does not provide a structured application logging system.

Logging supports:
- Severity levels.
- Timestamps and contextual metadata.
- Console and file output.
- Different formatting per destination.
- Configurable filtering.
- Integration with external log collectors.

Logging also allows an application to keep diagnostic statements in place while controlling which events are emitted in each environment.

## Logging levels

Python provides standard levels:

| Level | Typical purpose |
|---|---|
| DEBUG | Detailed information for diagnosis |
| INFO | Normal application events |
| WARNING | Unexpected situation that does not stop operation |
| ERROR | An operation failed |
| CRITICAL | Serious failure that may prevent the application from continuing |

The configured threshold determines which records are processed by a handler. For example, an INFO threshold normally includes INFO, WARNING, ERROR, and CRITICAL events.

## Basic logging

~~~python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s: %(message)s"
)

logging.debug("Detailed diagnostic information")
logging.info("Application started")
logging.warning("Cache is nearly full")
logging.error("Unable to process request")
~~~

The DEBUG message is filtered because the configured level is INFO.

Call basicConfig early in a simple application. In larger systems, configure logging centrally rather than having every module configure the root logger independently.

## Use a named logger

A module should commonly create a logger using __name__:

~~~python
import logging

logger = logging.getLogger(__name__)

def load_configuration(path):
    logger.info("Loading configuration from %s", path)
~~~

Named loggers form a hierarchy based on their dotted names. This lets an application adjust logging behavior for individual modules or packages.

Use the logging API's argument formatting rather than eagerly building strings with concatenation. The message can then be formatted when the record is actually emitted.

## Log exceptions with tracebacks

Use logger.exception() inside an exception handler when a traceback is useful:

~~~python
try:
    load_configuration()
except OSError:
    logger.exception("Configuration loading failed")
~~~

logger.exception() includes exception information at ERROR level. Do not expose internal stack traces to untrusted users; logs should be protected and access-controlled.

## Handlers and formatters

A logger creates LogRecord objects. Handlers decide where records go, and formatters define how they are rendered.

Common handlers include:
- StreamHandler for console or stream output.
- FileHandler for a file.
- RotatingFileHandler for size-based rotation.
- TimedRotatingFileHandler for time-based rotation.
- QueueHandler and QueueListener for queue-based logging arrangements.

Example file logging:

~~~python
import logging

logger = logging.getLogger("service")
logger.setLevel(logging.INFO)

handler = logging.FileHandler("service.log", encoding="utf-8")
handler.setFormatter(
    logging.Formatter("%(asctime)s %(levelname)s %(message)s")
)

logger.addHandler(handler)
~~~

In reusable packages, avoid adding handlers automatically. Let the application decide how logs should be routed.

## Avoid duplicate log messages

Log records can propagate from a child logger to parent loggers. Adding handlers to both a child and the root logger may cause duplicate output.

Choose one clear configuration strategy. In most applications, configure handlers centrally and allow module loggers to propagate unless there is a specific reason not to.

## Structured logging and context

Production systems benefit from fields such as:
- Timestamp.
- Severity.
- Service name.
- Environment.
- Request or correlation ID.
- Operation name.
- Relevant non-sensitive identifiers.

JSON logging can make records easier for log platforms to parse. Python's standard logging system can be extended with custom formatters and filters, or a structured-logging library can be used.

Never place passwords, access tokens, session secrets, or sensitive personal data into logs.

## Configure logging by environment

A development environment may emit DEBUG messages to the console, while production may use INFO or WARNING with centralized collection.

Avoid hardcoding secrets or environment-specific paths in logging configuration. Ensure log files have appropriate permissions, retention, and rotation.

## Common mistakes

### Using print for production diagnostics

Use named loggers so severity, routing, and filtering can be managed consistently.

### Logging sensitive information

Redact credentials and personal information. Logs often have broader access and longer retention than application data.

### Configuring handlers in every module

Central configuration avoids duplicated output and inconsistent formatting.

### Catching exceptions and logging nothing

Preserve useful diagnostics when an error is handled. Do not silently discard unexpected failures.

### Logging every loop iteration

Excessive logs add storage, processing, and noise. Choose useful events and appropriate levels.

## Frequently asked questions

### What is the default logging level?

The root logger's default threshold is WARNING unless configuration changes it.

### Should every Python module use a logger?

Reusable modules commonly use getLogger(__name__) and leave handler configuration to the application.

### Does logging automatically rotate files?

No. Use a rotating handler or an external log-management system when file rotation is needed.

## Further reading

- [Python: logging](https://docs.python.org/3/library/logging.html)
- [Python Logging HOWTO](https://docs.python.org/3/howto/logging.html)
- [Python Logging Cookbook](https://docs.python.org/3/howto/logging-cookbook.html)

---
