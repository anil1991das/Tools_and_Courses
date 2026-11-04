---
title: "DMA Cache Coherency Explained: Clean, Invalidate, and Buffer Ownership"
description: "Understand why CPU caches can disagree with DMA transfers and learn when cache clean and invalidate operations are required on embedded systems."
slug:"dma-cache-coherency-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems"
tags:
  - "DMA"
  - "Cache Coherency"
  - "Embedded Systems"
  - "Memory"
  - "ARM"
relatedTools: []
relatedArticles:
  - "dma-embedded-systems-explained"
  - "memory-mapped-io-registers-guide"
draft: false
---

# DMA Cache Coherency Explained: Clean, Invalidate, and Buffer Ownership

A DMA controller transfers data between memory and peripherals without requiring the CPU to copy each item. On systems with data caches, the CPU may hold a cached copy of a memory region while DMA accesses main memory directly.

If the CPU cache and main memory contain different values, software may read stale data or overwrite a completed DMA result.

This is a cache-coherency problem and must be handled according to the processor architecture and memory system.

## Why cache and DMA can disagree

Consider a CPU writing a transmit buffer:

1. CPU modifies the buffer.
2. The modified data remains in a dirty cache line.
3. DMA reads the buffer from main memory.
4. Main memory still contains old values.
5. The peripheral transmits stale data.

A similar issue can happen on receive:

1. DMA writes new data into main memory.
2. The CPU still has an old copy in its cache.
3. CPU reads the buffer.
4. Software sees stale values rather than the DMA result.

The exact behavior depends on whether the DMA engine is coherent with the CPU cache and how the memory region is configured.

## What is cache clean?

A cache clean operation writes dirty cache data back to the point of coherency or to the required memory level, depending on the architecture.

For a non-coherent DMA transmit buffer, software commonly cleans the relevant cache lines before DMA reads the buffer.

The purpose is to ensure that the data in memory reflects the CPU's latest writes before DMA accesses it.

## What is cache invalidate?

An invalidate operation marks cache lines as invalid so future CPU reads fetch data again from the appropriate memory system.

For a non-coherent DMA receive buffer, software commonly invalidates the relevant cache lines after DMA has completed writing the buffer and before the CPU reads it.

The precise sequence must follow the architecture's cache and DMA rules.

## Typical transmit sequence

A simplified transmit flow is:

1. CPU prepares data in the buffer.
2. CPU ensures the buffer is no longer being modified.
3. Clean the relevant cache lines if required.
4. Apply any required memory barrier.
5. Start DMA.
6. Wait for or receive completion.
7. Reuse the buffer only when ownership is returned.

## Typical receive sequence

A simplified receive flow is:

1. CPU prepares or relinquishes the receive buffer.
2. Apply required pre-DMA cache maintenance so dirty cache data cannot later overwrite DMA output.
3. Apply required barriers.
4. Start DMA.
5. Wait for completion.
6. Invalidate the relevant cache lines if required.
7. Apply required barriers and read the received data.

The exact pre- and post-DMA operations depend on the platform and whether the buffer may share cache lines with unrelated CPU data.

## Cache-line alignment matters

Cache maintenance operates on cache lines, not arbitrary individual bytes on many architectures.

If a DMA buffer shares a cache line with unrelated data, invalidating that line may discard CPU updates to the unrelated data.

For this reason, DMA buffers are often aligned and sized to cache-line boundaries, with padding or dedicated memory regions as needed.

Use the cache-line size and alignment rules for the specific processor. Do not assume one universal line size.

## Non-cacheable or coherent memory

Some systems provide non-cacheable memory regions or coherent DMA allocations.

Using these regions can simplify some cache-maintenance requirements, but may affect CPU performance or have other platform constraints.

A coherent DMA allocation API can establish the appropriate mapping and synchronization rules. Prefer the operating system or platform's DMA APIs when available rather than implementing cache control manually.

## Memory barriers

Cache clean and invalidate operations do not automatically replace all required memory-ordering barriers.

A barrier may be needed to ensure that memory operations and device register writes occur in the required order.

Use the architecture-specific functions and driver APIs supplied by the platform. A compiler-only barrier does not necessarily order hardware transactions.

## Debugging cache-coherency defects

Symptoms can include:

- DMA transmits old data.
- The first transfer works but later transfers fail.
- Data becomes correct when cache is disabled.
- Only buffers crossing certain alignment boundaries fail.
- Receive data appears stale.
- Intermittent corruption occurs under load.

To investigate:

1. Confirm whether DMA is cache coherent.
2. Check memory attributes for the buffer region.
3. Inspect buffer alignment and size.
4. Verify ownership transitions.
5. Review cache maintenance sequence.
6. Check barriers and DMA completion handling.
7. Test with cache enabled and representative load.

Disabling the data cache can help isolate a problem during debugging, but it is not necessarily an acceptable production solution.

## Common mistakes

### Cleaning a receive buffer after DMA

For a receive operation, cleaning a stale dirty CPU cache line after DMA has written memory can overwrite the new data. Follow the platform's receive-buffer sequence.

### Invalidating before transmit without cleaning

Invalidating dirty cache lines can discard data the CPU intended DMA to transmit. Use the correct operation for the direction and platform.

### Sharing cache lines with unrelated data

Cache maintenance may affect neighboring bytes in the same line. Align and isolate DMA buffers.

### Ignoring coherent DMA APIs

Operating systems and SoC SDKs often provide allocation and mapping helpers that implement platform-specific requirements.

### Assuming volatile solves coherency

volatile does not clean, invalidate, or synchronize CPU caches with DMA.

## Frequently asked questions

### Does every microcontroller need DMA cache maintenance?

No. Some microcontrollers have no data cache, and some systems provide coherent DMA access. Check the target memory architecture.

### Should I always invalidate after DMA receive?

Not universally. The exact sequence depends on the platform, memory mapping, and buffer ownership. Follow the processor and SDK documentation.

### Can cache coherency cause intermittent bugs?

Yes. Timing, cache-line alignment, and buffer reuse can make these defects intermittent and difficult to reproduce.

## Further reading

- [Arm: Cache architecture and maintenance](https://developer.arm.com/documentation/)
- [Linux Kernel: Dynamic DMA mapping Guide](https://docs.kernel.org/core-api/dma-api-howto.html)
- [CMSIS documentation](https://arm-software.github.io/CMSIS_6/latest/)

---
