# 🍯 Cloud Honeypot — T-Pot on AWS EC2

A public-facing honeypot deployed on AWS to capture and analyze real-world cyberattacks. Within 24 hours of deployment, the honeypot recorded **60,000+ attack attempts** from across the globe, providing hands-on exposure to real attacker tactics, techniques, and procedures (TTPs).

---

## Overview

This project involved deploying T-Pot — a multi-honeypot platform — on a cloud-based Ubuntu server exposed to the public internet. The goal was to observe how attackers behave in the wild: what credentials they try, what ports they target, what tools they use, and where they originate from.

T-Pot bundles multiple honeypot services (Cowrie, Dionaea, Sentrypeer, ConPot, and more) alongside an ELK stack (Elasticsearch + Kibana) for real-time log ingestion, visualization, and analysis, as well as Suricata for IDS alerting.

---

## Architecture

```
Public Internet
      │
      ▼
AWS EC2 Instance (Ubuntu Server)
      │
      ├── T-Pot (Multi-Honeypot Platform)
      │     ├── Cowrie        (SSH/Telnet honeypot)
      │     ├── Dionaea       (Malware capture)
      │     ├── Sentrypeer    (VoIP honeypot)
      │     ├── ConPot        (ICS/SCADA honeypot)
      │     ├── Honeytrap     (Generic network honeypot)
      │     └── ...more
      │
      ├── ELK Stack
      │     ├── Elasticsearch (Log storage & indexing)
      │     └── Kibana        (Dashboards & visualization)
      │
      └── Suricata IDS        (Intrusion detection & alerting)
```

---

## Setup

### 1. AWS EC2 Instance
- Launched an Ubuntu Server EC2 instance with sufficient RAM to run T-Pot's full stack
- Generated an SSH key pair for secure remote access
- Configured security groups:
  - **Port 64295** — SSH access restricted to my IP only (T-Pot moves SSH here post-install)
  - **Port 64297** — Web UI access restricted to my IP only
  - **All other ports** (22, 80, 443, 25, 445, 3389, etc.) — open to the public internet for honeypot services to receive traffic

### 2. Connecting via Kali Linux VM
- Set up a Kali Linux VM locally
- Connected to the EC2 instance via SSH using the generated key pair credentials

### 3. T-Pot Installation
- Cloned the T-Pot repository from GitHub
- Ran the installer and completed setup
- Post-install: SSH session was automatically moved to port 64295 as expected
- Verified all honeypot services were running and accessible

### 4. Accessing the Dashboard
- Navigated to the Kibana web UI via `https://<EC2-IP>:64297`
- Confirmed live attack data was being ingested into Elasticsearch
- Explored pre-built T-Pot dashboards for real-time monitoring

---

## Results & Findings

### Attack Volume (24-hour window)
| Timeframe | Attacks |
|-----------|---------|
| Last 1 hour | 1,846 |
| Last 24 hours | 58,466 |
| Total (lifetime) | 60,000+ |

### Top Honeypots Targeted
| Honeypot | Attacks |
|----------|---------|
| Honeytrap | 26k |
| Cowrie (SSH) | 24k |
| Dionaea | 3k |
| Sentrypeer | 2k |
| ConPot | 2k |

### Top Attacking Countries
- United States, France, Mexico, Romania, China, Netherlands, South Korea, Germany

### Most Targeted Ports
- Port 22 (SSH), 5060 (SIP/VoIP), 445 (SMB), 5901 (VNC), 10001

### Suricata IDS Alert Categories
- Generic Protocol Command Activity
- Miscellaneous Activity
- Detection of a Network Scan
- Attempted Administrator Privilege Gain

### Attacker Credential Patterns
Common usernames attempted: `root`, `admin`, `ubuntu`, `user`, `postgres`, `deploy`, `test`

Common passwords attempted: `123456`, `password`, `admin`, `ubuntu`, `1234`, `(blank)`

This confirms mass automated credential stuffing and brute force attacks targeting default/weak credentials on exposed SSH and other services.

---

## Key Takeaways

- **Attackers are immediate.** Within minutes of the instance going public, attack attempts began — highlighting how continuously the internet is scanned.
- **Credential stuffing is rampant.** The most common attack pattern was automated brute force attempts using default and common credentials.
- **SSH (port 22) is a primary target.** Cowrie captured tens of thousands of SSH login attempts, confirming it remains one of the most attacked services on the internet.
- **Geographic distribution is global.** Attacks originated from over 10 countries simultaneously, with no single dominant source.
- **IDS complements honeypots.** Suricata's categorization added a layer of context beyond raw logs, helping distinguish between scan activity, brute force, and more targeted behavior.

---

## Tools & Technologies

| Tool | Purpose |
|------|---------|
| AWS EC2 | Cloud infrastructure |
| Ubuntu Server | Host OS |
| T-Pot | Multi-honeypot platform |
| Cowrie | SSH/Telnet honeypot |
| Dionaea | Malware capture honeypot |
| Elasticsearch | Log storage and indexing |
| Kibana | Dashboard and visualization |
| Suricata | Intrusion detection system |
| Kali Linux | Remote SSH client |

---

## Screenshots

> *Attack Map — Live feed showing global attack origins*

> *Kibana Dashboard — 60k+ attacks across all honeypot services*

> *Attack Analytics — Port histogram, country breakdown, OS fingerprinting*

> *Credential Analysis — Username and password tag clouds from brute force attempts*

---

## Disclaimer

This honeypot was deployed in an isolated cloud environment for educational and research purposes only. No production systems or user data were at risk. All captured data was used solely for threat analysis and learning.
