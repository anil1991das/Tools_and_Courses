---
title: "UART Communication Explained: Baud Rate, Data Bits, Parity, and Stop Bits"
description: "Learn how UART asynchronous serial communication works, configure common frame formats, and troubleshoot baud-rate and wiring problems."
slug:"uart-communication-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Embedded Systems"
tags:
  - "UART"
  - "Serial Communication"
  - "Microcontrollers"
  - "Embedded C"
relatedTools: []
relatedArticles:
  - "spi-vs-i2c-guide"
draft: false
---

# UART Communication Explained: Baud Rate, Data Bits, Parity, and Stop Bits

UART is a common serial communication interface in embedded systems. It is used for debug consoles, communication between microcontrollers, modem interfaces, bootloaders, and peripheral connections.

UART sends data serially without a separate clock line. Both endpoints must agree on the communication parameters so that the receiver can sample each bit at the correct time.

## What is UART?

UART stands for Universal Asynchronous Receiver/Transmitter. It is a hardware peripheral that converts parallel data to serial form for transmission and serial data back to parallel form for reception.

A basic UART connection commonly uses:

- TX: transmit data.
- RX: receive data.
- GND: common reference.

The TX pin of one device normally connects to the RX pin of the other, and vice versa. Signal voltage levels must be compatible.

UART logic signals alone are not the same as RS-232 or RS-485 electrical interfaces. Those standards use additional transceiver hardware and different electrical characteristics.

## How asynchronous serial framing works

UART frames commonly contain:

1. A start bit.
2. A configured number of data bits.
3. An optional parity bit.
4. One or more stop bits.

A common configuration is 8-N-1:

- 8 data bits.
- No parity.
- 1 stop bit.

The line is normally idle at the configured logic level. A start bit indicates the beginning of a frame. The receiver uses timing to sample the following bits.

## What is baud rate?

Baud rate describes the number of symbol changes or symbols transmitted per second. In common binary UART signaling, one symbol represents one bit, so baud rate and bit rate are often numerically equal.

Both endpoints must use compatible baud-rate settings. A mismatch can produce corrupted characters or framing errors.

Common example settings include 9600, 115200, and higher values, depending on the hardware and application.

## Data bits, parity, and stop bits

### Data bits

Many UART devices use 7 or 8 data bits, though hardware can support other configurations.

### Parity

Parity adds a simple error-detection bit. Even parity and odd parity are common options.

Parity can detect some single-bit errors within the protected frame but does not provide robust error detection or correction.

### Stop bits

Stop bits provide a defined interval between frames and help the receiver resynchronize. One or two stop bits are common options.

## Flow control

When a receiver cannot accept data continuously, flow control can prevent buffer overflow.

Hardware flow control commonly uses RTS and CTS signals. Software flow control may use special control characters such as XON and XOFF, where suitable for the data protocol.

Not every UART connection uses flow control. Confirm that both devices and the application support the same scheme.

## UART versus SPI and I2C

| Feature | UART | SPI | I2C |
|---|---|---|---|
| Clock line | No separate clock | Yes | Yes |
| Device addressing | Usually point-to-point or protocol-specific | Chip select | Address field |
| Main lines | TX, RX, GND | Clock and data lines plus CS | SDA, SCL |
| Duplex | Separate TX and RX can support full duplex | Commonly full duplex | Half duplex data direction |
| Typical use | Console and serial links | High-speed peripherals | Shared low-pin-count peripherals |

These are interface-level distinctions. Longer-distance UART links commonly use a transceiver standard such as RS-485.

## Polling, interrupts, and DMA

UART drivers may use different transfer methods:

- Polling: the CPU checks whether data can be read or written.
- Interrupt-driven: hardware interrupts notify the CPU when transmit or receive events occur.
- DMA: a DMA controller transfers data between memory and the UART peripheral with less CPU involvement.

The best method depends on throughput, latency, buffer size, CPU load, and driver support.

Some RTOS driver APIs do not allow interrupt-driven and DMA-based asynchronous modes to be enabled simultaneously for the same peripheral. Follow the target driver documentation.

## Troubleshooting UART communication

Check these items in order:

1. TX and RX wiring is crossed correctly.
2. Grounds are connected where required.
3. Voltage levels are compatible.
4. Both endpoints use the same baud rate.
5. Data bits, parity, and stop bits match.
6. Flow-control settings match.
7. The correct UART instance and pins are configured.
8. Buffer sizes and receive timeouts are adequate.
9. The receiving software handles partial data and frame boundaries.

A UART peripheral transports bytes. It does not automatically define message boundaries for higher-level application messages. The application protocol needs delimiters, length fields, timeouts, or another framing mechanism.

## Common mistakes

### Connecting TX to TX

For a basic two-device UART link, connect one side's TX to the other side's RX.

### Confusing UART with RS-232 voltage levels

MCU UART pins are usually logic-level signals. RS-232 requires a suitable transceiver and uses different voltage levels.

### Ignoring common ground

A voltage reference is needed for ordinary single-ended UART wiring unless the interface design provides isolation or another reference method.

### Assuming each read returns a full message

UART data arrives as a stream of bytes. A software read can return only part of an application message.

### Using the wrong baud rate

A mismatch can result in unreadable characters, framing errors, or no useful communication.

## Frequently asked questions

### Is UART full duplex?

A UART with separate transmit and receive lines can transmit and receive simultaneously. Some interfaces use only one direction or combine data lines through another physical-layer arrangement.

### Does UART require a clock wire?

No. It is asynchronous and relies on agreed timing parameters at both endpoints.

### Can UART be used over long distances?

The logic-level UART connection is generally intended for shorter board-level links. RS-232 and RS-485 transceivers enable different electrical interfaces and distance capabilities.

## Further reading

- [Zephyr: UART](https://docs.zephyrproject.org/latest/hardware/peripherals/uart.html)
- [Microchip: USART peripheral overview](https://www.microchip.com/en-us/products/microcontrollers-and-microprocessors/8-bit-mcus/peripherals/serial-communication/usart)
- [NXP: UART application documentation](https://www.nxp.com/)

---
