# 💬 SIM800L: Essential AT Commands Reference

The *SIM800L GSM/GPRS module* is controlled using *AT (Attention) commands* sent over the serial port. These commands are necessary for everything from checking network status to establishing a GPRS data connection.

## ⚙ Core Command Structure

* All commands must start with the prefix AT.
* Commands are terminated with a *Carriage Return* (\r or ASCII 13).
* The module responds with an acknowledgement, usually *OK*, or an error code.

---

## 📶 Network & Status Commands

These commands help you check the module's power, connectivity, and signal strength.

| Command | Description | Example Response | Notes |
| :--- | :--- | :--- | :--- |
| AT | Checks if the module is active and ready. | OK | Basic serial handshake. |
| ATE0 | Turns command echo *off*. | OK | *Recommended* for cleaner serial communication in code. |
| AT+CFUN=1 | Sets the module to *full functionality mode*. | OK | Ensures the radio is active and ready to register. |
| AT+CPIN? | Checks *SIM card status*. | +CPIN: READY | Must be READY before attempting network registration. |
| AT+CSQ | Checks *signal quality* (RSSI). | +CSQ: 18,0 | First number (0-31 is good, 99 is not detectable). |
| AT+CREG? | Checks *network registration status*. | +CREG: 0,1 | 0,1 means *registered* (home network). 0,2 means searching. |

[attachment_0](attachment)

---

## 🌐 GPRS and Internet Setup Commands

These commands are essential for enabling the GPRS (2G data) connection to log data to cloud services like ThingSpeak.

| Command | Description | Example Value | Notes |
| :--- | :--- | :--- | :--- |
| AT+CSTT | Sets the *Access Point Name (APN)*. | AT+CSTT="internet","user","pass" | Replace with your carrier's specific APN, username, and password. |
| AT+CIICR | *Brings up* the wireless connection. | AT+CIICR | This command activates the GPRS PDP Context. |
| AT+CIFSR | *Gets the local IP address* assigned by the network. | 10.x.x.x | Wait for a valid IP address to confirm successful connection. |
| AT+CIPSHUT | *Deactivates GPRS* and closes the connection. | SHUT OK | Used for safely resetting the data connection when finished. |

---
<img width="532" height="502" alt="Screenshot 2025-11-22 134253" src="https://github.com/user-attachments/assets/33df2c22-f467-4108-a4d6-0ab11c209c69" />


## ☁ HTTP/HTTPS Data Transfer Commands

The SIM800L has a built-in HTTP stack, simplifying data uploads for IoT projects. This sequence is commonly used for POSTing sensor data to services like ThingSpeak.

| Command | Description | HTTP Stack Commands | Notes |
| :--- | :--- | :--- | :--- |
| AT+HTTPINIT | *Initializes* the HTTP service. | 1. Initialize | Must be done once before any HTTP operations. |
| AT+HTTPPARA | Sets *HTTP parameters* (e.g., URL). | 2. Set Parameters | AT+HTTPPARA="URL","https://api.thingspeak.com/update?api_key=KEY..." |
| AT+HTTPACTION | *Executes* the HTTP method. | 3. Execute | AT+HTTPACTION=1 (0=GET, 1=POST). |
| AT+HTTPREAD | *Reads the server response*. | 4. Read Response | Use this to check the result code (e.g., 200 is success). |
| AT+HTTPTERM | *Terminates* the HTTP service. | 5. Terminate | Frees up resources after the data transfer is complete. |


