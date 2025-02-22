# ESP32 Temperature Sensor with MQTT

This project demonstrates how to connect an ESP32 microcontroller with a DHT11 temperature and humidity sensor to multiple edge nodes using MQTT. The sensor data is published to an MQTT broker running on edge nodes, and the data can be subscribed to and stored for further analysis.


## Features
- Reads temperature and humidity data from a DHT11 sensor.
- Publishes sensor data to multiple MQTT brokers (edge nodes).
- Stores sensor data in a text file for further analysis.


## Hardware Requirements
- ESP32 microcontroller
- DHT11 temperature and humidity sensor
- Breadboard and jumper wires
- Micro-USB cable for power and programming

## Software Requirements
- Arduino IDE 
- Mosquitto MQTT broker (installed on edge nodes)
- ESP32 board support package (installed in Arduino IDE)
- PubSubClient library (for MQTT communication)
- DHT library (for reading sensor data)

## Setup

### 1. Install Mosquitto on Edge Nodes
Run the following commands on each edge node to install and start the Mosquitto MQTT broker:
```bash
sudo apt update
sudo apt install mosquitto mosquitto-clients -y
sudo systemctl enable mosquitto

### 2. Upload code to ESP32
Open the esp32_temp_sensor.ino file in the Arduino IDE or PlatformIO.

Update the Wi-Fi credentials (ssid and password) in the code.

Update the MQTT broker IP addresses in the mqtt_servers array.

Upload the code to your ESP32.
