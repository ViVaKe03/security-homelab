# Homelab Plan

**Hardware:** ThinkPad X1 Carbon (32GB RAM, Fedora, KVM/virt-manager) + Raspberry Pi 5
**Ziel:** Security-Lab für Security+, EASM-Praxis, LocateRisk-Vorbereitung, Portfolio

---

## Phase 1 – Fundament

### Docker auf ThinkPad

- [ ] Docker + Docker Compose installieren (`sudo dnf install docker docker-compose`)
- [ ] Docker Service starten (`sudo systemctl enable --now docker`)
- [ ] User zur Docker-Gruppe (`sudo usermod -aG docker $USER`, dann neu einloggen)
- [ ] Test: `docker run hello-world`
- [ ] Test: `docker run -d -p 80:80 nginx` → Browser `localhost`
- [ ] Verstehen: Images, Container, Volumes, Ports, `docker ps`, `docker logs`, `docker-compose up/down`

### DVWA (Damn Vulnerable Web App)

- [ ] `docker run -d -p 8080:80 vulnerables/web-dvwa`
- [ ] Login: admin/password, Security Level auf Low
- [ ] SQL Injection durchspielen
- [ ] XSS (Reflected + Stored) durchspielen
- [ ] Command Injection durchspielen
- [ ] Ergebnisse dokumentieren (Screenshots, was passiert, warum)

### Pi-hole auf Raspberry Pi 5

- [ ] Raspberry Pi OS Lite flashen (Raspberry Pi Imager, SSH aktivieren)
- [ ] Pi headless per SSH verbinden
- [ ] Pi-hole installieren: `curl -sSL https://install.pi-hole.net | bash`
- [ ] ThinkPad DNS auf Pi-IP umstellen
- [ ] Pi-hole Dashboard anschauen – welche Queries laufen, was wird geblockt
- [ ] Eigene Blocklists hinzufügen

---

## Phase 2 – Monitoring & SIEM

### Wazuh (SIEM)

- [ ] Wazuh Docker Deployment clonen: `git clone https://github.com/wazuh/wazuh-docker.git`
- [ ] Single-Node starten: `cd wazuh-docker/single-node && docker-compose up -d`
- [ ] Wazuh Dashboard öffnen (https://localhost:443)
- [ ] Wazuh Agent auf ThinkPad installieren
- [ ] Prüfen: Logs kommen rein, Dashboard zeigt Events
- [ ] File Integrity Monitoring aktivieren – Testdatei ändern, Alert prüfen
- [ ] Eigene Alert-Rule schreiben (z.B. SSH-Login von unbekannter IP)

### Kali Linux VM

- [ ] Kali ISO runterladen
- [ ] VM in virt-manager erstellen (2 Cores, 4GB RAM, 40GB Disk)
- [ ] Netzwerk: NAT oder Isolated (je nach Szenario)
- [ ] Kali installieren, Snapshot machen (Clean State)
- [ ] Nmap installiert? Test: `nmap -sV localhost`

---

## Phase 3 – Angriff & Verteidigung

### Nmap Scanning

- [ ] Von Kali aus: `nmap -sV [ThinkPad-IP]` – offene Ports und Services
- [ ] Von Kali aus: `nmap -sV [Pi-IP]` – was ist sichtbar
- [ ] Von Kali aus: `nmap -A [DVWA-IP]` – Aggressive Scan
- [ ] In Wazuh prüfen: Hat das SIEM den Scan erkannt?

### DVWA angreifen + beobachten

- [ ] Von Kali aus DVWA attackieren (SQLi, XSS, Brute Force)
- [ ] Gleichzeitig Wazuh Dashboard offen – Alerts beobachten
- [ ] Dokumentieren: Angriff → Detection → Alert → was würde man als Response tun

### Netzwerk-Analyse

- [ ] Wireshark auf Kali: Traffic zwischen Kali und DVWA mitschneiden
- [ ] PCAP analysieren – was sieht man bei einem SQLi-Angriff im Netzwerk?
- [ ] DNS-Queries in Pi-hole beobachten während Scan läuft

---

## Phase 4 – EASM & Python Scripts

### DNS & Certificate Recon

- [ ] Python Script: DNS-Lookup für eine Domain (dnspython library)
- [ ] Python Script: TLS Certificate abrufen und Ablaufdatum prüfen (ssl + socket)
- [ ] Python Script: Subdomains checken (Wortliste + DNS-Abfrage)
- [ ] Python Script: SPF/DKIM/DMARC Records einer Domain auslesen

### Shodan API

- [ ] Shodan Account erstellen (kostenlos, API Key)
- [ ] Python Script: Domain/IP in Shodan abfragen, offene Ports auslesen
- [ ] Ergebnisse als JSON speichern und formatiert ausgeben

### Automatisierung

- [ ] Script das mehrere Domains aus einer Textdatei scannt
- [ ] Ergebnisse als Report (JSON oder Markdown) ausgeben
- [ ] Alles in einem sauberen Git-Repo mit README

---

## Phase 5 – Malware-Analyse (Fortgeschritten)

### Analyse-VM aufsetzen

- [ ] Windows 10 VM in virt-manager (4GB RAM, 60GB Disk)
- [ ] Netzwerk: ISOLATED – kein Internet, kein Zugang zum Host-Netz
- [ ] Kein Shared Folder mit Host
- [ ] Analyse-Tools installieren: pestudio, strings, Process Monitor, Autoruns
- [ ] Snapshot machen (Clean State)

### REMnux VM (optional)

- [ ] REMnux OVA runterladen und in virt-manager importieren
- [ ] Netzwerk: ISOLATED
- [ ] Tools testen: YARA, oletools, strings

### Samples analysieren

- [ ] MalwareBazaar (bazaar.abuse.ch) – Sample runterladen
- [ ] Statische Analyse: strings, pestudio, YARA Rules
- [ ] Dynamische Analyse: Sample in Windows VM ausführen, Process Monitor beobachten
- [ ] Wazuh Agent in der Windows VM → Alerts bei Malware-Ausführung prüfen
- [ ] Nach jeder Analyse: Snapshot zurücksetzen

---

## GitHub Repo Struktur

```
security-homelab/
├── README.md              # Architektur, was läuft wo, Diagramm
├── docker-compose/
│   ├── dvwa/
│   └── wazuh/
├── scripts/
│   ├── dns_lookup.py
│   ├── cert_checker.py
│   ├── subdomain_enum.py
│   ├── spf_dkim_check.py
│   └── shodan_scan.py
├── notes/
│   ├── dvwa-findings.md
│   ├── wazuh-setup.md
│   └── malware-analysis/
└── screenshots/
```

---

## Sicherheitsregeln

1. Malware-VM NIEMALS ans echte Netz oder Internet
2. Immer Snapshot vor Tests, danach zurücksetzen
3. Kali nur in isoliertem Netz oder gegen eigene Systeme scannen
4. Keine Scans gegen fremde IPs – ist illegal
5. Pi-hole DNS nur im eigenen Netz nutzen
