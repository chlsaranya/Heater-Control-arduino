# Heater Control System using Arduino Uno


---

## Introduction

This project involves the design and simulation of a basic heater control system using the Arduino Uno platform. The system uses a DHT22 temperature sensor to monitor the ambient temperature and simulates a heater using an LED. A buzzer is also included to provide alerts in case of an overheat condition. The heater is turned ON or OFF automatically based on predefined temperature thresholds, and the system continuously logs the temperature and operating state to the serial monitor. The implementation was carried out in the Wokwi simulator using the Arduino programming language (C/C++). This project demonstrates fundamental concepts of embedded systems, including sensor interfacing, actuator control, state management, and real-time monitoring.

---

## Part 1: System Design

### Components Used

| Component     | Description                                |
|--------------|--------------------------------------------|
| Arduino Uno   | Microcontroller board                      |
| DHT22 Sensor  | Measures ambient temperature and humidity  |
| LED           | Simulates the heater (ON/OFF indicator)    |
| Buzzer        | Provides alert on overheat condition       |
| Serial Monitor| For logging temperature and system state   |

---

### Communication Protocol

**Serial Communication (UART)** is used for:
- Real-time temperature and state logging
- Debugging the system through the Arduino IDE Serial Monitor

This protocol is simple and requires no additional hardware, making it ideal for basic embedded applications.

---

### Block Diagram

       +----------------+
       | DHT22 Sensor   |
       +-------+--------+
               |
               v
      +--------+---------+
      |   Arduino Uno    |
      +--------+---------+
               |
     +---------+----------+
     |                    |
   [LED]              [Buzzer]
(Heater ON/OFF)     (Overheat Warning)
               |
               v
     Serial Monitor (Logs state)

---

### Future Roadmap

- Implement **overheat protection**: shutdown system and alert user
- Add **multiple heating profiles** (Low/Medium/High modes)
- Integrate **Bluetooth/BLE** for mobile connectivity
- Real heater control using **relay module**
- Use **FreeRTOS** for real-time task management

---

## Part 2: Embedded Implementation

### State Definitions

| State           | Description                                       |
|----------------|---------------------------------------------------|
| Idle            | System waiting to begin or low temperature        |
| Heating         | Temperature below target, heater (LED) is ON      |
| Stabilizing     | Temperature approaching target                    |
| Target Reached  | Target temperature reached, heater is OFF         |
| Overheat        | Temperature above safe threshold, buzzer active   |


### Temperature Reading

Using `DHT.h` library:

```cpp
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
}
#define LED_PIN 3
#define BUZZER_PIN 4

void loop() {
  float temp = dht.readTemperature();
  String state = "";

  if (isnan(temp)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  if (temp < 30) {
    digitalWrite(LED_PIN, HIGH);      // Heater ON
    digitalWrite(BUZZER_PIN, LOW);
    state = "Heating";
  } else if (temp < 35) {
    digitalWrite(LED_PIN, LOW);       // Target reached
    digitalWrite(BUZZER_PIN, LOW);
    state = "Target Reached";
  } else {
    digitalWrite(LED_PIN, LOW);       // Overheat
    digitalWrite(BUZZER_PIN, HIGH);
    state = "Overheat";
  }

  Serial.print("Temp: ");
  Serial.print(temp);
  Serial.print(" °C | State: ");
  Serial.println(state);

  delay(1000);
}

Temp: 28.5 °C | State: Heating  
Temp: 30.1 °C | State: Target Reached  
Temp: 36.7 °C | State: Overheat  

