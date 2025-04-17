# RTT Geo-Location Anomaly Detector

RTT Geo-Location Anomaly Detector is a Wireshark plugin written in Lua that analyzes the Round-Trip Time ( RTT ) of TCP and TLS packets and compares it with an expected average value for the country associated with the IP address, using a MaxMind geolocation database. The goal is to determine whether the host is actually located in the region corresponding to its registered IP address ( *or if it is masking its true location using technologies such as VPNs, Tor, intermediate caches/CDNs, etc...* )

---

## 📁 Struttura del Progetto

rtt-geo-checker/ │ ├── generator.py # Script Python per generare RTT medi per ogni paese usando server NTP ├── rtt_check.lua # Plugin Lua per Wireshark che analizza RTT dei pacchetti osservati │ ├── parameters.json # Configurazione del sistema (timeout, sample_size, ecc.) ├── country.json # Mappa dei codici paese (ISO Alpha-2 → nome esteso) ├── ntp_rtt_stats.txt # RTT medi e deviazione standard per ogni paese (output del Python) │ └── README.md # Questo file

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
