# 🔌 SIM800L Connection Guide: ESP32 (3.3V Logic)

The ESP32 operates on *3.3V logic, which is very close to the SIM800L's **2.8V logic. This significantly simplifies the serial connection, and **logic level converters are often unnecessary* but still recommended for strict compliance.

## ⚠ Power Requirement (CRITICAL)

The SIM800L needs a dedicated power source due to its high current demands (up to *2A* peaks during transmission). *Do NOT power the SIM800L directly from the ESP32's 3.3V output.*

| Connection | Destination | Notes |
| :--- | :--- | :--- |
| *SIM800L VCC* | *4.0V DC Source (+)* | Use a LiPo battery or a *DC-DC Buck Converter* set to *4.0V* and rated for at least *2A*. |
| *SIM800L GND* | *4.0V DC Source (-)* | |
| *SIM800L GND* | *ESP32 GND* | *Essential:* All grounds must be connected (Common Ground). |

<img width="733" height="557" alt="f22f1c5dfcf54e41180a94fb7811cd953ebdd4f9" src="https://github.com/user-attachments/assets/18c06b88-6010-400f-b1ea-6ec9a8f7e81a" />

---

## 💬 Serial Communication (Hardware UART)

The ESP32 has multiple hardware serial ports (UARTs), which are more reliable than software serial. We will use UART2 (Serial2).

| ESP32 Pin | SIM800L Pin | Purpose | Logic Level |
| :--- | :--- | :--- | :--- |
| *GPIO 16 (RX2)* $\leftarrow$ | *TXD* | ESP32 *receives* data from the SIM800L. | 2.8V $\rightarrow$ 3.3V Input (Safe) |
| *GPIO 17 (TX2)* $\rightarrow$ | *RXD* | ESP32 *sends* data to the SIM800L. | 3.3V $\rightarrow$ 2.8V Input (Generally Safe) |

---

## ⚡ Logic Level Consideration (Direct Connection)

Because the ESP32's 3.3V output is only slightly higher than the SIM800L's 2.8V input maximum, direct connection is widely practiced and generally safe, but should be tested carefully.

* *SIM800L TX $\rightarrow$ ESP32 RX:* The 2.8V signal is easily read by the 3.3V ESP32 input.
* *ESP32 TX $\rightarrow$ SIM800L RX:* The 3.3V signal is close enough to the 2.8V required by the SIM800L to work without a level converter in most cases.

## 💡 Note for Bulk Conversion

If you opt to use a *bulk converter* for added safety, ensure its Low Voltage (LV) side is connected to the *3.3V* reference/pin of the ESP32. This guarantees the 3.3V signals are properly managed before they hit the SIM800L.
