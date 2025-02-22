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
```

### 2. Upload code to ESP32
- Open the your_file_name.ino file in the Arduino IDE 
- Update the Wi-Fi credentials (ssid and password) in the code.
```cpp
const char* ssid = "your_wifi_ssid";       // Change this
const char* password = "your_wifi_password"; // Change this
```
- Update the MQTT broker IP addresses in the mqtt_servers array.
```cpp
const char* mqtt_servers[] = { "x.x.x.x", "x.x.x.x", "x.x.x.x" };  // Change these IPs
```
- Upload the code to your ESP32.

### 3. Subscribe to MQTT Topic 
To receive sensor data on an edge node, run the following command:
```bash
mosquitto_sub -h <ip_of_edge_node> -t "sensor/data" -v
```
### 4. Store Data to a Text File
To store the sensor data in a text file, run:
```bash
mosquitto_sub -h <ip_of_edge_node> -t "sensor/data" -v >> sensor_data.txt
```
### 5. Code 
The main code for the ESP32 is provided below. Make sure to update the Wi-Fi credentials and MQTT broker IP addresses as described in the Setup section.
```cpp
#include <WiFi.h>
#include <PubSubClient.h>
#include "DHT.h"

#define DPIN 15
#define DTYPE DHT11

const char* ssid = "your_wifi_ssid";       // Change this
const char* password = "your_wifi_password"; // Change this

const char* mqtt_servers[] = { "x.x.x.x", "x.x.x.x", "x.x.x.x" };  // Multiple EdgeNodes IP addresses
const int num_servers = 3;

WiFiClient espClients[num_servers];
PubSubClient clients[num_servers] = {
  PubSubClient(espClients[0]),
  PubSubClient(espClients[1]),
  PubSubClient(espClients[2]),

};

DHT dht(DPIN, DTYPE);

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi!");

  for (int i = 0; i < num_servers; i++) {
    clients[i].setServer(mqtt_servers[i], 1883);
  }

  dht.begin();
}

void loop() {
  delay(2000);

  float tempC = dht.readTemperature(false);
  float humidity = dht.readHumidity();
  
  String payload = "{ \"temperature\": " + String(tempC) + ", \"humidity\": " + String(humidity) + " }";

  for (int i = 0; i < num_servers; i++) {
    if (!clients[i].connected()) {
      Serial.print("Connecting to MQTT broker: ");
      Serial.println(mqtt_servers[i]);

      String clientID = "ESP32Client" + String(i);  // Convert to String
      if (clients[i].connect(clientID.c_str())) {  // Convert to const char*
        Serial.println("Connected to MQTT!");
      } else {
        Serial.println("MQTT connection failed!");
        continue;
      }
    }

    clients[i].publish("sensor/data", payload.c_str());
    Serial.println("Data Sent to " + String(mqtt_servers[i]) + ": " + payload);
  }
}
```
