---
title: "Bike Rack Slot Availability System"
date: "2025-09-13"
tags: [Embedded Systems, Embedded C, ESP32, Python, Computer Vision, HTTP, Cloud Computing, Systems Integration]
thumbnail: "/assets/img/bikes.JPG"
---

# Project Description
---
A real-time Bike Rack Slot Availability System built using embedded hardware and cloud infrastructure to monitor and report open bike parking spaces.

This system can:
Capture live images using an ESP32-CAM
Process images to detect available bike rack slots
Transmit data via HTTP to cloud infrastructure
Store and manage images using Google Cloud
Provide real-time slot availability updates

The system uses an ESP32-CAM to periodically capture images of a bike rack. Due to limited onboard memory and computing power constraints of the microcontroller, it was not feasible to store or process images locally on the MCU. 

To address these hardware limitations, the system architecture was designed to offload image storage and processing to the cloud. Captured images are transmitted via HTTP to Google Cloud, where they are stored and processed using a Python-based image processing pipeline to determine occupancy.

This project focuses on embedded systems design and end-to-end system integration:
📷 ESP32-CAM image capture and device configuration  
🌐 HTTP-based data transmission  
☁️ Google Cloud storage integration  
🧠 Image processing pipeline for occupancy detection  
🔌 Embedded C firmware development  
🐍 Python backend processing  
🔁 Real-time data flow from edge device to cloud  
🛠 Version control and Agile development using Git  

# System Architecture
---
<img width="488" height="667" alt="image" src="https://github.com/user-attachments/assets/ac7b2ddf-ff3f-4cd3-a3a3-f85a4443e21e" />

# Bike Detection
---
<img width="513" height="382" alt="image" src="https://github.com/user-attachments/assets/2509434c-df48-4a77-ad2d-634803dda57a" />

