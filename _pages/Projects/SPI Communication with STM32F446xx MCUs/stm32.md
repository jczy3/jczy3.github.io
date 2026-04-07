---
title: "SPI Communication with STM32F446xx MCUs"
date: "2026-04-01"
tags: [Embedded Systems, STM32, Communication Protocols, SPI]
thumbnail: "/assets/img/STM32Blog/projectstm32.jpg"
---

# Overview
---
Welcome to my first blog! Here, I will teach you how SPI communication works with STM32F446XX microcontrollers.

Lets start with, what exactly is SPI? Why should we care?

SPI stands for Serial Peripheral Interface. This is a **serial communication protocol** (shared clock involved) used in embedded systems, whether thats a microcontroller communicating with external peripherals such as a sensor, SD card, a display, or even a microcontroller communicating with other microcontrollers. In a typical SPI setup, there is only one master and one or more slaves. SPI is also typically setup as to being full duplex (data being transmitted and received at the same time).

SPI uses 4 GPIO pins: SCLK, MOSI, MISO, and SS.

SCLK is the Serial Clock pin, which determines when the master is to receive/transmit data. This is an output pin for the master and input pin/s for the slave/s, as the SCLK is produced by the master.

MOSI is the data line in which the master transmits data and the slave receives data.

MISO is the data line in which the master receives data and the slave transmits data.

SS is the slave select pin, which allows the master to select an individual slave for communication. Ensure that the master is set as an output pin for SS, and the slave/s are set as input pin/s.

# SPI vs. other communication protocols
---
SPI in comparison to most other protcols, is designed to operate within a short distance (around 10 ft) with a high throughput (more data can be transferred in less time). Take I2C for example, it too is considered a "short distance" protocol (18 ft) and operates at around 3.4 - 5 Mbps in high speed mode, while SPI typically operates around 10 - 50 Mbps. For applications requiring longer distances, opt for protocols that suit this requirement (CAN, ethernet, etc.). Communication protocols are application dependent and are not "one size fits all."

Just a reminder, frequency / bandwith measures the capacity (pipe size). Bits per second measures the throughput (flow rate).

# Important considerations with configuring SPI
---
Both master and slave devices must support the SPI interface. Whenever the master wants to communicate with a slave, the master must select the slave. Via the SS pin, the master pulls the SS pin of the slave to ground (0). If this is not done, the data lines (MOSI and MISO) will be in high impedance state, and the slave will not respond to any data sent by the master.

# SPI bus configurations
---
By default, SPI is a full duplex communication protocol. Shift registers of the master and slave are linked between MOSI and MISO pins. Data is shifted synchronously on the SCLK edges provided by the master. The master transmits data to the slave via the MOSI line and receives data from the slave via the MISO line.

Half duplex configuration: A single cross connection line links the shift registers of the master and slave together. The data is synchronously shifted between the shift registers on the SCLK edges in the transfer direction selected reciprocally by both master and slave (one direction at a time). MOSI of master is connected to the MISO of slave. This must be configured in software: when master is in Tx mode, slave is in Rx mode. When master is in Rx mode, slave is in Tx mode.

Simplex configuration: Tx only / Rx only. The application ignores the information on the unused pin.

# SPI under the hood
---
SPI communication is based on shift registers. After a clock cycle, the master shifts its LSB to the slave's MSB via MOSI, and the slave simulataneously shifts its LSB to the master's MSB via MISO.

I've provided a visualization on how SPI communication uses shift registers for communication between master and slave. Note that in these images, the device on the left is the master (with contents B7-B0 in its shift register) and the device on the right is the slave (with contents A7-A0 in its shift register).

This is what the shift registers look like before a shift.

![](/assets/img/SPIex1.png)

After one clock cycle, master transmits A0 via MOSI to the slave and receives B0 via MISO from the slave.

![](/assets/img/SPIex2.png)

After seven more clock cycles, A7-A0 is transmitted from master to the slave (MOSI), and B7-B0 is transmitted from the slave to the master (MISO).

![](/assets/img/SPIex3.png)

# What does the hardware look like?
---
Block Diagram: 

![](/assets/img/SPIBlockdia.JPG)

For STM32 MCUs, the shift register can hold up to 16 bits.
The frequency of the SCLK is determined by the baud rate generator.

There are also internal Rx and Tx buffers that are accessed by the APB bus. When the shift register receives complete data via MISO (lets say, DFF is 8 bits), the byte will be moved to the Rx buffer which triggers an interrupt, and then the data can be read in firmware. Similarly, data is written to the Tx buffer in firmware and when the shift register is free, the data is loaded into the shift register and transmission begins via MOSI. Whenever Tx buffer is empty, an interrupt occurs so the application can load data.

So, there is an interrupt when the Tx buffer is empty (load data) and an interrupt when the Rx buffer is full (receive data).

# Software slave management
---
For 1 master and 1 slave: You don't need to use the NSS pin of the master and slave in SSM. If you don't want to use SSM, you can connect NSS pin of the master to the NSS pin of the slave.

For 1 master and multiple slaves: You can't use SSM. You can't use the NSS pin of the master to connect to the NSS pin of any of the slaves, the master must use its GPIO pins to control the NSS pins of the slaves (ground the I/O pin of the slave it wishes to use).

# SPI Communication Format
---

There are 3 important considerations for SPI's format, CPHA (clock phase bit), CPOL (clock polarity bit), and DFF (data frame format, either 8 or 16 bits).

By default, CPOL = 0. CPOL being 0 corresponds to a low level idle state. CPOL being 1 corresponds to a high level idle state.

By default, CPHA = 0. CPHA being 0 corresponds to data being sampled on the lead edge of the clock. CPHA being 1 corresponds to data being sampled on the trailing edge of the clock.

There are effectively 4 different modes for SPI, changing CPHA and CPOL (changing DFF is trivial).

# Which bus is my SPI peripheral connected to? 
---

Bus: a shared communication pathway that transfers data, addresses, and control signals between components like the CPU, memory, and peripherals.

We must refer to the reference manual to find this information. 

SPI2 and SPI3 are hanging on the APB1 bus.

![](/assets/img/STM32Blog/APB2.png)

SPI1 and SPI4 are hanging on the APB2 bus.

![](/assets/img/STM32Blog/APB1.png)

# What is the frequency of SCLK?
---

You first need to understand the speed of the bus in which the SPI peripheral is connected to and what clock source is being used. For our purposes, when using the internal RC oscillator clock (HSI) as the system clock, its frequency is 16 MHz. From there, you can adjust the APBx prescaler and the SPIx peripheral prescaler to adjust the frequency of the SCLK. The minimum prescale value for the APBx prescaler is 1, and the minimum prescale value for the SPIx peripheral is 2, so the max frequency of SCLK using the HSI is 8 MHz.

# Where can I find the pins / registers for this SPI peripheral?
---

For the pins, we need to consult the datasheet.

SPI requires a GPIO pin to be in alternate functionality mode, so lets review this section of the data sheet with the pinout and pin description.

![](/assets/img/STM32Blog/pinout.PNG)

As you can see, SPI1's pin's correspond to GPIO pins PA4 - PA7 under alternate functionality mode 5.

For the registers, we need to consult the reference manual.

SPI register information can be found on section 26.7 of the manual.

![](/assets/img/STM32Blog/spi_registers.PNG)

Each register under 26.7 will contain the bit fields for that specific register, as well as what the register is configuring under a specific bit.

![](/assets/img/STM32Blog/bitfield.PNG)

To enable / disable the peripheral clocks of SPI, you will need to consult the APBxENR RCC register.

Let's take SPI1 as an example:

![](/assets/img/STM32Blog/RCC1.PNG)

![](/assets/img/STM32Blog/rcc2.PNG)

To enable the peripheral clock of SPI1, you must set bit 12 of the RCC_APB2ENR register to high. To disable the peripheral clock of SPI1, you must set bit 12 of the RCC_APB2ENR register to low.

# What are common issues when setting up SPI?
---

The most common issue is forgetting to enable the peripheral clock of the respective GPIO port that the SPI peripheral is using, or the peripheral clock of the SPI peripheral itself. If your program isn't working, ensure that these bits (under RCC -> APBxENR) are being set when observing the registers in debug mode.

Also, make sure that after data is sent that the SPI peripheral is disabled. To do this, wait until the SPI flag is not busy (BSY bit of the status register) before disabling.

# Implementation
---
I have implemented the SPI driver for the STM32 F446RE MCU [here](https://github.com/jczy3/STM32F446RE_Drivers). 

# Sending "Hello world"

![](/assets/img/STM32Blog/helloworld.PNG)

When using the logic analyzer on the MOSI and SCLK pins, we see that 11 bytes of data were sent, corresponding to the 11 characters in "Hello world" when loaded into the Tx buffer and sent via the shift register.

# STM32 Master and Arduino Slave communication

I programmed a simple application using a button to send a message via SPI from an STM32 F446RE master to an Arudino slave (transmission from master to slave only). Please note that you need a bidirectional logic level converter when interfacing between an STM32 and an Arduino, as their logic levels are incompatiable if you were to connect pins directly to each other.

[STM32 code](https://github.com/jczy3/STM32F446RE_Drivers/blob/main/sample_apps/SPI_txonly_arduino/SPI_txonly_arduino.c)

[Arduino sketch](https://github.com/niekiran/MasteringMCU/blob/master/Resources/Arduino/spi/001SPISlaveRxString/001SPISlaveRxString.ino)

Here is the logic / protocol analyzer decoding the length of the message and the message itself (before being sent to Arduino).

![](/assets/img/STM32Blog/221LA.PNG)
![](/assets/img/STM32Blog/msgLA.PNG)

Verification by the Arduino that the length of the message and the message itself is sent.

![](/assets/img/STM32Blog/arduinoproof.PNG)

As you can see, the custom message that I created was 221 bytes long. When changing the SPI settings in the Logic Analyzer software to ASCII, I am able to decode the message.

# Command handling with Arduino Slave

Here, I program an application interfacing an STM32 F446RE master and an Arudino slave, for the Arduino slave to handle commands sent by the STM32 master.

[STM32 code](https://github.com/jczy3/STM32F446RE_Drivers/blob/main/sample_apps/SPI_txonly_arduino/SPI_txonly_arduino.c)

[Arduino sketch](https://github.com/niekiran/MasteringMCU/blob/master/Resources/Arduino/spi/001SPISlaveRxString/001SPISlaveRxString.ino)

Lets analyze sending the LED control command (COMMAND_LED_CTRL).

![](/assets/img/STM32Blog/FD0.png)

![](/assets/img/STM32Blog/FD1.png)

In green, we can see that the command 0x50 was sent via MOSI, which corresponds to COMMAND_LED_CTRL. Note that the dummy read is also reflected in the trace (in red) via the MISO pin to clear garbage data in the Rx buffer sent by the Arduino, clearing the RXNE (because whenever you write a byte, you receive a byte).

![](/assets/img/STM32Blog/FD2.png)

In green, we can see that the ACK byte 0xF5 was received via MISO (acknowledging that data was received succcesfully). Note that the dummy write is also reflected in the trace (in red) via the MOSI pin to fetch the response from the slave (because whenever you read a byte, you write a byte).

![](/assets/img/STM32Blog/FD3.png)

Finally, we send the arugments to the arudino, setting PIN9 (0x09) to HIGH (0x01).
