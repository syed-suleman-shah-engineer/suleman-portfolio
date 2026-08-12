---
title: "ESP-01S Relay Flashing Guide: The One Trick That Actually Works"
description: "A complete step-by-step guide to flashing custom firmware onto ESP-01S relay modules using FTDI and PlatformIO. After hours of frustration, here's the timing trick that finally worked."
tags: [ESP8266, ESP-01S, Relay Control, PlatformIO, FTDI, IoT, WiFi, Web Server, Arduino]
date: 2026-08-12
---

Today I finally figured out how to reliably flash custom firmware onto ESP-01S relay modules. After hours of frustration with "Failed to connect" errors, the solution was surprisingly simple—it's all about timing.

This guide documents everything I learned so I (and you) never have to struggle with this again.

## What You'll Need

- ESP-01S relay module (ESP8266-based, 1MB flash)
- FTDI USB-to-serial adapter (3.3V mode)
- Jumper wires (female-to-female)
- VS Code with PlatformIO extension
- Basic understanding of Arduino/ESP8266 programming

## The Problem: Why Flashing Fails

The ESP-01S checks the GPIO0 pin only at the exact moment it powers up. Here's what happens:

GPIO0 State at Boot	Result
LOW (connected to GND)	Enter flash mode → Ready to upload
HIGH or floating	Boot normal mode → Runs existing firmware
Most tutorials tell you to "connect GPIO0 to GND" but don't emphasize the critical timing. You must power the ESP after GPIO0 is low—the bootloader checks this pin only once, during the initial power-on sequence.

## Wiring Guide

### FTDI to ESP-01S Connections
FTDI Pin	ESP-01S Pin	Note
VCC (3.3V)	VCC	Power
VCC (3.3V)	EN	Chip enable (mandatory!)
GND	GND	Ground
TX	RX	Cross connection
RX	TX	Cross connection
GND	GPIO0	Only during flashing
> ⚠️ **Important:** The FTDI must be set to 3.3V mode. Using 5V can damage your ESP module. If your adapter has a voltage switch, make sure it's on 3.3V.

### Visual Wiring Reference
text
FTDI Adapter          ESP-01S Relay
─────────────         ─────────────
   VCC (3.3V) ──────── VCC
   VCC (3.3V) ──────── EN
   GND        ──────── GND
   TX         ──────── RX
   RX         ──────── TX
   GND        ──────── GPIO0 (Jumper - ONLY during flash!)
> **Note:** GPIO0 is connected to GND with a jumper wire during flashing. Remove this wire after successful upload.

## PlatformIO Project Setup

### 1. Create New Project

In VS Code with PlatformIO:

1. Click "New Project"
2. Name: `ESP-Relay-Control`
3. Board: `Espressif ESP8266 ESP12e`
4. Framework: `Arduino`

### 2. platformio.ini

Replace the contents with:

ini
[platformio]
default_envs = generic_esp8266

[env:generic_esp8266]
platform = espressif8266
board = esp12e
framework = arduino
upload_port = COM11          # Change to your COM port
monitor_port = COM11
upload_speed = 115200        # Can lower to 9600 for stability
monitor_speed = 115200

lib_deps = 
    bblanchon/ArduinoJson@^6.21.3
> **Note:** `board = esp12e` works perfectly for ESP-01S modules as they share the same ESP8266 core.

## The Complete Firmware Code

Create `src/main.cpp` with the following code:

cpp
#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>
#include <WiFiUdp.h>
#include <ArduinoJson.h>
#include <DNSServer.h>
#include <ESP8266mDNS.h>
#include <EEPROM.h>

// ========================================
// EEPROM Configuration (for saving WiFi credentials)
// ========================================
#define EEPROM_SIZE 150
#define SSID_ADDR 0
#define PASS_ADDR 50
#define VALID_FLAG_ADDR 149

// ========================================
// Default WiFi credentials (fallback if no saved credentials)
// ========================================
const char *defaultSSID = "Your-Home-WiFi";        // Change to your home WiFi
const char *defaultPassword = "Your-WiFi-Password"; // Change to your WiFi password

// ========================================
// Access Point settings (always active)
// ========================================
const char *apSSID = "witshells-relay";
const char *apPassword = "12ta442ta1";

// ========================================
// Relay Pin - USE GPIO2 (NOT GPIO0!)
// GPIO0 is the boot-mode pin - using it can cause boot failures
// ========================================
const int relayPin = 2;   // ✅ Safe choice for ESP-01S relay boards

// ========================================
// Web Server & UDP
// ========================================
ESP8266WebServer server(80);
DNSServer dnsServer;
WiFiUDP udp;
const int udpPort = 5555;
char packetBuffer[255];

String savedSSID = "";
String savedPassword = "";

// ========================================
// EEPROM Functions
// ========================================
void saveWiFiCredentials(String ssid, String password)
{
  EEPROM.begin(EEPROM_SIZE);
  
  // Clear existing data
  for (int i = 0; i < 50; i++)
    EEPROM.write(SSID_ADDR + i, 0);
  for (int i = 0; i < 64; i++)
    EEPROM.write(PASS_ADDR + i, 0);
  
  // Write new credentials
  for (int i = 0; i < ssid.length(); i++)
    EEPROM.write(SSID_ADDR + i, ssid[i]);
  for (int i = 0; i < password.length(); i++)
    EEPROM.write(PASS_ADDR + i, password[i]);
  
  EEPROM.write(VALID_FLAG_ADDR, 1); // Mark as valid
  EEPROM.commit();
  EEPROM.end();
}

bool loadWiFiCredentials()
{
  EEPROM.begin(EEPROM_SIZE);
  
  if (EEPROM.read(VALID_FLAG_ADDR) != 1)
    return false;

  char ssidBuf[51] = {0};
  char passBuf[65] = {0};
  
  for (int i = 0; i < 50; i++)
    ssidBuf[i] = EEPROM.read(SSID_ADDR + i);
  for (int i = 0; i < 64; i++)
    passBuf[i] = EEPROM.read(PASS_ADDR + i);

  savedSSID = String(ssidBuf);
  savedPassword = String(passBuf);
  
  EEPROM.end();
  return savedSSID.length() > 0;
}

// ========================================
// HTML Page (embedded in code)
// ========================================
const char *htmlPage = "<!DOCTYPE html><html lang=\"en\"><head>"
                       "<meta charset=\"UTF-8\"><meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0\">"
                       "<title>Relay Control</title>"
                       "<style>"
                       "body{display:flex;flex-direction:column;align-items:center;justify-content:center;height:100vh;margin:0;"
                       "background:#1e1e1e;font-family:Arial, sans-serif;color:#eee;}"
                       "button{width:60%;height:50px;font-size:16px;margin:10px;border:none;border-radius:10px;cursor:pointer;color:#fff;"
                       "background:#3498db;}"
                       "input{width:55%;padding:10px;margin:5px;border-radius:6px;border:1px solid #555;background:#2a2a2a;color:#eee;}"
                       ".state-box{background:#2a2a2a;padding:20px 30px;border-radius:10px;text-align:center;margin-bottom:10px;}"
                       ".active-terminal{font-size:32px;font-weight:bold;color:#2ecc71;margin:10px 0;}"
                       ".terminal-row{display:flex;gap:15px;margin-top:10px;}"
                       ".terminal{padding:10px 20px;border-radius:6px;background:#444;font-size:14px;}"
                       ".terminal.active{background:#2ecc71;color:#000;font-weight:bold;}"
                       ".settings{margin-top:30px;border-top:1px solid #444;padding-top:20px;width:80%;text-align:center;}"
                       "</style></head>"
                       "<body>"
                       "<h2>Relay Control</h2>"
                       "<div class='state-box'>"
                       "<div>Common is currently connected to:</div>"
                       "<div class='active-terminal' id='activeTerminal'>--</div>"
                       "<div class='terminal-row'>"
                       "<div class='terminal' id='ncBox'>NC</div>"
                       "<div class='terminal' style='background:#666'>COM</div>"
                       "<div class='terminal' id='noBox'>NO</div>"
                       "</div></div>"
                       "<button onclick='toggleRelay()'>Toggle Relay</button>"
                       "<div class='settings'>"
                       "<h3>WiFi Settings</h3>"
                       "<input type='text' id='ssidInput' placeholder='WiFi Name'><br>"
                       "<input type='password' id='passInput' placeholder='WiFi Password'><br>"
                       "<button onclick='saveWifi()'>Save & Reconnect</button>"
                       "<div id='wifiMsg'></div>"
                       "</div>"
                       "<script>"
                       "function toggleRelay() {"
                       "  fetch('/toggle').then(updateStatus);"
                       "}"
                       "function updateStatus() {"
                       "  fetch('/status').then(r => r.text()).then(t => {"
                       "    var energized = t === 'ON';"
                       "    document.getElementById('activeTerminal').textContent = energized ? 'NO' : 'NC';"
                       "    document.getElementById('ncBox').className = 'terminal' + (energized ? '' : ' active');"
                       "    document.getElementById('noBox').className = 'terminal' + (energized ? ' active' : '');"
                       "  });"
                       "}"
                       "function saveWifi() {"
                       "  var ssid = document.getElementById('ssidInput').value;"
                       "  var pass = document.getElementById('passInput').value;"
                       "  if (!ssid) { alert('Enter a WiFi name'); return; }"
                       "  fetch('/savewifi?ssid=' + encodeURIComponent(ssid) + '&pass=' + encodeURIComponent(pass))"
                       "    .then(r => r.text()).then(t => {"
                       "      document.getElementById('wifiMsg').textContent = t;"
                       "    });"
                       "}"
                       "setInterval(updateStatus, 1500);"
                       "updateStatus();"
                       "</script></body></html>";

// ========================================
// Web Server Handlers
// ========================================
void handleRoot() { 
  server.send(200, "text/html", htmlPage); 
}

void handleToggle()
{
  // Relay is active-LOW (LOW = ON, HIGH = OFF) on most ESP-01S boards
  bool currentlyOn = digitalRead(relayPin) == LOW;
  digitalWrite(relayPin, currentlyOn ? HIGH : LOW);
  server.send(200, "text/plain", currentlyOn ? "OFF" : "ON");
}

void handleStatus()
{
  server.send(200, "text/plain", digitalRead(relayPin) == LOW ? "ON" : "OFF");
}

void handleSaveWifi()
{
  String newSSID = server.arg("ssid");
  String newPass = server.arg("pass");
  saveWiFiCredentials(newSSID, newPass);
  server.send(200, "text/plain", "Saved! Restarting and connecting to " + newSSID + "...");
  delay(1500);
  ESP.restart();
}

void handleNotFound() { 
  handleRoot(); 
}

// ========================================
// Setup
// ========================================
void setup()
{
  // Initialize relay pin
  pinMode(relayPin, OUTPUT);
  digitalWrite(relayPin, HIGH);  // Start with relay OFF (active-LOW)

  Serial.begin(115200);
  delay(1000);
  
  Serial.println("\n=== ESP-01S Relay Control ===");
  Serial.println("Relay pin: GPIO2");
  Serial.print("Chip ID: 0x");
  Serial.println(ESP.getChipId(), HEX);

  // ========================================
  // Load saved WiFi credentials
  // ========================================
  bool hasSaved = loadWiFiCredentials();
  String connectSSID = hasSaved ? savedSSID : String(defaultSSID);
  String connectPass = hasSaved ? savedPassword : String(defaultPassword);

  Serial.print("Connecting to: ");
  Serial.println(connectSSID);

  // ========================================
  // Connect to WiFi (both STA and AP modes)
  // ========================================
  WiFi.mode(WIFI_AP_STA);
  WiFi.begin(connectSSID.c_str(), connectPass.c_str());

  int attempts = 0;
  while (WiFi.status() != WL_CONNECTED && attempts < 20)
  {
    delay(500);
    Serial.print(".");
    attempts++;
  }

  if (WiFi.status() == WL_CONNECTED)
  {
    Serial.println(" Connected!");
    Serial.print("IP Address: ");
    Serial.println(WiFi.localIP());
  }
  else
  {
    Serial.println(" Failed to connect to WiFi.");
    Serial.println("AP mode will remain active for configuration.");
  }

  // ========================================
  // Start Access Point (always active)
  // ========================================
  String ssidWithChipId = String(apSSID) + "-" + String(ESP.getChipId(), HEX);
  WiFi.softAP(ssidWithChipId.c_str(), apPassword);
  
  Serial.print("AP SSID: ");
  Serial.println(ssidWithChipId);
  Serial.print("AP Password: ");
  Serial.println(apPassword);
  Serial.print("AP IP: ");
  Serial.println(WiFi.softAPIP());

  // ========================================
  // DNS Server (captive portal)
  // ========================================
  dnsServer.start(53, "*", WiFi.softAPIP());

  // ========================================
  // mDNS
  // ========================================
  if (MDNS.begin(ssidWithChipId))
  {
    MDNS.addService("http", "tcp", 80);
    Serial.println("mDNS started");
  }

  // ========================================
  // UDP Server
  // ========================================
  udp.begin(udpPort);
  Serial.print("UDP listening on port: ");
  Serial.println(udpPort);

  // ========================================
  // HTTP Web Server
  // ========================================
  server.on("/", handleRoot);
  server.on("/toggle", handleToggle);
  server.on("/status", handleStatus);
  server.on("/savewifi", handleSaveWifi);
  server.onNotFound(handleNotFound);
  server.begin();
  
  Serial.println("Web server started!");
  Serial.println("Open http://192.168.4.1 in your browser");
  Serial.println("========================================\n");
}

// ========================================
// Main Loop
// ========================================
void loop()
{
  dnsServer.processNextRequest();
  MDNS.update();
  server.handleClient();

  // ========================================
  // UDP Command Handler
  // ========================================
  int packetSize = udp.parsePacket();
  if (packetSize)
  {
    int len = udp.read(packetBuffer, 255);
    if (len > 0)
      packetBuffer[len] = '\0';
    
    DynamicJsonDocument doc(1024);
    if (!deserializeJson(doc, packetBuffer))
    {
      const char *cmd = doc["cmd"];
      if (cmd)
      {
        if (strcmp(cmd, "on") == 0)
          digitalWrite(relayPin, LOW);
        else if (strcmp(cmd, "off") == 0)
          digitalWrite(relayPin, HIGH);
        else if (strcmp(cmd, "toggle") == 0)
        {
          bool currentlyOn = digitalRead(relayPin) == LOW;
          digitalWrite(relayPin, currentlyOn ? HIGH : LOW);
        }
        
        Serial.print("UDP command received: ");
        Serial.println(cmd);
      }
    }
  }
}
The Critical Upload Trick
Why This Works
The ESP-01S checks GPIO0 only at power-up. If you power it after starting the upload (while GPIO0 is held LOW), it enters flash mode and the upload succeeds.

Step-by-Step Upload Process
Prepare the hardware:

Connect GPIO0 to GND with a jumper wire

Leave all other connections in place (VCC, GND, TX, RX, EN)

Do NOT power the ESP yet (keep USB unplugged or VCC disconnected)

Start the upload from PlatformIO:

Click the Upload (→) arrow in VS Code

The console will show: Connecting... with scrolling dots and underscores

The magic timing:

While Connecting... is scrolling, plug in the USB power (or reconnect VCC)

The ESP powers up with GPIO0 = LOW → enters flash mode immediately

The uploader detects it and starts writing

Complete the upload:

You'll see progress: Writing at 0x00000000... (6 %)

Once you see writing progress, remove the GPIO0 jumper

Wait for completion: Hash of data verified. and Hard resetting via RTS pin...

## Verification with esptool.py

To verify the ESP is responsive before uploading:

```sh
python -m esptool --port COM11 --baud 115200 flash-id
```

**Expected Success Output:**
text
Connected to ESP8266 on COM11:
Chip type:          ESP8266EX
Features:           Wi-Fi, 160MHz
Crystal frequency:  26MHz
MAC:                44:17:93:14:54:57

Flash Memory Information:
Manufacturer: 5e
Device: 6014
Detected flash size: 1MB
**If Connection Fails:**

```text
A fatal error occurred: Failed to connect to Espressif device: No serial data received.
```

Fix: Unplug USB, connect GPIO0 to GND, plug USB back in, then run the command within 2-3 seconds before the ESP boots into normal mode.

## Complete Upload Console Log

Here's what a successful upload looks like:

text
Dependency Graph
|-- ArduinoJson @ 6.21.5
|-- DNSServer @ 1.1.1
|-- EEPROM @ 1.0
|-- ESP8266mDNS @ 1.2
|-- ESP8266WebServer @ 1.0
|-- ESP8266WiFi @ 1.0
Building in release mode
Retrieving maximum program size .pio\build\generic_esp8266\firmware.elf
Checking size .pio\build\generic_esp8266\firmware.elf
RAM:   [====      ]  39.4% (used 32316 bytes from 81920 bytes)
Flash: [===       ]  32.0% (used 334195 bytes from 1044464 bytes)
Configuring upload protocol...
AVAILABLE: espota, esptool
CURRENT: upload_protocol = esptool
Looking for upload port...
Using manually specified: COM11
Uploading .pio\build\generic_esp8266\firmware.bin
esptool.py v3.0
Serial port COM11
Connecting....
Chip is ESP8266EX
Features: WiFi
Crystal is 26MHz
MAC: 4c:75:25:05:d9:dd
Uploading stub...
Running stub...
Stub running...
Configuring flash size...
Compressed 338352 bytes to 243802...
Writing at 0x00000000... (6 %)
Writing at 0x00004000... (13 %)
Writing at 0x00008000... (20 %)
Writing at 0x0000c000... (26 %)
Writing at 0x00010000... (33 %)
Writing at 0x00014000... (40 %)
Writing at 0x00018000... (46 %)
Writing at 0x0001c000... (53 %)
Writing at 0x00020000... (60 %)
Writing at 0x00024000... (66 %)
Writing at 0x00028000... (73 %)
Writing at 0x0002c000... (80 %)
Writing at 0x00030000... (86 %)
Writing at 0x00034000... (93 %)
Writing at 0x00038000... (100 %)
Wrote 338352 bytes (243802 compressed) at 0x00000000 in 21.5 seconds (effective 126.2 kbit/s)...
Hash of data verified.

Leaving...
Hard resetting via RTS pin...
============================================================== [SUCCESS] Took 25.39 seconds ==============================================================
## After Upload: Testing the Relay

### 1. Disconnect GPIO0 Jumper

Remove the jumper wire connecting GPIO0 to GND.

### 2. Power Cycle

Unplug and re-plug the USB power to restart the ESP.

### 3. Find the WiFi Network

Look for a WiFi network named: `witshells-relay-XXXXXX`
(Where XXXXXX is the last 6 digits of your chip's MAC address)

Password: `12ta442ta1`

### 4. Connect & Control

1. Connect your phone/PC to the ESP's WiFi network
2. Open a browser and go to: `http://192.168.4.1`

You'll see the relay control page:

- Status display showing **NC** (Normally Closed) or **NO** (Normally Open)
- **Toggle Relay** button to switch the relay state
- **WiFi Settings** section to save your home WiFi credentials

### 5. Add to Home WiFi (Optional)

1. On the web page, scroll to **WiFi Settings**
2. Enter your home WiFi SSID and password
3. Click **Save & Reconnect**

The ESP will restart and join your home network. The AP (`witshells-relay-XXXXXX`) will still remain active for fallback access.

## Troubleshooting Guide

### "Failed to connect: No serial data received"

**Cause:** GPIO0 wasn't pulled LOW during power-up.

**Fix:** Unplug USB, connect GPIO0 to GND, plug USB back in, then immediately run upload.

### "The process cannot access the file" (firmware.bin locked)

**Cause:** Another program (serial monitor, another VS Code instance) is using the file.

**Fix:** Close all serial monitors, restart VS Code, or run `pio run --target clean`.

### Relay doesn't click/respond

Possible causes:

- **Wrong relay pin:** Try changing `relayPin` to `0` or `2`
- **Wrong logic:** Your relay might be active-HIGH instead of active-LOW
- **Insufficient power:** ESP-01S relay boards need stable 3.3V supply

### ESP doesn't show WiFi network

Wait 10–15 seconds after power-up—the AP takes a moment to start.
If still not visible, check serial monitor output for error messages.

### Can't access web page after saving WiFi

Try both:

- `http://192.168.4.1` (AP mode)
- The IP address assigned by your router (check router's DHCP client list)

## Important: Use GPIO2, Not GPIO0

| GPIO  | Boot Behavior                          | Recommended for Relay?     |
| ----- | -------------------------------------- | -------------------------- |
| GPIO2 | Pulled HIGH internally at boot         | ✅ Safe to use              |
| GPIO0 | Must be HIGH at boot, LOW = flash mode | ❌ Avoid (can prevent boot) |

In the code:

```cpp
const int relayPin = 2;   // GPIO2 - safe for relay
```

Never use GPIO0 as an output in production. If your circuit pulls it LOW during boot, the ESP won't start.

## UDP Control (Advanced)

Your code listens for UDP commands on port 5555. Send JSON messages to control the relay:

```json
{"cmd":"on"}
{"cmd":"off"}
{"cmd":"toggle"}
```

### Example Python Script to Control via UDP

```python
import socket
import json

udp_ip = "192.168.4.1"  # Replace with your ESP's IP
udp_port = 5555

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
sock.sendto(json.dumps({"cmd": "toggle"}).encode(), (udp_ip, udp_port))
```
## Resources & Links

- [PlatformIO Documentation](https://docs.platformio.org/)
- [ESP8266 Reference](https://espressif.com/en/products/socs/esp8266)
- [ArduinoJson Library](https://arduinojson.org/)

## Summary

| Step | Action                                         |
| ---- | ---------------------------------------------- |
| 1    | Wire FTDI to ESP-01S (VCC, GND, TX↔RX, EN)     |
| 2    | Connect GPIO0 to GND (jumper)                  |
| 3    | Click Upload in PlatformIO                     |
| 4    | When `Connecting...` appears, power on the ESP |
| 5    | Wait for upload to complete                    |
| 6    | Remove GPIO0 jumper                            |
| 7    | Connect to `witshells-relay-XXXXXX` WiFi       |
| 8    | Open `http://192.168.4.1`                      |

The golden rule: **Power the ESP after starting the upload, not before.** This single trick makes the difference between "Failed to connect" and a successful flash.