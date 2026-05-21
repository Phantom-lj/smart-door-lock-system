# 🔐 Smart Password-Based Door Lock System

![Arduino](https://img.shields.io/badge/Arduino-Uno-00979D?style=flat&logo=arduino&logoColor=white)
![ESP8266](https://img.shields.io/badge/ESP8266-WiFi-E7352C?style=flat&logo=espressif&logoColor=white)
![C++](https://img.shields.io/badge/Language-C%2FC%2B%2B-00599C?style=flat&logo=c%2B%2B&logoColor=white)
![Wokwi](https://img.shields.io/badge/Simulated%20on-Wokwi-7B2FBE?style=flat)
![Status](https://img.shields.io/badge/Status-Simulation%20Verified-22c55e?style=flat)
![Tests](https://img.shields.io/badge/Tests-14%2F14%20Passed-22c55e?style=flat)

An advanced IoT-enabled door lock system built on **Arduino Uno + ESP8266**, featuring keypad authentication, servo motor locking, RTC-timestamped access logging, intruder detection, and a WiFi web dashboard for remote control — fully simulated and validated on **Wokwi**.

---

## 📽️ Demo

> *(Insert your Wokwi screen recording GIF or video here)*
> 
> You can convert your screen recording to GIF using [ezgif.com](https://ezgif.com/video-to-gif) and drag-drop it into this README on GitHub.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔑 **Password Authentication** | 4-digit PIN via 4×4 matrix keypad, echoed as `****` on LCD |
| ⚙️ **Servo Motor Lock** | SG90 servo rotates 0°→90° on unlock, auto-relocks after 3 seconds |
| 📟 **LCD Feedback** | Real-time status: Enter Password / Access Granted / Denied / Intruder |
| 🚨 **Intruder Detection** | Red LED strobe + buzzer alarm after 3 consecutive wrong attempts |
| 🕐 **RTC Timestamps** | DS3231 timestamps every access event with battery-backed accuracy |
| 💾 **EEPROM Logging** | Circular buffer stores last 20 access events across power cycles |
| 🔒 **Password Change** | User-initiated 2-step password update without reprogramming |
| 🌐 **WiFi Web Dashboard** | ESP8266 hosts HTTP server — unlock remotely from any browser |
| 📋 **Access Log Viewer** | Web page showing full timestamped event history |
| 🚪 **Reed Switch** | Detects if door is physically open/closed independent of servo |

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         INPUT LAYER                             │
│  [4×4 Keypad D2-D9]   [DS3231 RTC A4/A5]   [Reed Switch D10]  │
└────────────────────────────┬────────────────────────────────────┘
                             │
┌────────────────────────────▼────────────────────────────────────┐
│                      Arduino Uno (Brain)                        │
│   Password auth · Servo control · EEPROM logging · Intruder    │
│   LCD management · Buzzer · LED · Serial comms to ESP           │
└───────────┬─────────────────────────────────────┬───────────────┘
            │                                     │
   [Serial TX/RX]                    ┌────────────▼────────────┐
            │                        │     OUTPUT LAYER        │
            │                        │  Servo D11 · LCD A4/A5  │
            │                        │  Buzzer D12 · LEDs D13/A0│
            │                        └─────────────────────────┘
            │
┌───────────▼─────────────────────────────────────────────────────┐
│                    ESP8266 (WiFi Layer)                         │
│   Connects to WiFi · Hosts HTTP server on port 80               │
│   Remote unlock · Access log viewer · JSON status API           │
│                                                                 │
│   Browser → http://<ESP-IP>/          → Dashboard              │
│   Browser → http://<ESP-IP>/unlock    → Remote unlock          │
│   Browser → http://<ESP-IP>/log       → Access log             │
│   Browser → http://<ESP-IP>/status    → JSON status            │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔌 Pin Mapping

### Arduino Uno

| Component | Pin | Notes |
|---|---|---|
| 4×4 Keypad (Rows) | D2, D3, D4, D5 | Row scan |
| 4×4 Keypad (Cols) | D6, D7, D8, D9 | Column scan |
| Servo Motor | D11 | PWM — 0° locked / 90° unlocked |
| Buzzer | D12 | + 100Ω resistor |
| Green LED | D13 | + 220Ω resistor |
| Red LED | A0 | + 220Ω resistor |
| Reed Switch | D10 | INPUT_PULLUP |
| LCD 16×2 I2C (SDA) | A4 | Shared I2C bus |
| LCD 16×2 I2C (SCL) | A5 | Shared I2C bus |
| DS3231 RTC (SDA) | A4 | Shared I2C bus |
| DS3231 RTC (SCL) | A5 | Shared I2C bus |
| ESP8266 TX→Arduino | D1 (SoftSerial) | 9600 baud |
| ESP8266 RX←Arduino | D0 (SoftSerial) | 9600 baud |

### ESP8266 / ESP32

| Signal | Pin | Notes |
|---|---|---|
| Arduino RX | GPIO16 (Serial2) | Receives from Arduino |
| Arduino TX | GPIO17 (Serial2) | Sends to Arduino |
| Status LED | GPIO2 | Blinks when WiFi connected |
| Power | 3.3V only | NOT 5V tolerant |

---

## 📦 Hardware Components

| Component | Quantity |
|---|---|
| Arduino Uno (ATmega328P) | 1 |
| ESP8266 NodeMCU / ESP32 DevKit | 1 |
| 4×4 Matrix Keypad | 1 |
| 16×2 LCD with I2C adapter | 1 |
| DS3231 RTC Module + CR2032 | 1 |
| SG90 Servo Motor | 1 |
| Passive Buzzer | 1 |
| Green LED 5mm | 1 |
| Red LED 5mm | 1 |
| Magnetic Reed Switch | 1 |
| Resistor 220Ω | 2 |
| Resistor 100Ω | 1 |
| Breadboard + Jumper Wires | — |

---

## 🗂️ Repository Structure

```
smart-door-lock/
│
├── arduino/
│   └── arduino_door_lock_wokwi.ino   # Main Arduino sketch (Wokwi-ready)
│
├── esp8266/
│   └── esp8266_server.ino            # ESP8266/ESP32 WiFi server sketch
│
├── wokwi/
│   ├── arduino_diagram.json          # Wokwi circuit diagram — Arduino Uno
│   └── esp32_diagram.json            # Wokwi circuit diagram — ESP32
│
├── docs/
│   └── Smart_Door_Lock_Report.docx   # Full project report
│
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites

Install these libraries via Arduino IDE → Tools → Manage Libraries:

```
Keypad             by Mark Stanley
LiquidCrystal_I2C  by Frank de Brabander
RTClib             by Adafruit
```

The following are built-in (no install needed):
```
Servo · EEPROM · SoftwareSerial · WiFi · WebServer
```

### Arduino Upload

1. Open `arduino/arduino_door_lock_wokwi.ino` in Arduino IDE
2. **Disconnect ESP8266 TX/RX wires** before uploading
3. Select board: `Arduino Uno` and correct COM port
4. Upload
5. Open Serial Monitor at **9600 baud**
6. Uncomment `rtc.adjust(...)` once to set the clock, re-upload, then comment it out again

### ESP8266/ESP32 Upload

1. Open `esp8266/esp8266_server.ino`
2. Replace WiFi credentials:
```cpp
const char* ssid     = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";
```
3. Select board: `NodeMCU 1.0` or `ESP32 Dev Module`
4. Upload at 115200 baud
5. Open Serial Monitor — note the IP address printed on boot
6. Open that IP in your phone or PC browser

### Wokwi Simulation

**Arduino project:**
1. Go to [wokwi.com/projects/new/arduino-uno](https://wokwi.com/projects/new/arduino-uno)
2. Paste `wokwi/arduino_diagram.json` into the `diagram.json` tab
3. Paste the Arduino sketch into the code editor
4. Hit ▶ Play

**ESP32 project:**
1. Go to [wokwi.com/projects/new/esp32](https://wokwi.com/projects/new/esp32)
2. Paste `wokwi/esp32_diagram.json` into the `diagram.json` tab
3. Paste the ESP sketch (WiFi SSID = `Wokwi-GUEST`, password = `""`)
4. Hit ▶ Play — IP address appears in Serial Monitor

---

## 🎮 Usage

### Keypad Controls

| Key | Action |
|---|---|
| `0–9` | Enter password digits (shown as `****`) |
| `#` | Submit password |
| `*` | Clear input / cancel |
| `A` | Start password change *(when unlocked)* |
| `D` | Manually lock door *(when unlocked)* |

### Default Password
```
1234
```

### Serial Monitor Commands *(Wokwi / debug)*

| Type | Action |
|---|---|
| `L` | Print full access log with timestamps |
| `U` | Simulate remote unlock from ESP |

### Web Dashboard Routes

| URL | Description |
|---|---|
| `http://<IP>/` | Main dashboard — status + controls |
| `http://<IP>/unlock` | Remote unlock |
| `http://<IP>/log` | View access log |
| `http://<IP>/status` | JSON status API |

---

## 💾 EEPROM Layout

```
Address  Size     Content
───────  ───────  ─────────────────────────────────────────
0–3      4 bytes  Password (ASCII, e.g. '1','2','3','4')
4        1 byte   Sentinel (0xAA = EEPROM initialised)
5        1 byte   Log write pointer (0–19, circular)
6–205    200 B    20 log entries × 10 bytes each:
                    [0] Year (YY)
                    [1] Month
                    [2] Day
                    [3] Hour
                    [4] Minute
                    [5] Second
                    [6] Status (0=GRANTED 1=DENIED 2=INTRUDER 3=REMOTE)
                    [7] Attempt count
                    [8–9] Reserved (0xFF)
```

---

## ✅ Test Results

All 14 test cases passed on Wokwi simulation:

| # | Test Case | Result |
|---|---|---|
| 1 | Correct password (1234) | ✅ PASS |
| 2 | Wrong password — 1 attempt | ✅ PASS |
| 3 | Wrong password — 3 attempts → intruder mode | ✅ PASS |
| 4 | Clear intruder alarm with `*` | ✅ PASS |
| 5 | Clear input mid-entry with `*` | ✅ PASS |
| 6 | Auto-relock after 3 seconds | ✅ PASS |
| 7 | Manual lock with `D` key | ✅ PASS |
| 8 | Password change flow (A key) | ✅ PASS |
| 9 | Mismatched password confirmation rejected | ✅ PASS |
| 10 | EEPROM log printed via Serial Monitor | ✅ PASS |
| 11 | Remote unlock via Serial Monitor (`U`) | ✅ PASS |
| 12 | ESP32 connects to Wokwi-GUEST WiFi | ✅ PASS |
| 13 | Web dashboard renders with door status | ✅ PASS |
| 14 | Remote unlock via web dashboard | ✅ PASS |

---

## 🔮 Future Scope

- [ ] Fingerprint sensor (R307) for biometric 2FA
- [ ] RFID/NFC card reader (RC522) for multi-user access
- [ ] Cloud MQTT integration (AWS IoT / HiveMQ)
- [ ] ESP32-CAM to capture photos on each access attempt
- [ ] AES-128 encryption of EEPROM password storage
- [ ] HTTPS on web dashboard
- [ ] Mobile app (Flutter) with push notifications

---

## 🛠️ Built With

- **Arduino Uno** — ATmega328P microcontroller
- **ESP8266 / ESP32** — WiFi + web server
- **C / C++** — Firmware for both controllers
- **Wokwi** — Online embedded systems simulator
- **Arduino IDE** — Development environment

---

## 👤 Author

**Lakshya**  
B.Tech Electronics & Communication Engineering  
Manipal University Jaipur  

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/YOUR_PROFILE)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat&logo=github)](https://github.com/YOUR_USERNAME)

---

## 📄 License

This project is open source under the [MIT License](LICENSE).

---

> ⭐ If you found this useful, give it a star — it helps other students find it!
