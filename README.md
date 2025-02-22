# ESP32-Temperature-Sensor
This project involves connecting an ESP32 microcontroller with a DHT11 temperature and humidity sensor to multiple edge nodes using MQTT.

## Setup

1) Install Mosquitto on the edge nodes:
```bash
sudo apt update

sudo apt install mosquitto mosquitto-clients -y

sudo systemctl enable mosquitto

2) Upload the ESP32 code to your device
