---
title: "AUTOSAR Mode Management Explained: BswM, EcuM, and ComM"
description: "Understand AUTOSAR ECU state management, communication modes, BswM rule evaluation, and how mode requests coordinate ECU behavior."
slug: "autosar-mode-management-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR"
  - "BswM"
  - "EcuM"
  - "ComM"
  - "Mode Management"
relatedTools: []
relatedArticles:
  - "autosar-classic-architecture-guide"
draft: false
---

# AUTOSAR Mode Management Explained: BswM, EcuM, and ComM

An automotive ECU has different operating states. It may start up, initialize its software, communicate on vehicle networks, enter a low-power mode, or shut down.

AUTOSAR Classic provides modules for coordinating these states. BswM, EcuM, and ComM have related but distinct responsibilities.

## Why mode management is needed

The ECU needs to coordinate many activities:

- Driver and BSW initialization.
- Communication availability.
- Network wake-up and sleep.
- Application mode changes.
- Diagnostic sessions or other operating conditions.
- Controlled shutdown and resource deinitialization.

Without clear mode coordination, one module may request communication while another module is preparing the ECU for shutdown.

## What is EcuM?

The ECU State Manager (EcuM) manages ECU startup, run, sleep, and shutdown-related state transitions according to the configured architecture.

Its responsibilities can include:

- Startup sequencing.
- Initialization coordination.
- Wake-up source handling.
- Run and post-run requests.
- Shutdown target selection.
- Sleep and restart coordination.

The exact interfaces and sequence depend on the AUTOSAR release and ECU configuration.

## What is ComM?

The Communication Manager (ComM) manages communication modes and communication requests for configured communication channels.

It provides a standardized way for users to request communication behavior without directly controlling every network interface.

ComM commonly manages modes such as no communication, silent communication, and full communication, subject to network and module support.

## What is BswM?

The Basic Software Mode Manager (BswM) evaluates configured mode conditions and triggers configured actions based on rules.

Inputs can include mode requests or indications from other modules. Actions may include requesting mode changes, enabling or disabling communication-related behavior, or coordinating BSW transitions.

BswM uses generated configuration. The rule and action design should be reviewed carefully because a wrong condition can cause an unexpected system-wide mode transition.

## How the modules work together

A simplified conceptual relationship is:

~~~text
Application / BSW Mode Requests
              |
       ComM / EcuM / Other
              |
            BswM
              |
     Configured Rule Evaluation
              |
       Mode-related Actions
              |
      BSW and ECU State Changes
~~~

This is a conceptual view, not a universal call sequence. EcuM, ComM, BswM, and other managers interact through AUTOSAR-defined interfaces according to the configured system.

## Communication modes

ComM provides communication mode management for network channels. A user may request communication, and the communication mode can change according to the channel state and network management behavior.

A diagnostic tool, application, or BSW module may influence communication requests through the configured ComM user model.

Do not assume that a Full Communication request means frames can be transmitted immediately. Controller, transceiver, network management, and lower-layer readiness also matter.

## Startup and shutdown coordination

During startup, the ECU initializes hardware and software modules according to the configured sequence. Mode managers coordinate transitions to operational states.

During shutdown, the ECU must consider active communication requests, outstanding work, wake-up sources, and shutdown targets.

Incorrect sequencing can produce communication failures, missed wake-up behavior, or a system that cannot enter its intended low-power state.

## Debugging mode transitions

When an ECU remains in an unexpected mode, examine:

1. The source of the mode request.
2. The current mode indication.
3. BswM rule conditions.
4. Configured action lists.
5. ComM channel state and user requests.
6. EcuM state and run requests.
7. Network management state.
8. CAN controller and transceiver modes.
9. Startup or shutdown sequencing.

Use trace and mode-indication callbacks to determine which transition did or did not occur.

## Common integration mistakes

### Confusing a request with an indication

A request asks a module to change state. An indication reports the current or changed state. They are not interchangeable.

### Assuming BswM owns every mode

BswM coordinates configured rules and actions. Other modules still own their defined state machines and interfaces.

### Ignoring outstanding communication requests

A channel may remain active because a user has not released its communication request.

### Triggering shutdown too early

The ECU may still have active tasks, communication, or wake-up conditions. Follow the configured shutdown sequence.

## Testing mode management

A test plan can cover:

- ECU startup transitions.
- Run and post-run request handling.
- Communication mode requests and indications.
- Network wake-up and sleep.
- BswM rules and action lists.
- Release of communication requests.
- Shutdown and restart behavior.
- Concurrent mode requests.
- Invalid or unsupported transitions.
- Recovery after reset or communication faults.

## Frequently asked questions

### Is BswM the same as EcuM?

No. EcuM manages ECU state and lifecycle behavior. BswM evaluates configured mode conditions and coordinates actions.

### Does ComM control the CAN controller directly?

ComM manages communication modes at a higher level. Lower communication and controller modules perform the relevant hardware operations through configured interfaces.

### Does Full Communication guarantee network transmission?

No. Other modules and hardware must also be ready, and network-specific conditions can still prevent transmission.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [AUTOSAR Specifications](https://www.autosar.org/standards/classic-platform/)
- [Vector: AUTOSAR Basic Software](https://www.vector.com/int/en/know-how/technologies/autosar/)

---
