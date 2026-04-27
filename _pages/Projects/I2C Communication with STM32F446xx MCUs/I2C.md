---
title: "I2C Communication with STM32F446xx MCUs"
date: "2026-04-27"
tags: [Embedded Systems, STM32, Communication Protocols, I2C, Driver Development]
thumbnail: "/assets/img/STM32Blog/projectstm32.jpg"
---

# Overview
---
Hello! Here, I will teach you how I2C communication works with STM32F446XX microcontrollers.

# What is I2C? How does it differ from SPI?
---
- It is a protocol for serial data communication betweens integrated circuits (ICs). The I2C protocol is much more complex than SPI (how data should be sent, received, how hand shaking occurs, error handling, etc.).
  
- I2C is based on dedicated specification. For STM32, the spec can be found [here.](https://www.nxp.com/docs/en/user-guide/UM10204.pdf). There is no global dedicated spec for I2C.
  
- I2C has multi-master capabilities. SPI has no guidelines for multi-master configuration and is dependent on the MCU designer. STM SPI peripherals can be used in multi master configurations, but abritration should be handles by software code. Arbitration in I2C is handled by the hardware automatically.
  
- I2C hardware also automatically ACKs every byte received, where SPI doesn't support automatic ACKing.

- I2C needs 2 pins, SPI requires 4.
 
- I2C master talks to slaves based on slave addresses. SPI uses a dedicated pin to select the slave.
 
- I2C is half duplex, and SPI is full duplex.
 
- I2C max speed is 4MHz in ultra speed plus. 
 
- I2C slaves can make the master wait by holding the clock down if it is busy (clock stretching). In SPI, slaves have no control over the clock.
 
- The data rate (# of bits transferred from sender to receiver in a second) of I2C is much less than SPI.

![](/assets/img/stm32_I2CBlog/i2c_definitions.PNG)

# I2C signals and modes
---
I2C uses 2 signals on the requried 2 lines: SDA and SCL.

- SDA and SCL are bidrectional lines, connected to a positive voltage supply via pull-up resistors. When the bus is free, both lines are held HIGH.
- THe output stages of devices connected to the bus must have an **open-drain or open-collector configuration.** Ensure that the pull up resistor value is calculated according to the I2C formula. You can use either internal or external pull up resistor. This applies to both SDA and SCL.
- The bus capacitance limits the number of interfaces that is connected to the bus.

Whenever you run into problems with I2C, probe the SDA and SCL pins after I2C initialization. These pins should be held at high.

- Standard mode communication data transfer rate can reach up to a max of 100 Kbps.
- Standard mode devices are not upward compatible. They cannot communicate with devices of fast mode or above.

- Fast mode communication data transfer rate can reach up to a max of 400 KBps
- Fast mode devices are downward compatiable.
- Standard mode devices should not be incorporated in a Fast-mode I2C bus system.
