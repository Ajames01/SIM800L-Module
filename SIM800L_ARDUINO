# 🔌 SIM800L Connection Guide: Arduino Uno (5V Logic)

The Arduino Uno operates on *5V logic, while the SIM800L uses **2.8V logic. **You cannot connect the 5V TX pin of the Arduino directly to the SIM800L RX pin* without damaging the module. This guide outlines the proper, safe connections.

## ⚠ Power Requirement (CRITICAL)

The SIM800L needs a dedicated power source due to its high current demands (up to *2A* peaks during transmission). *Do NOT power the SIM800L directly from the Arduino's 5V pin.*

| Connection | Destination | Notes |
| :--- | :--- | :--- |
| *SIM800L VCC* | *4.0V DC Source (+)* | Use a LiPo battery or a *DC-DC Buck Converter* set to *4.0V* and rated for at least *2A*. |
| *SIM800L GND* | *4.0V DC Source (-)* | |
| *SIM800L GND* | *Arduino GND* | *Essential:* All grounds must be connected (Common Ground). |

---

## 💬 Serial Communication (SoftwareSerial)

Since the Arduino Uno's main serial port is used for programming and debugging, we use the SoftwareSerial library on digital pins.

| Arduino Pin | SIM800L Pin | Purpose |
| :--- | :--- | :--- |
| *D2 (SoftwareSerial RX)* $\leftarrow$ | *TXD* | Arduino *receives* data from the SIM800L. (No conversion needed here.) |
| *D3 (SoftwareSerial TX)* $\rightarrow$ | *RXD* | Arduino *sends* data to the SIM800L. *Requires Logic Level Conversion.* |

---

## ⚡ Logic Level Conversion (The MUST-DO Step)

To prevent damage, the *5V signal from Arduino D3 (TX)* must be stepped down to *~3.3V* before reaching the SIM800L's RXD pin.

### 1. Recommended Method: Bidirectional Logic Level Converter
* Connect the *5V Arduino TX (D3)* to the *HV (High Voltage)* side.
* Connect the *SIM800L RXD* to the *LV (Low Voltage)* side.

### 2. Simple Method: Resistor Voltage Divider
Use two resistors to divide the voltage on the TX line.

> 

| Resistor Placement | Value | Notes |
| :--- | :--- | :--- |
| *Arduino D3 (TX) to SIM800L RXD* | $R_1$ (e.g., *10kΩ*) | |
| *SIM800L RXD to GND* | $R_2$ (e.g., *20kΩ* or *15kΩ*) | This steps 5V down to a safe $\sim 3.3$V. |

***

## 💡 Note for Bulk Conversion

If you are using a *bulk converter* for logic leveling, ensure its High Voltage (HV) side is connected to the *5V* source/pin of the Arduino, and the Low Voltage (LV) side is connected to the *3.3V* reference/pin. This confirms the correct voltage translation for the SIM800L's RX pin.
