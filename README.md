# Security Homelab

A hands-on security lab forlearning all things security related. I switched from looking at tutorials and reading about stuff to just trying it out, building it, so i can understand it.

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                  ThinkPad X1 Carbon                  │
│               Fedora / KVM / Docker                  │
│                                                      │
│  ┌──────────┐  ┌──────────┐  ┌───────────────────┐  │
│  │ Kali VM  │  │  DVWA    │  │      Wazuh        │  │
│  │ Attacker │  │ (Docker) │  │  SIEM (Docker)    │  │
│  │ Nmap,    │  │ SQLi,XSS │  │  Alerts, Logs,    │  │
│  │ Wireshark│  │ Target   │  │  File Integrity   │  │
│  └──────────┘  └──────────┘  └───────────────────┘  │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │            Python EASM Scripts               │    │
│  │  DNS Recon, Cert Check, Shodan, SPF/DKIM     │    │
│  └──────────────────────────────────────────────┘    │
└──────────────────────┬──────────────────────────────┘
                       │ LAN
              ┌────────┴────────┐
              │  Raspberry Pi 5 │
              │    Pi-hole      │
              │  DNS Filtering  │
              └─────────────────┘
```

## Components

### DVWA (Damn Vulnerable Web App)
Intentionally vulnerable web application for practicing common web attacks.
- SQL Injection, XSS, Command Injection, Brute Force
- Running as Docker container

### Wazuh SIEM
Open-source security monitoring platform for log analysis and threat detection.
- Agent-based monitoring on ThinkPad
- File Integrity Monitoring
- Custom alert rules
- Attack detection from Kali scans

### Kali Linux VM
Penetration testing environment running in KVM/virt-manager.
- Nmap scanning against lab targets
- Wireshark packet capture and analysis
- Web application testing against DVWA

### Pi-hole (Raspberry Pi 5)
Network-wide DNS filtering and monitoring.
- DNS query logging and analysis
- Ad/tracker blocking
- Custom blocklists

### EASM Scripts
Python scripts for external attack surface reconnaissance.
- DNS record enumeration
- TLS certificate validation
- SPF/DKIM/DMARC verification
- Shodan API integration
- Multi-domain batch scanning

## Setup

### Prerequisites
- Fedora Linux with Docker and Docker Compose
- KVM/virt-manager for VMs
- Python 3.10+
- Raspberry Pi 5 with Raspberry Pi OS Lite

### Quick Start
```bash
# DVWA
docker run -d -p 8080:80 vulnerables/web-dvwa

# Wazuh
git clone https://github.com/wazuh/wazuh-docker.git
cd wazuh-docker/single-node
docker-compose up -d

# EASM Scripts
cd scripts/
pip install -r requirements.txt
python dns_lookup.py example.com
```

## Project Structure

```
security-homelab/
├── README.md
├── .gitignore
├── docker-compose/
│   ├── dvwa/
│   │   └── docker-compose.yml
│   └── wazuh/
│       └── docker-compose.yml
├── scripts/
│   ├── requirements.txt
│   ├── dns_lookup.py
│   ├── cert_checker.py
│   ├── subdomain_enum.py
│   ├── spf_dkim_check.py
│   └── shodan_scan.py
├── wazuh-rules/
│   └── custom_rules.xml
├── notes/
│   ├── dvwa-findings.md
│   ├── wazuh-setup.md
│   ├── nmap-results.md
│   └── malware-analysis/
└── screenshots/
```

## Findings & Learnings

*Updated as the lab grows.*

| Date | Activity | Key Learning |
|------|----------|-------------|
| | DVWA setup | |
| | Wazuh SIEM deployment | |
| | First Nmap scan + alert | |

## Tools & Technologies

- **Virtualization:** KVM/QEMU, virt-manager, Docker
- **Offensive:** Nmap, Wireshark, Kali Linux
- **Defensive:** Wazuh SIEM, Pi-hole, File Integrity Monitoring
- **Scripting:** Python (dnspython, ssl, shodan, requests)
- **Networking:** DNS, TLS/SSL, SPF/DKIM/DMARC

## License

This project is for educational purposes only. All scanning and testing is performed exclusively against systems I own and control.
