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
It is a protocol for serial data communication betweens integrated circuits (ICs). The I2C protocol is much more complex than SPI (how data should be sent, received, how hand shaking occurs, error handling, etc.).

I2C is based on dedicated specification. For STM32, the spec can be found [here.](https://www.nxp.com/docs/en/user-guide/UM10204.pdf). There is no global dedicated spec for I2C.

I2C has multi-master capabilities. SPI has no guidelines for multi-master configuration and is dependent on the MCU designer. STM SPI peripherals can be used in multi master configurations, but abritration should be handles by software code. Arbitration in I2C is handled by the hardware automatically.

I2C hardware also automatically ACKs every byte received, where SPI doesn't support automatic ACKing.

I2C needs 2 pins, SPI requires 4.

I2C master talks to slaves based on slave addresses. SPI uses a dedicated pin to select the slave.

I2C is half duplex, and SPI is full duplex.

I2C max speed is 4MHz in ultra speed plus. 

I2C slaves can make the master wait by holding the clock down if it is busy (clock stretching). In SPI, slaves have no control over the clock.

The data rate (# of bits transferred from sender to receiver in a second) of I2C is much less than SPI.

![](/assets/img/stm32_I2CBlog/i2c_definitions.PNG)
