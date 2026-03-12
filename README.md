# Quadcopter Web GCS (Ground Control Station)

[![Tech Stack](https://img.shields.io/badge/Stack-HTML5%20%7C%20TailwindCSS%20%7C%20Vanilla%20JS-38B2AC?style=flat)](#) [![Status](https://img.shields.io/badge/Status-Base_Code%20%2F%20Active-brightgreen?style=flat)](#)

<img width="1899" height="904" alt="GSC App for Drone" src="https://github.com/user-attachments/assets/ee3e295f-599b-4830-b3e8-e38602e730b2" />


## What is this?

You dont have RC Transmitter for drone? No problem! This app has inbuilt controller for sending the control commands.
This is a lightweight single file, browser-based Ground Control Station (GCS) designed for custom quadcopter survillance missions, specifically search or remote scouting. No Need for external radio transmitters.

Moreover heavy desktop apps like Mission Planner are great, but sometimes you need a fast, custom dashboard that anyone can open on a field laptop without installing software. And to build complex app on basic codebase This mini app is great for getting started. It serves as the frontend UI and the manual control logic layer. Right now, the telemetry and connection are mocked, but the architecture is ready to get live IP camera feeds and WebSocket telemetry from an onboard companion computer (like an ESP32 or Raspberry Pi).

## ⚙️ Core Features

* **IP Camera Integration:** Built specifically to handle direct MJPEG streams (like from an ESP32-CAM). Just punch the IP address into the top bar, and get feed updates.
* **Offline Fallbacks:** Disaster zones usually don't have internet. If the GCS loses connection, the live map automatically falls back to a pre-loaded local map image, and the video feed has a local replay fallback for UI testing.
* **Telemetry Data Info:** The UI is structured to display Altitude, Ground/Vertical Speed, Battery, and GPS. It's currently populated with dummy data, waiting to be linked to live JSON/WebSocket packets.
* **Mission Console:** A live-updating terminal in the browser that logs connections, errors, and outgoing flight commands.

## 🕹️ Custom Keyboard Flight Logic
The most complex part of this base code is the custom manual flight controller. I wrote this so the drone can be flown directly from a laptop keyboard without an external RC transmitter.

* **Kinematic Mapping:** * `W / S`: Pitch Forward / Backward
  * `A / D`: Yaw Left / Right
  * `↑ / ↓`: Throttle Up / Down
  * `← / →`: Roll Left / Right
* **Acceleration Math:** The system doesn't just send hard limits. It implements an acceleration/deceleration interval. Holding the up arrow ramps the base throttle (starting at 1000) up to a max of 2000. Releasing the key triggers a deceleration curve to smooth out the flight.
* **Command Packaging:** It calculates multi-axis inputs (e.g., Pitch Forward + Roll Right) and packages them into a clean comma-separated string `(e.g., "Pitch Forward, Roll Right, 1500, 1200")` to send over WebSockets.

## What you have to do Next..
This repo contains the frontend and control logic but to deploy this on real hardware, the following needs to be done:
1. Replace the `MockSocket` Javascript class with a real WebSocket pointing to the drone's IP.
2. Write a lightweight server script on the drone (Python/C++) to catch the WebSocket commands and translate them into MAVLink `RC_CHANNELS_OVERRIDE` messages or if youre working on ROS 2 write a ROS 2 node for handling commands But this app is not designed for ROS2, I have separate repo in which I developed ROS2 compatible app. Visit that.
3. Stream the drone's onboard sensors (Pixhawk/GPS) back through the WebSocket to populate the Telemetry and Map windows.
