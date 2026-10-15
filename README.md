# BH-INTRCPTOR 🚀
**Advanced Heating Control Daemon & Web Portal**

Developed by **Heiko Zschenderlein "Vantrix"**  
Country: Germany | Web: [www.brainhack.eu](http://www.brainhack.eu) | Contact: vantrix@brainhack.eu  
Version: 1.0 (2026)

---

> 🇩🇪 **Deutsche Version:** [Klicke hier für das deutsche Handbuch](#-deutsches-handbuch)  
> 🇬🇧 **English Version:** [Click here for the English manual](#-english-manual)

---

## 🇩🇪 Deutsches Handbuch

### 1. Systemüberblick
Der **BH-INTRCPTOR** ist ein in C++ geschriebenes, hochstabiles System zur direkten lokalen (Serial/USB) oder netzwerkbasierten (TCP/ser2net) Steuerung von Heizungsanlagen. Das System besteht aus zwei ausfallsicheren, als Systemd-Hintergrunddienste laufenden Komponenten:
1. **Hardware & MQTT Daemon:** Kommuniziert mit der Heizung, filtert Protokollfehler und pusht die Daten via MQTT an Smart-Home-Systeme (Home Assistant, Domoticz).
2. **Web Configuration Portal:** Ein eingebetteter, pfeilschneller Webserver (Port 8093) zur Live-Konfiguration des Systems und der Parameter.

### 2. Hauptfunktionen (Features)
* **Unstoppable Systemd Services:** Beide Dienste sind mit `StartLimitIntervalSec=0` konfiguriert. Bei einem unerwarteten Absturz startet Linux das System innerhalb von 3 Sekunden sofort und endlos oft neu.
* **Bulletproof TCP/IP & Serial Handling:** Der Daemon fängt Netzwerk-Abbrüche (SIGPIPE) ab, säubert vor jeder Abfrage rigoros TCP-Puffer (verhindert "verrutschte" Werte durch Timeouts) und unterstützt Keepalives sowie NoDelay-Parameter.
* **Smart Home Auto-Discovery:** Vollautomatische Integration in Home Assistant. Reglerwerte erhalten einen Slider mit erweitertem Minus-Bereich (bis -50), Fehler-Resets werden automatisch als Push-Button (Taster) konfiguriert.
* **Zero-Wear Live Data (RAM-Disk):** Das Web-Portal holt sich Live-Daten direkt aus dem Linux-Arbeitsspeicher (`/tmp/bh_live.json`), um SD-Karten-Verschleiß komplett zu vermeiden.
* **Sicherheit:** Das Web-Portal ist direkt in die Binärdatei eingebacken (keine manipulierbaren HTML-Dateien auf dem Server). Die Binärdateien sind "stripped" (unlesbarer Maschinencode). MQTT unterstützt volle TLS/SSL Verschlüsselung.

### 3. Installation
Das System wird als portables Debian-Paket (`.deb`) verteilt, welches Abhängigkeiten (`libmosquitto1`) automatisch auflöst.

Kopiere das `.deb` Paket auf deinen Raspberry Pi / Linux-Rechner und führe aus:
```bash
sudo apt install ./bh-intrcptor_1.0_*.deb -y
```
Das Paket installiert alle Dateien nach `/opt/bh-intrcptor/` und startet die Hintergrunddienste sofort.

**Dienste prüfen / Logs lesen:**
```bash
systemctl status bh-portal
systemctl status bh-daemon
journalctl -u bh-daemon -f
```

### 4. Konfiguration (Web-Portal)
Öffne nach der Installation deinen Browser und navigiere zu:
👉 `http://<IP-DEINES-PI>:8093`

* **Standard-Passwort (Admin):** `admin123`
* **Fachmann-PIN:** `8844`

Im Portal kannst du die Verbindungsart (Lokal / TCP-Remote / Virtuell), deine MQTT-Zugangsdaten inkl. TLS-Zertifikaten und die genauen Smart-Home-Typen deiner Parameter (Temperatur, Schalter, Button, Selector) konfigurieren. Der "Fachmann"-Modus (Entsperrung über das Schloss-Symbol) erlaubt es dir, Schreibrechte für einzelne Sensoren festzulegen.

### 5. Fallback & Notfall-Lösungen (Lost Password)
Falls du dich ausgesperrt hast oder die Fachmann-PIN vergessen wurde, bietet das System einen hardwarebasierten Notfall-Reset:

1. Verbinde dich per SSH auf deinen Raspberry Pi.
2. Erstelle eine leere Datei namens `emergency_reset.txt` im Datenordner:
   ```bash
   sudo touch /opt/bh-intrcptor/data/emergency_reset.txt
   ```
3. Starte das Portal neu:
   ```bash
   sudo systemctl restart bh-portal
   ```
Der Server erkennt die Datei, setzt sofort das Admin-Passwort auf `admin123` und die PIN auf `8844` zurück und löscht die Reset-Datei anschließend automatisch. Deine übrigen Netzwerkeinstellungen bleiben erhalten!

### 6. Lizenz & Haftungsausschluss
**Freeware / Non-Commercial Use Only**
Dieses Softwarepaket ("BH-INTRCPTOR") wird als Freeware ausschließlich für private, nicht-kommerzielle Zwecke zur Verfügung gestellt ("free to copy, use at own risk"). Jegliche kommerzielle Nutzung oder Integration in kommerzielle Produkte ohne vorherige schriftliche Zustimmung des Autors ist untersagt. 

*Haftungsausschluss:* Da die Software direkte Eingriffe in Heizungsanlagen vornimmt, trägt der Nutzer die alleinige Verantwortung. Der Autor schließt jegliche Haftung für Hardwareschäden oder Fehlfunktionen aus (gemäß der beiliegenden `DISCLAIMER_LICENSE.txt`).

---

## 🇬🇧 English Manual

### 1. System Overview
**BH-INTRCPTOR** is a highly stable C++ system for direct local (Serial/USB) or network-based (TCP/ser2net) control of heating systems. It operates via two fail-safe systemd background services:
1. **Hardware & MQTT Daemon:** Communicates with the heating unit, filters protocol anomalies, and pushes telemetry via MQTT to Smart Home platforms (Home Assistant, Domoticz).
2. **Web Configuration Portal:** A blazing-fast embedded web server (Port 8093) for live system and parameter configuration.

### 2. Main Features
* **Unstoppable Systemd Services:** Configured with `StartLimitIntervalSec=0`. If a crash occurs, the Linux OS restarts the daemon instantly and infinitely within 3 seconds.
* **Bulletproof TCP/IP & Serial Handling:** Captures broken pipes (SIGPIPE), aggressively purges TCP buffers before querying to prevent "slipped" telemetry, and features Keepalive/NoDelay handling.
* **Smart Home Auto-Discovery:** Fully automated Home Assistant integration. Setpoints receive sliders supporting negative values (down to -50). Fault resets are correctly identified and configured as push buttons.
* **Zero-Wear Live Data (RAM-Disk):** The frontend fetches live values directly from a memory-mapped RAM disk (`/tmp/bh_live.json`), avoiding SD card degradation.
* **Security:** The HTML/CSS portal is directly baked into the C++ binary (no modifiable HTML files). Binaries are stripped to prevent reverse engineering. Full TLS/SSL support for MQTT.

### 3. Installation
The software is distributed as a portable Debian package (`.deb`), automatically resolving prerequisites (e.g., `libmosquitto1`).

Transfer the `.deb` file to your Raspberry Pi / Linux machine and execute:
```bash
sudo apt install ./bh-intrcptor_1.0_*.deb -y
```
This installs the system to `/opt/bh-intrcptor/` and automatically starts the background services.

**Check Service Status / Logs:**
```bash
systemctl status bh-portal
systemctl status bh-daemon
journalctl -u bh-daemon -f
```

### 4. Configuration (Web Portal)
After installation, open your browser and navigate to:
👉 `http://<IP-OF-YOUR-PI>:8093`

* **Default Admin Password:** `admin123`
* **Expert PIN (Fachmann):** `8844`

Use the portal to configure your connection type (Local / TCP-Remote / Virtual), MQTT credentials (incl. TLS certificates), and specific Smart Home types for your parameters (Temperature, Switch, Button, Selector). The Expert mode (unlocked via the padlock icon) allows you to define read/write permissions for every single sensor.

### 5. Fallback & Emergency Reset (Lost Password)
If you lock yourself out or forget the Expert PIN, the system provides a hard-reset mechanism:

1. Connect to your Raspberry Pi via SSH.
2. Create an empty file named `emergency_reset.txt` in the data directory:
   ```bash
   sudo touch /opt/bh-intrcptor/data/emergency_reset.txt
   ```
3. Restart the portal service:
   ```bash
   sudo systemctl restart bh-portal
   ```
The portal detects the file, instantly resets the Admin password to `admin123` and the PIN to `8844`, and automatically deletes the trigger file. Your other network and MQTT settings remain untouched!

### 6. License & Disclaimer
**Freeware / Non-Commercial Use Only**
This software ("BH-INTRCPTOR") is provided "as is" and is free to copy for personal, non-commercial use only ("use at own risk"). Any commercial application or integration into commercial products without prior written consent from the author is strictly prohibited.

*Disclaimer:* As this software performs direct hardware manipulation on heating controllers, the user assumes all responsibility. The author disclaims any liability for hardware damage or malfunction resulting from the use of this software (as outlined in the included `DISCLAIMER_LICENSE.txt`).
