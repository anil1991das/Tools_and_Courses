---
title: "SPI vs I2C: Compare Two Common Embedded Communication Buses"
description: "Compare SPI and I2C wiring, addressing, timing, speed, topology, and design trade-offs for connecting sensors and peripherals to microcontrollers."
slug:"spi-vs-i2c-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems"
tags:
  - "SPI"
  - "I2C"
  - "Microcontrollers"
  - "Embedded Systems"
  - "Communication Protocols"
relatedTools: []
relatedArticles:
  - "uart-communication-explained"
draft: false
---

# SPI vs I2C: Compare Two Common Embedded Communication Buses

Microcontrollers communicate with sensors, memory devices, displays, and other peripheral chips using serial interfaces. SPI and I2C are two widely used board-level communication buses.

Both can connect a controller to multiple peripherals, but their electrical interfaces, addressing methods, timing, and system design differ.

## What is SPI?

Serial Peripheral Interface (SPI) is a synchronous serial bus. A controller provides the clock and selects the peripheral participating in a transfer.

Common signals include:

- SCLK: serial clock.
- SDO: controller-to-peripheral data output, sometimes called MOSI.
- SDI: peripheral-to-controller data output, sometimes called MISO.
- CS: chip select for a peripheral.

Signal naming varies by documentation and role convention. A device's data input and output should be understood from that device's perspective.

SPI can support full-duplex transfers, where data can be transmitted and received during the same clock cycles.

## What is I2C?

Inter-Integrated Circuit (I2C) is a synchronous two-wire bus.

The two main signals are:

- SDA: serial data.
- SCL: serial clock.

The bus uses open-drain or open-collector signaling with pull-up resistors. A controller initiates transactions and addresses a target device. Multiple devices can share the same bus if their addresses and electrical characteristics are compatible.

I2C supports acknowledgement and defined start, stop, and repeated-start conditions.

## SPI versus I2C

| Feature | SPI | I2C |
|---|---|---|
| Main signal lines | Clock, data in, data out, chip select | SDA and SCL |
| Clock | Synchronous | Synchronous |
| Device selection | Often separate chip-select signal | Address in protocol |
| Duplex | Commonly full duplex | Half duplex data direction per transfer phase |
| Addressing | No universal built-in address field | 7-bit and 10-bit addressing |
| Pull-up resistors | Not generally required for push-pull signals | Required for open-drain bus lines |
| Topology | Often one CS per peripheral | Shared two-wire bus |
| Arbitration | Usually not a standard multi-controller bus arbitration model in simple setups | Supports arbitration for multi-controller operation |
| Typical use | Displays, flash, high-rate peripherals | Sensors, EEPROMs, low-pin-count peripherals |

Actual speed and feature support depend on the MCU, peripheral, board layout, and device datasheets.

## SPI clock modes

SPI devices use clock polarity (CPOL) and clock phase (CPHA) to define when data is sampled and shifted.

These parameters produce four common modes:

| Mode | CPOL | CPHA |
|---|---:|---:|
| 0 | 0 | 0 |
| 1 | 0 | 1 |
| 2 | 1 | 0 |
| 3 | 1 | 1 |

The controller and peripheral must agree on the mode and supported clock rate. Incorrect configuration can produce corrupted data even when the wiring is correct.

## I2C addressing and transactions

An I2C transaction commonly begins with a START condition, followed by an address and direction bit, then data bytes with acknowledgement bits.

A transaction can finish with a STOP condition or use a repeated START to continue without releasing the bus.

Address conflicts can occur when two devices use the same fixed address. Some peripherals provide address-select pins; others require a mux or separate bus.

## Electrical considerations

### SPI

SPI signal routing should consider clock frequency, trace length, signal integrity, chip-select timing, and voltage compatibility.

At higher rates, ringing, skew, and loading can create communication errors. Use the signal-integrity guidance from the device datasheets and board design rules.

### I2C

I2C rise time depends on bus capacitance and pull-up resistance. A pull-up that is too weak can produce slow edges; one that is too strong can exceed device sink-current limits.

Calculate or validate pull-up values using the bus capacitance, supply voltage, allowed rise time, and device specifications.

## When should you choose SPI?

SPI is often a good fit when:

- Higher throughput is needed.
- Full-duplex transfers are useful.
- The design can allocate chip-select lines.
- The peripheral supports the required SPI mode and clock.
- The board layout can support the signal timing.

## When should you choose I2C?

I2C is often suitable when:

- Pin count is limited.
- Several low- or moderate-speed peripherals share a bus.
- Device addressing is convenient.
- The sensor or memory device already provides an I2C interface.
- The bus capacitance and pull-up design are manageable.

## Common mistakes

### Using the wrong SPI mode

Check CPOL and CPHA in the peripheral datasheet. Do not assume all SPI devices use mode 0.

### Choosing I2C pull-ups by guesswork

Pull-up resistance must satisfy rise-time and sink-current requirements. Validate the actual bus.

### Ignoring address conflicts

Check every device's address, including hardware-selectable variants.

### Assuming a logic-level match

Confirm voltage compatibility between MCU pins and peripheral inputs. Use level shifters where required.

### Ignoring bus faults

Design for timeouts, stuck lines, failed acknowledgements, and peripheral recovery where the application requires it.

## Frequently asked questions

### Is SPI faster than I2C?

SPI commonly supports higher clock rates and has lower protocol overhead for many devices, but actual performance depends on the controller, peripheral, wiring, and implementation.

### Can multiple devices share SPI?

Yes. A controller can share clock and data lines among peripherals and use separate chip-select signals, or use another supported selection arrangement.

### Can I2C have more than one controller?

Yes. I2C supports multi-controller operation with arbitration, although many embedded products use a single controller for simpler design.

## Further reading

- [NXP: I2C-bus specification and user manual](https://www.nxp.com/docs/en/user-guide/UM10204.pdf)
- [Zephyr: I2C Bus](https://docs.zephyrproject.org/latest/hardware/peripherals/i2c.html)
- [Zephyr: SPI Bus](https://docs.zephyrproject.org/latest/hardware/peripherals/spi.html)

---
