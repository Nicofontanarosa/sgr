# RTT Geo-Location Anomaly Detector

What is RTT Geo-Location Anomaly Detector ?

RTT Geo-Location Anomaly Detector ( ***RTT GAD*** ) is a Wireshark plugin written in Lua that analyzes the Round-Trip Time ( RTT ) of TCP and TLS packets and compares it with an expected average value for the country associated with the IP address, using a MaxMind geolocation database. The goal is to determine whether the host is actually located in the region corresponding to its registered IP address ( *or if it is masking its true location using technologies such as VPNs, Tor, intermediate caches/CDNs, etc...* )

---

## 🤸 Quickstart

To get started with **RTT GAD**, follow these steps:

### 1️⃣ Install the MaxMind GeoIP2 Country Database

Download the **GeoLite2 Country** database ( *free with registration* ) or another GeoIP2 Country database from [MaxMind's official site](https://www.maxmind.com/en/geoip-databases)

You can also follow this helpful video tutorial for guidance by Chris Greer:   
🔗 [YouTube – How to Download GeoLite2](https://www.youtube.com/watch?v=IlVppluWTHw)

### 2️⃣ Install the Lua Plugin

Move the `rtt_check.lua` script to Wireshark’s plugin directory. Wireshark automatically loads Lua plugins placed in these directories depending on your OS:

- **Windows**:  
  `C:\Program Files\Wireshark\plugins\<version>\`
  
- **macOS**:  
  `/Applications/Wireshark.app/Contents/PlugIns/wireshark/<version>/`
  
- **Linux**:  
  `/usr/lib/wireshark/plugins/<version>/`

Copy your Lua script ( *rtt_check.lua* ) into Wireshark's plugin directory. If you're using the personal plugin directory, make sure it exists and create the plugins folder if necessary

#### GUI Method ( recommended ):

1. Open Wireshark
2. Click on `Help` → `About Wireshark`
3. Go to the `Folders` tab
4. Click the link next to `Personal Plugins`
5. Move the `rtt_check.lua` script into this folder

### 3️⃣ Restart Wireshark to load the plugin

### 4️⃣ Add the RTT Statistics File

Move the `ntp_rtt_stats.txt` file into the **main Wireshark directory** ( *same path where the main Wireshark executable is installed or where your plugin folder is located* )

### 5️⃣ Now you're ready to start analyzing RTT anomalies and detecting geo-location inconsistencies!

---


## ⚙️ Funzionamento

### 1. Raccolta RTT - `generator.py`
Questo script invia richieste NTP a server pubblici in diversi paesi, calcola:
- RTT medio (`mean`)
- Deviazione standard (`std`)
  
Il risultato è salvato in `ntp_rtt_stats.txt` nel seguente formato:
IT: 38.42 4.91 US: 105.89 12.32 JP: 278.56 18.97 ...

### 2. Analisi RTT - `rtt_check.lua`
Lo script Lua si integra in Wireshark come plugin:
- Analizza pacchetti TCP SYN o pacchetti ICMP Echo (ping)
- Calcola l’RTT osservato
- Confronta con i dati medi dal file `ntp_rtt_stats.txt`
- Se l’RTT osservato è significativamente diverso (>2×std), viene segnalato come **anomalia**

Ogni anomalia è visualizzata nella finestra di Wireshark, all’interno del tree del pacchetto, come:
[RTT Geo Check] RTT observed: 40 ms, Expected: 130 ± 20 ms → Possibile VPN / CDN

---

## 🧪 Esempio di utilizzo

1. Lancia lo script Python:
   ```bash
   python3 generator.py
Verrà generato ntp_rtt_stats.txt aggiornato.

Apri Wireshark con il plugin rtt_check.lua installato.

Analizza un file .pcap con traffico TCP o ICMP.
Il plugin evidenzierà pacchetti sospetti nella colonna "Info" o nel tree del pacchetto.

📌 Requisiti
Wireshark v3.4 o superiore con supporto a Lua

Python 3.x

Connessione Internet (per generare RTT reali)

Python Dependencies:
ntplib

json

concurrent.futures

Installa con:

bash
Copia
Modifica
pip install ntplib
🔎 Idee future e miglioramenti
Aggiunta del fingerprinting QUIC (versioni, user agent, parametri di handshake)

Esportazione automatica dei pacchetti anomali

Report PDF o HTML con classificazione degli IP sospetti

Integrazione con GeoIP (MaxMind) per visualizzare mappe

📄 Licenza
Progetto accademico realizzato per fini didattici e di ricerca sulla sicurezza informatica.
Autore: Nicolò Fontanarosa
Università: Università di Pisa
Anno: 2025

---

## 🙌 DISCLAIMER

While I do my best to detect location anomalies, I cannot guarantee that this software is error-free or 100% accurate. Please ensure that you respect users' privacy and have proper authorization to monitor, capture, and inspect network traffic

