---
title: "AUTOSAR Classic Architecture Explained: Application, RTE, and BSW"
description: "Understand the main layers of AUTOSAR Classic, the role of the RTE and Basic Software, and how MCAL and communication services fit together."
slug: "autosar-classic-architecture-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems and Automotive"
tags:
  - "AUTOSAR"
  - "AUTOSAR Classic"
  - "BSW"
  - "RTE"
  - "MCAL"
  - "Automotive Software"
relatedTools: []
relatedArticles:
  - "can-vs-can-fd-explained"
  - "uds-diagnostics-explained"
draft: false
---

# AUTOSAR Classic Architecture Explained: Application, RTE, and BSW

AUTOSAR Classic is a standardized software architecture used in automotive electronic control units, especially in systems with real-time and safety-related requirements.

It defines a layered architecture and standardized interfaces that help separate application software from hardware-specific implementation and common infrastructure.

Understanding the Application, Runtime Environment (RTE), and Basic Software (BSW) layers is a useful starting point for working with AUTOSAR projects.

## What is AUTOSAR Classic?

AUTOSAR is a global development partnership that defines automotive software architecture and methodology standards. The Classic Platform targets deeply embedded systems with predictable execution behavior and static configuration.

It is commonly used in ECUs that run real-time control applications and communicate with other vehicle systems.

AUTOSAR Classic is not the same as AUTOSAR Adaptive. The Adaptive Platform is designed for a different class of applications and execution environment.

## The main layers

A simplified AUTOSAR Classic architecture contains:

1. Application Layer.
2. Runtime Environment (RTE).
3. Basic Software (BSW).

The BSW is further divided into functional groups and layers, including Services, ECU Abstraction, and Microcontroller Abstraction.

## Application Layer

The Application Layer contains Software Components (SWCs) that implement vehicle functions.

Examples may include:

- Vehicle state management.
- Body control functionality.
- Lighting control.
- Sensor signal processing.
- Powertrain application logic.

An SWC communicates through defined ports and interfaces rather than directly accessing arbitrary hardware drivers.

## Runtime Environment (RTE)

The RTE provides communication between Software Components and between Software Components and BSW services.

It implements the communication behavior described by the AUTOSAR configuration and generated interfaces.

The RTE abstracts communication details from the application software, helping SWCs remain independent of some lower-level implementation details.

## Basic Software (BSW)

Basic Software provides common services and interfaces that applications need.

A simplified BSW breakdown includes:

### Services Layer

Provides common system services such as:

- Operating System services.
- Communication services.
- Diagnostic services.
- Memory services.
- ECU state management.
- Watchdog management.

### ECU Abstraction Layer

Abstracts ECU-specific hardware devices and provides standardized interfaces to upper layers.

It helps isolate application and service modules from certain hardware implementation details.

### Microcontroller Abstraction Layer (MCAL)

MCAL provides low-level access to microcontroller peripherals.

Examples include:

- CAN driver.
- SPI driver.
- ADC driver.
- PWM driver.
- DIO driver.
- ICU driver.
- Microcontroller-specific system support.

MCAL is hardware-dependent and commonly supplied or configured for the selected microcontroller family.

## Communication Stack example

For CAN communication, a simplified AUTOSAR Classic path may include:

~~~text
Application SWC
      |
     RTE
      |
     COM
      |
    PduR
      |
   CanIf
      |
   CanDrv
      |
 CAN Controller
      |
 CAN Transceiver
~~~

The exact module path depends on the communication type, configuration, and stack implementation.

COM handles signal and I-PDU communication abstraction. PduR routes protocol data units. CanIf provides a hardware-independent CAN interface to upper layers. CanDrv accesses the CAN controller through the MCAL layer.

For diagnostics over CAN, CanTp may transport segmented diagnostic messages. Dcm implements diagnostic communication services. The exact routing and module configuration depend on the ECU design.

## BSW module configuration

AUTOSAR modules are configured using standardized descriptions and configuration tools. Configuration defines items such as:

- Channels and hardware controllers.
- PDU identifiers and routing.
- Signal layouts and endianness.
- Timing parameters.
- Diagnostic services and DIDs.
- OS tasks, events, and alarms.
- Memory sections and hardware mappings.

Configuration consistency is important. Incorrect PDU references, mismatched IDs, or invalid timing can cause integration failures even when individual modules compile.

## Generate and integrate code

A typical AUTOSAR development workflow includes:

1. Define system and ECU architecture.
2. Configure Software Components and ports.
3. Configure BSW modules.
4. Generate RTE and BSW configuration code.
5. Integrate application and generated code.
6. Compile and link the ECU software.
7. Run unit, integration, and target tests.
8. Validate communication and timing behavior.

Tooling differs between vendors and OEM workflows. Generated code should be treated as part of the controlled build configuration, not manually edited without a defined process.

## Where OS fits

AUTOSAR Classic commonly uses an AUTOSAR OS implementation. It provides real-time scheduling and synchronization features such as tasks, events, alarms, counters, and resources.

Runnable entities are mapped to execution contexts according to timing and configuration. A developer should understand the task mapping and timing behavior when debugging missed deadlines or unexpected execution order.

## Testing AUTOSAR software

Testing can be performed at different levels:

- Unit testing for individual modules or functions.
- Software integration testing for BSW and application interfaces.
- Communication testing for CAN, LIN, Ethernet, or other configured networks.
- Diagnostic testing for UDS services and timing.
- Target testing on the ECU hardware.
- Safety-related verification according to project requirements.

A communication issue may originate from application signal configuration, COM, PduR, interface modules, MCAL, controller configuration, transceiver, or physical wiring. Debugging should follow the configured path layer by layer.

## Common AUTOSAR misconceptions

### RTE is the same as BSW

RTE is a separate layer that provides communication between SWCs and BSW services. It is not simply another name for BSW.

### MCAL is hardware-independent

MCAL is specifically the microcontroller abstraction and contains hardware-dependent drivers or implementations.

### Every AUTOSAR project has the same BSW configuration

The architecture is standardized, but the selected modules, versions, hardware mapping, and OEM configuration vary by project.

### AUTOSAR automatically guarantees functional safety

AUTOSAR provides standardized architecture and mechanisms, but safety is a system and development-process responsibility. ISO 26262 activities, analyses, verification, and evidence are still required where applicable.

## Frequently asked questions

### What is the difference between AUTOSAR Classic and Adaptive?

Classic targets deeply embedded, predictable real-time ECUs with a layered architecture and static configuration. Adaptive targets higher-performance computing platforms and service-oriented applications.

### What is the role of MCAL?

MCAL provides standardized access to microcontroller peripherals and hardware-specific functionality through defined interfaces.

### Does COM communicate directly with the CAN controller?

In a typical CAN communication path, COM uses PduR and CanIf before the CAN driver and hardware controller. The exact stack path depends on configuration and implementation.

## Further reading

- [AUTOSAR Classic Platform](https://www.autosar.org/standards/classic-platform/)
- [AUTOSAR: Main Requirements and Technical Overview](https://www.autosar.org/)
- [ISO 26262: Road vehicles — Functional safety](https://www.iso.org/standard/68383.html)

---
