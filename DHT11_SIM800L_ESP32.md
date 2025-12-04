

# ☁ DHT11 to ThingSpeak via SIM800L (No WiFi)

[![Arduino](https://img.shields.io/badge/Arduino-IDE-blue.svg)]()
[![SIM800L](https://img.shields.io/badge/SIM800L-GSM/GPRS-red.svg)]()
[![IoT](https://img.shields.io/badge/IoT-ThingSpeak-green.svg)]()
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)]()

Send **temperature and humidity** data from a **DHT11 sensor** to **ThingSpeak** using the **SIM800L GSM Module** — without WiFi.  
This project is ideal for **remote farms, rural IoT deployments, greenhouses, weather nodes, and offline environments**.

---

# 📁 Repository Structure

```

📦 dht11-sim800l-thingspeak
┣ 📂 /images
┃ ┣ wiring-diagram.png
┃ ┗ serial-log.png
┣ 📄 README.md
┗ 📄 dht11_sim800l_thingspeak.ino

````

---

# 🧩 Hardware Required

- ESP32 / Arduino UNO / Nano  
- SIM800L GSM Module  
- DHT11 sensor  
- 3.7–4.2V 2A power supply for SIM800L  
- Antenna  
- Jumper wires  
- 1000uF capacitor (for Stabilization)


---

# 🔌 Wiring Connections
<img width="1920" height="1080" alt="sim800l_project" src="https://github.com/user-attachments/assets/42fb7aaf-a661-49e2-b438-b07ff43516ad" />


## DHT11 → Arduino
| DHT11 | Arduino |
|-------|---------|
| VCC   | 5V      |
| GND   | GND     |
| DATA  | D2      |

## SIM800L → Arduino
| SIM800L | Arduino |
|---------|---------|
| TX      | D8      |
| RX      | D7      |
| GND     | GND     |
| VCC     | External 4V 2A |

⚠ **Do NOT power SIM800L from Arduino 5V — it will reboot constantly.**

---

# 🛠 Required Libraries

Install via Arduino Library Manager:

- **DHT sensor library** (Adafruit)
- **SoftwareSerial**

---

# 🎯 Configure ThingSpeak

1. Create or sign in to [ThingSpeak](https://thingspeak.com)  
2. Create a channel  
3. Enable:
   - Field 1 → Temperature  
   - Field 2 → Humidity  
4. Copy your **Write API Key**

---

# 🧪 FULL WORKING CODE (Arduino)

Save as: `dht11_sim800l_thingspeak.ino`

```cpp
#include <SoftwareSerial.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

// SIM800L pins
SoftwareSerial sim800l(7, 8); // RX, TX

String apn     = "your_apn_here";   // Example: "internet", "web.gprs.mtnnigeria.net"
String apiKey  = "YOUR_THINGSPEAK_API_KEY";

void sendCommand(String cmd, int delayMs) {
  sim800l.println(cmd);
  delay(delayMs);
  while (sim800l.available()) {
    Serial.write(sim800l.read());
  }
}

void setup() {
  Serial.begin(9600);
  sim800l.begin(9600);

  Serial.println("Initializing...");
  dht.begin();

  delay(3000);
}

void loop() {
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  if (isnan(h) || isnan(t)) {
    Serial.println("DHT11 read failed!");
    delay(2000);
    return;
  }

  Serial.print("Temp: "); Serial.print(t);
  Serial.print("  Humidity: "); Serial.println(h);

  // ----- SIM800L GPRS SETUP -----
  sendCommand("AT", 1000);
  sendCommand("AT+CSQ", 1000);
  sendCommand("AT+CREG?", 1000);

  sendCommand("AT+SAPBR=3,1,\"CONTYPE\",\"GPRS\"", 1000);
  sendCommand("AT+SAPBR=3,1,\"APN\",\"" + apn + "\"", 2000);

  sendCommand("AT+SAPBR=1,1", 3000);
  sendCommand("AT+SAPBR=2,1", 2000);

  // ----- HTTP REQUEST -----
  String url = "http://api.thingspeak.com/update?api_key=" + apiKey +
               "&field1=" + String(t) + "&field2=" + String(h);

  sendCommand("AT+HTTPTERM", 1000);  // reset previous session
  sendCommand("AT+HTTPINIT", 2000);
  sendCommand("AT+HTTPPARA=\"CID\",1", 1000);
  sendCommand("AT+HTTPPARA=\"URL\",\"" + url + "\"", 2000);

  sendCommand("AT+HTTPACTION=0", 6000); // 0 = GET
  sendCommand("AT+HTTPREAD", 2000);
  sendCommand("AT+HTTPTERM", 1000);

  Serial.println("Data sent to ThingSpeak!");

  delay(20000); // ThingSpeak accepts updates every 15+ seconds
}
````

---

# 📡 AT Command Flow Used by the Code

```
AT
AT+CSQ
AT+CREG?
AT+SAPBR=3,1,"CONTYPE","GPRS"
AT+SAPBR=3,1,"APN","your_apn"
AT+SAPBR=1,1
AT+HTTPINIT
AT+HTTPPARA="URL","http://api.thingspeak.com/update?api_key=XXXXX&field1=25&field2=80"
AT+HTTPACTION=0
AT+HTTPREAD
AT+HTTPTERM
```

---

# 🖼 Screenshots (Add in /images)

<img width="532" height="502" alt="Screenshot 2025-11-22 134253" src="https://github.com/user-attachments/assets/70efb103-4ca0-4b51-bf8a-25318b1b2b1a" />
```

```

---

# 🧯 Troubleshooting

| Problem                  | Cause            | Fix                            |
| ------------------------ | ---------------- | ------------------------------ |
| SIM800L keeps restarting | Not enough power | Use 4V 2A supply               |
| HTTP 603/601 error       | Wrong APN        | Use your country's correct APN |
| No network               | Weak signal      | Add external antenna           |
| ThingSpeak not updating  | Wrong API key    | Re-check the channel write key |
| `DHT read failed`        | Loose pin        | Check wiring                   |

---


# 📈 Result
![WhatsApp Image 2025-12-04 at 07 21 08_0e13b9ad](https://github.com/user-attachments/assets/6db35e0e-0609-4e9e-bb4d-60759f81b7c3)

✔ Temperature & humidity uploaded every 20 seconds
✔ No WiFi required — pure GSM GPRS
✔ Perfect for remote IoT nodes

---

# 📜 License

MIT License
You can use, modify, redistribute freely.

---

#  Authors

James Anaga & Elijah Ayara

```



