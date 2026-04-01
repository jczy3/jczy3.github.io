---
title: "SPI Communication with STM32F446xx MCUs"
date: "2026-04-01"
tags: [Embedded Systems, STM32, Communication Protocols, SPI]
thumbnail: "assets/img/stm32.jpg"
---

# Overview
---
Welcome to my first blog! Here, I will teach you how SPI communication works with STM32F446XX microcontrollers. By no means am I an expert, and I am a proponent in continuous improvement. If you see something here that is questionable or outright wrong, feel free to leave a comment for feedback!

Lets start with, what exactly is SPI? Why should we care?

SPI stands for Serial Peripheral Interface. This is a **serial communication protocol** (shared clock involved) used in embedded systems, whether thats a microcontroller communicating with external peripherals such as a sensor, SD card, a display, or even a microcontroller communicating with other microcontrollers. In a typical SPI setup, there is only one master and one or more slaves. SPI is also typically setup as to being full duplex (data being transmitted and received at the same time).

SPI uses 4 GPIO pins: SCLK, MOSI, MISO, and SS.

SCLK is the Serial Clock pin, which determines when the master is to receive/transmit data. This is an output pin for the master and input pin/s for the slave/s, as the SCLK is produced by the master.

MOSI is the data line in which the master transmit data and the slave receives data.

MISO is the data line in which the master receives data and the slave transmits data.

SS is the slave select pin, which allows the master to select an individual slave for communication.

# SPI vs. other communication protocols
---
SPI in comparison to most other protcols, is designed to operate within a short distance (around 10 ft) with a high throughput (more data can be transferred in less time). Take I2C for example, it too is considered a "short distance" protocol (18 ft) and operates at around 3.4 - 5 Mbps in high speed mode, while SPI typically operates around 10 - 50 Mbps. For applications requiring longer distances, opt for protocols that suit this requirement (CAN, ethernet, etc.). Communication protocols are application dependent and are not "one size fits all."

Just a reminder, frequency / bandwith measures the capacity (pipe size). Bits per second measures the throughput (flow rate).

# 
