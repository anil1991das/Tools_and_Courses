---
title: "Linker Scripts Explained: Memory Sections, Symbols, and Embedded Firmware Layout"
description: "Learn how linker scripts place code and data into flash and RAM, define memory regions, and support startup initialization in embedded systems."
slug:"embedded-linker-script-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems"
tags:
  - "Linker Script"
  - "Embedded C"
  - "Flash Memory"
  - "RAM"
  - "Toolchain"
relatedTools: []
relatedArticles:
  - "microcontroller-startup-code-guide"
  - "embedded-bootloader-firmware-update-guide"
draft: false
---

# Linker Scripts Explained: Memory Sections, Symbols, and Embedded Firmware Layout

An embedded executable must be placed into the memory available on the target device. Code may execute from flash, while variables and stacks use RAM. A linker script tells the linker how to organize sections and assign addresses.

Understanding the linker script helps when debugging memory overflow, startup faults, bootloader integration, and application image layout.

## What does the linker do?

The compiler translates source files into object files. The linker combines object files and libraries, resolves symbols, and produces an executable image.

For embedded targets, the linker must place sections into physical memory regions such as flash and RAM.

The linker script defines the memory layout and section-placement rules.

## Common memory regions

A microcontroller may contain:

- Flash or ROM for executable code and constants.
- SRAM for runtime data and stacks.
- Retention RAM for selected low-power behavior.
- External RAM or memory-mapped devices.
- Special sections for vectors, boot metadata, or calibration data.

The device memory map and product architecture determine the available regions.

## Common sections

| Section | Typical contents |
|---|---|
| .text | Executable code |
| .rodata | Read-only constants |
| .data | Initialized writable global/static variables |
| .bss | Zero-initialized or uninitialized static-storage variables |
| .isr_vector | Interrupt vector table, on common MCU toolchains |
| .noinit | Data intentionally not cleared by ordinary startup, where configured |
| Stack and heap regions | Runtime memory reservation |

Exact section names and conventions depend on the compiler, linker, ABI, and startup implementation.

## Example memory declarations

A GNU ld linker script can define memory regions:

~~~ld
MEMORY
{
    FLASH (rx) : ORIGIN = 0x08000000, LENGTH = 512K
    RAM   (rwx): ORIGIN = 0x20000000, LENGTH = 128K
}
~~~

These addresses and sizes are examples only. Replace them with the target device's memory map.

## Place sections into regions

A simplified section layout may look like:

~~~ld
SECTIONS
{
    .text :
    {
        *(.isr_vector)
        *(.text*)
        *(.rodata*)
    } > FLASH

    .data :
    {
        *(.data*)
    } > RAM AT > FLASH

    .bss :
    {
        *(.bss*)
        *(COMMON)
    } > RAM
}
~~~

The .data section has a runtime address in RAM but an initial load image in flash. Startup code copies the initial values from the load address to the RAM execution address.

The .bss section is normally cleared to zero during startup.

This example omits many details required by real projects, such as alignment, stack reservation, heap policy, constructor sections, exception tables, and platform-specific symbols.

## Linker symbols

Linker scripts can define symbols that startup code or other components use to locate sections.

Examples include:

- Start and end of .data.
- Load address of .data.
- Start and end of .bss.
- Heap start and end.
- Stack boundaries.
- Image boundaries.

These symbols are commonly referenced through extern declarations in C, but their exact names and types depend on the linker script and toolchain.

## Inspect the map file

A linker map file helps identify where symbols and sections are placed.

Use it to investigate:

- Flash and RAM consumption.
- Unexpected library inclusion.
- Section placement.
- Memory region overflow.
- Duplicate or unresolved symbols.
- Alignment and padding.
- Bootloader/application boundary violations.

Map files are useful evidence during memory-budget reviews and release validation.

## Common linker errors

### Region overflowed

The combined sections do not fit in the configured memory region. Review code size, constants, libraries, and memory layout.

### Section assigned to the wrong region

A section may be placed in RAM or flash incorrectly. Review the section rules and memory attributes.

### Missing startup symbols

Startup code may expect linker-defined symbols that are not present or have been renamed.

### Incorrect application offset

When an application runs behind a bootloader, its vector table and image placement must match the bootloader's expected address.

### Overlapping memory regions

Incorrect ORIGIN and LENGTH values can cause two sections to occupy the same physical range.

## Common mistakes

### Copying an example memory map

Always use the actual MCU or SoC reference manual and memory configuration.

### Forgetting initialized data copying

The .data section needs initial contents copied from its load image into RAM before normal application use.

### Ignoring section alignment

Hardware and ABI requirements may need specific alignment for vectors, stacks, DMA buffers, or code.

### Not reviewing map files

A successful link does not prove the image is placed correctly for the hardware architecture.

## Frequently asked questions

### Is a linker script the same as a startup file?

No. The linker script defines memory layout and symbols. Startup code performs early runtime initialization, often using symbols generated by the linker.

### Does every embedded compiler use GNU ld scripts?

No. Toolchains use different linker configuration formats and tools. The concepts are similar, but syntax and symbol conventions differ.

### Can a linker script reserve memory for a bootloader?

Yes. It can define application memory regions and place sections at a configured offset, subject to the bootloader and image-format requirements.

## Further reading

- [GNU ld: Linker Scripts](https://sourceware.org/binutils/docs/ld/Scripts.html)
- [GNU ld: MEMORY command](https://sourceware.org/binutils/docs/ld/MEMORY.html)
- [GCC documentation](https://gcc.gnu.org/onlinedocs/)

---
