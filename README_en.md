# GAS-DETECTOR

<p align="center">
  <img alt="Main language" src="https://img.shields.io/badge/C%2B%2B-ESP32-blue?style=for-the-badge&logo=cplusplus">
  <img alt="Arduino" src="https://img.shields.io/badge/Arduino-Compatible-00979D?style=for-the-badge&logo=arduino&logoColor=white">
  <img alt="Wokwi" src="https://img.shields.io/badge/Simulated%20on-Wokwi-purple?style=for-the-badge">
  <img alt="Status" src="https://img.shields.io/badge/status-in%20development-yellow?style=for-the-badge">
</p>

<p align="center">
  <a href="./README_pt.md">Portugues</a>
  &nbsp;|&nbsp;
  <strong>English</strong>
</p>

<p align="center">
  Cyber-physical system for flammable gas detection using an ESP32, gas sensor, status LEDs, buzzer and JSON serial logging.
</p>

<p align="center">
  <a href="https://wokwi.com/projects/462937229989048321">Open the Wokwi simulation</a>
</p>

---

## About the Project

**GAS-DETECTOR** is an embedded system prototype for monitoring flammable gas levels. The project was developed in Arduino-style C++ and simulated on Wokwi, using an ESP32 as the main control unit.

The application monitors the gas level, classifies the environment into operating states and triggers visual and audible signals according to the detected risk.

## Features

- Periodic gas level reading.
- State-based classification: `DESLIGADO`, `SEGURO`, `ALERTA`, `PERIGO`, `FALHA_SENSOR` and `FALHA_PINO`.
- Status LEDs for safe, warning and danger conditions.
- Buzzer for audible alerts in critical situations.
- Physical button to turn the system on, turn it off or silence the alarm.
- Moving average to smooth unstable readings.
- Hysteresis thresholds to avoid constant state switching.
- Event logging with a circular buffer.
- JSON serial output for integration with gateways, dashboards or Node-RED.
- Failure checks for timeout, invalid readings and output pin issues.

## Components

| Component | Purpose |
| --- | --- |
| ESP32 DevKit C V4 | Main microcontroller |
| Gas sensor | Analog input for gas level readings |
| Green LED | Safe environment indicator |
| Yellow LED | Intermediate warning level |
| Red LED | Dangerous gas level |
| Buzzer | Audible alarm |
| Button | Turns the system on/off and silences the alarm |
| Resistors | Current limiting and circuit support |

## Pinout

| ESP32 Pin | Component |
| --- | --- |
| GPIO 2 | Red LED |
| GPIO 4 | Yellow LED |
| GPIO 5 | Green LED |
| GPIO 25 | Buzzer |
| GPIO 27 | Button |
| GPIO 34 | Gas sensor analog output |
| 3V3 | Sensor power supply |
| GND | Common ground |

## State Machine

| State | Condition | Output |
| --- | --- | --- |
| `DESLIGADO` | System waiting to be enabled | LEDs and buzzer off |
| `SEGURO` | Average gas level below the warning threshold | Green LED on |
| `ALERTA` | Average gas level in the warning range | Yellow LED on |
| `PERIGO` | Average gas level above the critical threshold | Red LED and buzzer on |
| `FALHA_SENSOR` | Sensor did not respond within the expected time | Red/yellow LEDs alternate and buzzer sounds |
| `FALHA_PINO` | Repeated errors or hardware failure | All LEDs blink and buzzer sounds |

## Configured Thresholds

```cpp
#define SEGURO_SUBIDA   1500
#define SEGURO_DESCIDA  1350
#define PERIGO_SUBIDA   2800
#define PERIGO_DESCIDA  2600
```

These values use **hysteresis**, meaning the system uses different thresholds for entering and leaving each state. This prevents small sensor variations from rapidly switching the alarm between safe, warning and danger states.

## How It Works

1. The system starts turned off and waits for the button to be pressed.
2. When enabled, it enters the `SEGURO` state.
3. Every 2 seconds, a sensor reading is processed.
4. The average of the last 5 readings is used to reduce noise.
5. Based on the average value, the system switches between `SEGURO`, `ALERTA` and `PERIGO`.
6. In the danger state, the buzzer is activated.
7. When the system is turned off, the recent log history is printed to the Serial Monitor.

## Serial Output

The system publishes data in JSON format, making it easier to integrate with other tools:

```json
{
  "device_id": "gas_sensor_01",
  "ts": 12345,
  "estado": "ALERTA",
  "gas_raw": 1800,
  "gas_avg": 1620,
  "erros": 0
}
```

## How to Simulate

1. Open the project on Wokwi:

   [https://wokwi.com/projects/462937229989048321](https://wokwi.com/projects/462937229989048321)

2. Start the simulation.
3. Press the button to turn on the system.
4. Watch the LEDs, buzzer and Serial Monitor.

## Project Structure

```text
.
|-- sketch.ino
|-- diagram.json
|-- README.md
|-- README_pt.md
`-- README_en.md
```

| File | Description |
| --- | --- |
| `sketch.ino` | Main embedded system code |
| `diagram.json` | Circuit used in the Wokwi simulation |
| `README.md` | Main README with language links |
| `README_pt.md` | Portuguese documentation |
| `README_en.md` | English documentation |

## Note for Real Hardware Usage

In the simulation, the sensor reading is generated with random values:

```cpp
leituraAtual = random(0, 4096);
```

To build the system with physical hardware, replace that line with:

```cpp
leituraAtual = analogRead(SENSOR);
```

After that, calibrate the thresholds according to the sensor being used, the test environment and the reading range shown in the Serial Monitor.

## Possible Improvements

- Add an OLED or LCD display to show the state locally.
- Send alerts over Wi-Fi using MQTT, HTTP or Telegram.
- Create a dashboard for reading history.
- Store logs in a database.
- Implement automatic sensor calibration.
- Add documentation with photos or an electronic schematic of the physical circuit.

## Author

Developed by **Mati Limas** and **Arcanjowz** as a cyber-physical systems project.

---

<p align="center">
  Academic flammable gas detection project with ESP32, C++ and Wokwi.
</p>
