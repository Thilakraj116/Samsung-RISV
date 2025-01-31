# Automated Irrigation Setup

## Working
Soil Moisture Sensing:
The soil moisture sensor measures the moisture content in the soil.
If the soil is dry, the sensor sends a signal to the microcontroller.
Microcontroller Processing:

The microcontroller (VSD Squadron Mini (CH32V003 MCU)) receives data from the moisture sensor.
If the moisture level is low, it triggers the relay module to turn on the water pump.
Relay Activation and Pump Control:

The relay module acts as a switch. When activated by the microcontroller, it allows 230V AC power to flow to the water pump.
The pump turns ON and supplies water to the plant.
Bluetooth Control (HC-05 Module):

The HC-05 Bluetooth module enables wireless control.
A mobile app can manually turn ON/OFF the water pump.
Watering the Plant:

When the soil moisture sensor detects sufficient moisture, the microcontroller turns OFF the relay.
The pump stops, preventing overwatering.

## Connections

| **Component**               | **Pin**         | **Connection** |
|-----------------------------|----------------|---------------|
| **Soil Moisture Sensor**    | VCC            | Microcontroller 3.3V/5V |
|                             | GND            | Microcontroller GND |
|                             | Signal (A0/D0) | Analog/Digital Input Pin of Microcontroller |
| **HC-05 Bluetooth Module**  | VCC            | Microcontroller 3.3V/5V |
|                             | GND            | Microcontroller GND |
|                             | TX             | RX of Microcontroller |
|                             | RX             | TX of Microcontroller |
| **Relay Module**            | VCC            | Microcontroller 5V |
|                             | GND            | Microcontroller GND |
|                             | IN (Signal Pin)| Digital Output Pin of Microcontroller |
| **Relay Switch Terminals**  | COM (Common)   | One side of 230V power supply |
|                             | NO (Normally Open) | Connected to water pump |
|                             | NC (Normally Closed) | Not used in this setup |
| **Water Pump**              | Terminal 1     | Relay NO (Normally Open) |
|                             | Terminal 2     | Neutral of 230V power supply |
