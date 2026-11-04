---
title: "DMA in Embedded Systems Explained: Transfers, Channels, and Interrupts"
description: "Learn how Direct Memory Access transfers data between peripherals and memory, compare DMA with CPU-driven I/O, and understand buffers and completion handling."
slug:"dma-embedded-systems-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems"
tags:
  - "DMA"
  - "Embedded Systems"
  - "Microcontrollers"
  - "Memory"
relatedTools: []
relatedArticles:
  - "interrupt-handling-embedded-systems-guide"
  - "dma-cache-coherency-guide"
draft: false
---

# DMA in Embedded Systems Explained: Transfers, Channels, and Interrupts

Microcontrollers frequently move data between memory and peripherals. A UART may receive bytes continuously, an ADC may generate samples periodically, or an SPI controller may transfer a large buffer.

Direct Memory Access (DMA) allows a dedicated hardware engine to transfer data without requiring the CPU to move every item individually. This can reduce CPU overhead and help meet throughput requirements.

## What is DMA?

DMA is a hardware mechanism that moves data between memory and peripherals, or between memory regions where supported, under software-configured control.

The CPU typically configures the DMA transfer, including:

- Source address.
- Destination address.
- Transfer length.
- Data width.
- Increment or fixed-address mode.
- Burst and priority settings.
- Transfer direction.
- Completion and error interrupts.

After the transfer starts, the DMA controller performs the configured movement while the CPU can execute other work.

The exact capabilities depend on the MCU or SoC DMA engine.

## CPU-driven transfer versus DMA

With CPU-driven I/O, software reads or writes each data item through a peripheral register or memory interface.

With DMA, the CPU configures a transfer and the DMA engine performs the data movement.

| CPU-driven I/O | DMA |
|---|---|
| CPU handles individual data movement | DMA hardware moves the configured data |
| Simple for small or infrequent transfers | Useful for repeated or large transfers |
| CPU load increases with transfer volume | Can reduce CPU involvement |
| Timing depends on CPU execution | Hardware transfer timing depends on bus and DMA configuration |
| Often fewer buffer-coherency concerns | Requires ownership and coherency management |

DMA is not always better. For tiny transfers, setup overhead can exceed the benefit.

## Common DMA use cases

- UART receive and transmit buffers.
- SPI and I2C data transfers where supported.
- ADC sample collection.
- Audio and video data movement.
- Ethernet packet buffers.
- Memory-to-memory copies.
- Sensor data acquisition.
- High-throughput communication interfaces.

DMA is especially valuable when data arrives or must be transmitted faster than the CPU can efficiently handle with polling or per-byte interrupts.

## Typical DMA transfer flow

A simplified sequence is:

1. Software prepares a source or destination buffer.
2. Software configures the DMA channel.
3. The peripheral or software triggers the transfer.
4. DMA moves data according to configured settings.
5. DMA reports completion or an error.
6. Software processes the buffer or prepares the next transfer.

Some peripherals use request lines to trigger individual DMA beats. Others support linked descriptors or scatter-gather transfers.

## Buffer ownership

DMA and CPU software may access the same memory region. The software must define which side owns the buffer at each point.

For a receive operation, the CPU may hand a buffer to DMA and avoid modifying it until the transfer completes. When DMA signals completion, software can process the received data and then return the buffer to DMA.

For transmit, software prepares the contents, transfers ownership to DMA, and must not modify the buffer until the transfer has finished unless the hardware supports a suitable streaming mechanism.

Incorrect ownership can cause corrupted or inconsistent data.

## Circular and double buffering

Some DMA engines support circular mode, where the transfer automatically wraps around a buffer.

This is useful for continuous data acquisition, but software must detect which part of the buffer is ready and avoid reading a region while DMA is writing it.

Double buffering uses two buffers so DMA can fill one while the CPU processes the other. This can reduce data loss when the processing time is bounded and the buffer handoff is correct.

## DMA interrupts

A DMA controller can signal transfer completion, half-transfer, or error conditions through interrupts.

An ISR should usually acknowledge the event and notify a task or deferred processing context. Long data processing should not be performed in a high-priority interrupt unless timing analysis justifies it.

Some DMA APIs provide callbacks or interrupt-service routines as part of the driver. Follow the MCU and RTOS documentation for safe execution contexts.

## Cache coherency

On systems with data caches, DMA may read or write main memory while the CPU holds a different copy of the data in cache.

This can cause stale data or overwritten updates unless the platform's cache-coherency rules are followed.

Depending on the architecture, software may need to clean cache lines before a DMA read, invalidate cache lines after a DMA write, or use coherent memory regions.

Cache operations must be aligned and sized according to the architecture requirements. See the separate guide on DMA cache coherency.

## Common DMA mistakes

### Incorrect transfer length

A unit may be bytes, words, or peripheral data elements. Check the DMA API and hardware register definitions.

### Buffer lifetime ends too early

A stack-local or reused buffer must remain valid until the DMA transfer completes.

### CPU modifies an active DMA buffer

Define ownership and synchronization to avoid concurrent modification.

### Ignoring alignment and memory-region restrictions

Some DMA engines can access only selected memory regions or require specific address alignment.

### Ignoring cache effects

On cached systems, correct addresses and transfer counts are not enough. Follow the architecture-specific cache maintenance rules.

## Frequently asked questions

### Does DMA eliminate CPU involvement?

No. The CPU configures and manages transfers, handles completion and errors, and processes data. DMA reduces per-item CPU work.

### Can DMA transfer between two memory addresses?

Many DMA engines support memory-to-memory transfers, but availability and configuration depend on the MCU or SoC.

### Is DMA always faster than CPU copying?

Not necessarily. DMA setup, arbitration, bus contention, and transfer size affect performance. Benchmark the relevant workload.

## Further reading

- [STMicroelectronics: DMA documentation](https://www.st.com/)
- [Zephyr: DMA API](https://docs.zephyrproject.org/latest/hardware/peripherals/dma.html)
- [Arm: Cortex-M generic user guides](https://developer.arm.com/documentation/)

---
