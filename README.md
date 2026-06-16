# 🖥️ Tecnure Serveis — IT Infrastructure for Grup Alimentari Guissona

![Debian](https://img.shields.io/badge/Debian-A81D33?style=flat&logo=debian&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat&logo=amazonaws&logoColor=white)
![WireGuard](https://img.shields.io/badge/WireGuard-88171A?style=flat&logo=wireguard&logoColor=white)
![Apache](https://img.shields.io/badge/Apache2-D22128?style=flat&logo=apache&logoColor=white)
![Wazuh](https://img.shields.io/badge/Wazuh-blue?style=flat)
![Nextcloud](https://img.shields.io/badge/Nextcloud-0082C9?style=flat&logo=nextcloud&logoColor=white)
![Grade](https://img.shields.io/badge/Grade-10%2F10-brightgreen?style=flat)

> **Final Grade: 10/10** · SMR (Sistemes Microinformàtics i Xarxes) · IES Thos i Codina · Mataró · 2025–2026

---

## 📋 Project Overview

**Tecnure Serveis** is a fictional IT consultancy created as the final cross-modular project of 2nd year SMR. The project consists of the **complete implementation of an IT department from scratch** for Grup Alimentari Guissona, a Catalan agri-food cooperative with over **4,300 employees** and **€15M annual turnover**.

Tecnure Serveis acted as an external technology provider, taking on all the functions of an internal IT department: network design, servers, cybersecurity, automation, monitoring and backups.

---

## 👥 Team

| Member | Role |
|--------|------|
| **Amadou Traore Keita** | Infrastructure, Systems, Security & Automation Lead |
| **Willy Anthony Machuca Sierra** | Physical Systems, Networks & Design Lead |

---

## 🏗️ Architecture

```
Internet (DIGI Fiber) — Public IP: 79.xxx.xxx.xxx → guissona.ddns.net
         │
    Router/Firewall (10.3.2.1) — Ports: 443, 80, 51820
         │
    ┌────┴────────────────────────────────┐
    │                                     │
server-guissona (10.3.2.200)         ad-server (10.3.2.203)
Raspberry Pi 4 · Debian              Windows Server
    │                                Active Directory + DNS
    ├── Apache2 Reverse Proxy
    ├── WordPress + Let's Encrypt → guissona.ddns.net
    ├── Postfix + Rainloop → correu.guissona.test
    ├── WireGuard VPN (port 51820)
    ├── Tailscale (contingency VPN)
    ├── Wazuh Agent ──────────────────→ siem-guissona (10.3.2.201) AWS
    ├── Suricata IDS/IPS                  Wazuh Manager + Indexer + Dashboard
    ├── Fail2ban                          n8n SOAR → alerts to technicians
    ├── Grafana + Prometheus → monitor.guissona.test
    ├── Bacula client → backup.guissona.test → SSD 500GB
    │
    └── DOCKER (3 services)          syslog-guissona (10.3.2.202)
        ├── Nextcloud → drive.guissona.test     Graylog · 8GB RAM · 4 CPU
        ├── n8n → auto.guissona.test
        └── ProFTPD → sftp.guissona.test
```

---

## 🔧 Technologies

### Infrastructure & Network
- **Topology:** Star of stars
- **Server:** Raspberry Pi 4 (ARM Cortex-A72, 4GB RAM, 64GB MicroSD)
- **OS:** Debian 12 Bookworm
- **Network:** 10.3.2.0/24 · Switch TP-Link · DIGI Fiber ISP
- **DDNS:** Dynamic IP → `guissona.ddns.net`

### Services

| Service | Technology | URL | Environment |
|---------|-----------|-----|-------------|
| Corporate website | WordPress | `guissona.ddns.net` | Public |
| SSL Certificate | Let's Encrypt | all domains | Public |
| Corporate email | Postfix + Rainloop | `correu.guissona.test` | Private |
| Corporate drive | Nextcloud | `drive.guissona.test` | Private |
| Secure FTP | ProFTPD | `sftp.guissona.test` | Private |
| Monitoring | Grafana + Prometheus | `monitor.guissona.test` | Private |
| Backup | Bacula + Bacularis | `backup.guissona.test` | Private |
| Automation | n8n | `auto.guissona.test` | Private |
| Internal DNS | BIND9 | `.test` zones | Internal |
| Reverse Proxy | Apache2 mod_proxy | all subdomains | Internal |
| Main VPN | WireGuard | port 51820 | Internal |
| Backup VPN | Tailscale | contingency | Internal |

### 🔒 Cybersecurity — SOC

| Layer | Tool | Function |
|-------|------|----------|
| **SIEM** | Wazuh (Agent → AWS) | Real-time incident detection & response |
| **IDS/IPS** | Suricata | Network traffic analysis & intrusion detection |
| **IPS** | Fail2ban | Automatic brute force blocking |
| **SOAR** | n8n | Alert automation → summary to technicians |
| **Audit** | Lynis | System hardening audit (**score: 80/100**) |
| **Pentest** | Kali Linux | Internal penetration testing (Nmap, Burp Suite, Nikto) |
| **Encryption** | SSL/TLS Let's Encrypt | All communications encrypted |

> 💡 **Distributed Wazuh architecture:** agent runs on the Raspberry Pi and sends all events to the Wazuh server hosted on **AWS EC2 (10.3.2.201)**, with real-time monitoring, alerts and centralized dashboard.

### 📊 Monitoring & Logs

| Tool | Function |
|------|----------|
| Grafana | Visual dashboards of system metrics |
| Prometheus | Metrics collection engine |
| Graylog | Centralized log collection from ALL servers (dedicated server 10.3.2.202) |
| Wireshark | Network traffic forensic analysis |

### 💾 Backup

- **Bacula + Bacularis:** enterprise backup jobs (Full + Incremental)
- **Storage:** Physical external SSD 500GB NAS
- **Schedule:** Daily and weekly automated backups

---

## 📁 Repository Structure

```
tecnure-serveis/
│
├── README.md
├── docs/
│   ├── memoria-tecnica.pdf          # Full technical report (90+ pages)
│   └── presentacio.pdf              # Project presentation slides
│
├── diagrama/
│   └── arquitectura.png             # Full infrastructure diagram
│
├── infraestructura/
│   ├── xarxa/                       # Network diagrams, IP tables, VLANs
│   └── active-directory/            # OU structure, GPOs, users
│
├── serveis/
│   ├── apache2-reverseproxy/        # Virtual Hosts + SSL config
│   ├── dns-bind9/                   # Internal DNS zones (.test)
│   ├── vpn-wireguard/               # WireGuard config
│   ├── nextcloud/                   # Nextcloud setup
│   ├── wordpress/                   # WordPress + SSL
│   ├── rainloop/                    # Webmail config
│   └── monitoratge/                 # Grafana dashboards, Prometheus, Graylog
│
├── seguretat/
│   ├── wazuh/                       # Agent config + AWS server notes
│   ├── suricata/                    # IDS/IPS rules
│   ├── fail2ban/                    # Configured jails
│   ├── lynis/                       # Audit results (80/100)
│   └── pentesting/                  # Kali Linux test notes & results
│
├── automatitzacio/
│   └── n8n/                         # SOAR workflows & alerts
│
└── backup/
    └── bacula/                      # Jobs, schedules & Bacularis config
```

---

## 📊 Key Results

- ✅ **Lynis Hardening Score: 80/100**
- ✅ Full SOC: Suricata + Wazuh + n8n SOAR (automatic alerts to technicians)
- ✅ Distributed Wazuh: local agent + AWS server
- ✅ HTTPS on all services with Let's Encrypt (Apache2 reverse proxy)
- ✅ Dual redundant VPN: WireGuard (main) + Tailscale (contingency)
- ✅ Automated daily & weekly backups with Bacula on SSD NAS
- ✅ 24/7 monitoring: Grafana + Prometheus + Graylog

---

## 📄 Documentation

All technical documentation is in the [`/docs`](./docs/) folder:
- **Technical report** (90+ pages): design, implementation and testing of all services
- **Presentation** (17 slides): visual project summary

---

## 🎓 Academic Context

- **Degree:** Sistemes Microinformàtics i Xarxes (SMR) — 2nd year
- **School:** IES Thos i Codina · Mataró
- **Year:** 2025–2026
- **Grade:** 10/10 ✨
- **Authors:** Amadou Traore Keita & Willy Anthony Machuca Sierra

---

## 📬 Contact

**Amadou Traore Keita**
Future ASIR student · Cloud · Cybersecurity · Sysadmin
📜 Certifications: SC-900 · AI-900 · CSST Cybersecurity · (in progress: AZ-900, AZ-104, CCST Networking)
🔗 [LinkedIn](https://www.linkedin.com/in/amtraorek)
🐙 [GitHub](https://github.com/amtraorek)
