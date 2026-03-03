---
title: "IoT Home Security Camera System"
date: "2025-12-23"
tags: [Embedded Systems, ESP32, FreeRTOS, MQTT, Computer Vision, IoT, Networking]
thumbnail: "assets/img/securitycam.png"
---

# Project Description
---
A real-time IoT Home Security Camera System built using an ESP32-S3 and cloud-based messaging infrastructure to enable remote monitoring and motion-triggered alerts.

This system can:
Capture images using an ESP32-S3 camera module
Detect motion events in real time
Transmit image data securely over Wi-Fi
Publish alerts via MQTT messaging
Enable remote monitoring through a cloud-connected backend

The system uses an ESP32-S3 running FreeRTOS to manage concurrent tasks for image capture, motion detection, and network communication. Due to limited MCU memory and processing constraints, image handling and message routing were optimized to balance responsiveness and reliability.

When motion is detected, the device captures an image frame and transmits it via MQTT to a cloud broker, enabling remote clients to receive alerts and view captured data. The architecture separates edge processing from cloud messaging to maintain low latency while preserving system scalability.

This project emphasizes embedded systems design and real-time communication:
📷 ESP32-S3 camera integration and peripheral configuration  
🧵 FreeRTOS task scheduling and concurrency management  
🌐 Wi-Fi networking and MQTT-based message transport  
⚡ Real-time motion detection pipeline  
🧠 Memory-aware embedded firmware design  
🔁 Edge-to-cloud communication architecture  
🛠 Git-based version control and modular code organization  

# System Architecture
---
