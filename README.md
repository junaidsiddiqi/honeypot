# Cloud Honeypot — T-Pot on Vultr

A public-facing honeypot deployed on a cloud server and exposed to the open internet to capture and analyze real-world cyberattacks. Over 30 days of operation, the honeypot recorded **2 million+ attack attempts** from threat actors across the globe, providing hands-on exposure to real attacker tactics, techniques, and procedures (TTPs).

---

## Overview

This project involved deploying T-Pot — a multi-honeypot platform — on a cloud-based Ubuntu server with no firewall restrictions, intentionally exposed to the public internet. The goal was to observe how attackers behave in the wild: what credentials they try, what ports they target, what tools they use, what vulnerabilities they exploit, and where they originate from.

T-Pot bundles multiple honeypot services alongside an ELK stack (Elasticsearch + Kibana) for real-time log ingestion and visualization, as well as Suricata IDS for intrusion detection and alert categorization.

> <img width="1916" height="943" alt="image" src="https://github.com/user-attachments/assets/60339425-f5f7-41da-9021-4972724ba9ae" />

---

## Setup

1. Deployed **Ubuntu 24.04 LTS** on a Vultr cloud instance
2. Cloned the T-Pot repository and ran the installer
3. Configured login credentials
4. Post-install: SSH automatically moved to port **64295**
5. Accessed Kibana web UI via browser on port **64297**
6. All honeypot services exposed to the public internet — no firewall restrictions

---

## Architecture

```
Public Internet
      │
      ▼
Vultr Cloud Instance (Ubuntu 24.04)
      │
      ├── T-Pot (Multi-Honeypot Platform)
      │     ├── Honeytrap     (Generic network honeypot)
      │     ├── Cowrie        (SSH/Telnet honeypot)
      │     ├── Dionaea       (Malware capture)
      │     ├── Heralding     (Credential capture)
      │     ├── Sentrypeer    (VoIP honeypot)
      │     ├── ConPot        (ICS/SCADA honeypot)
      │     └── ...more
      │
      ├── ELK Stack
      │     ├── Elasticsearch (Log storage & indexing)
      │     └── Kibana        (Dashboards & visualization)
      │
      └── Suricata IDS        (Intrusion detection & alerting)
```

---

## Results & Findings

### Attack Volume (30-day window)
| Honeypot | Attacks |
|----------|---------|
| Total | 2,000,000+ |
| Honeytrap | 1,000,000+ |
| Cowrie (SSH) | 617k |
| Dionaea | 227k |
| Heralding | 119k |
| Sentrypeer | 99k |
| ConPot | 36k |

### Top Attacking Organizations (ASN)
| Organization | Attack Count |
|-------------|-------------|
| DigitalOcean, LLC | 487,379 |
| Unmanaged Ltd | 109,704 |
| W-NET TELECOM | 106,291 |
| Alsycon B.V. | 102,211 |
| OVH SAS | 79,244 |
| Contabo GmbH | 47,950 |
| Akamai Connected Cloud | 45,109 |

The dominance of cloud providers like DigitalOcean and OVH confirms that attackers heavily rely on cheap VPS infrastructure to host automated attack bots, making it easy to rotate IPs and scale attacks.

### Top Attacking Countries
United States, Brazil, France, Romania, China, Netherlands, Canada, Bulgaria, Hong Kong

### Most Targeted Ports
- **5901-5903** — VNC (remote desktop)
- **5060** — SIP/VoIP
- **22** — SSH
- **445** — SMB
- **443** — HTTPS

### CVEs Being Actively Exploited
| CVE | Count |
|-----|-------|
| CVE-2020-11900 | 24 |
| CVE-2020-10173 | 18 |
| CVE-2020-11910 | 7 |
| CVE-2019-12263 | 6 |

Real attackers were actively attempting to exploit known vulnerabilities years after their disclosure, confirming that unpatched systems remain a primary attack target in the wild.

### Suricata IDS Top Alerts
| Alert | Count |
|-------|-------|
| SURICATA IPv4 truncated packet | 639,744 |
| SURICATA AF-PACKET truncated packet | 639,744 |
| SURICATA STREAM Packet with broken ack | 435,611 |
| ET SCAN Zmap User-Agent (Inbound) | 71,115 |
| ET INFO SSH session in progress on Expected Port | 29,285 |

The **71,115 Zmap scan detections** indicate large-scale automated internet-wide reconnaissance — Zmap is a tool used to scan the entire IPv4 address space in under an hour, confirming that public-facing infrastructure gets discovered and attacked almost immediately after going live.

### Attacker Credential Patterns
**Most common usernames attempted:** `root`, `admin`, `ubuntu`, `user`, `administrator`, `support`, `postgres`

**Most common passwords attempted:** `123456`, `password`, `ubuntu`, `(blank)`, `admin`, `12345678`

**Notable finding:** Credentials including `solana`, `firedancer`, `validator`, and `345gs5662d34` appeared prominently in both username and password tag clouds. These are associated with Solana blockchain validator node infrastructure, indicating a targeted campaign specifically hunting for cryptocurrency validator nodes to compromise and steal from — not just generic brute force activity.

---

## Key Takeaways

- **The internet is constantly being scanned.** Within minutes of the server going live, attack attempts began. There is no such thing as security through obscurity for public-facing infrastructure.

- **Most attacks are fully automated.** The dominance of Linux-based attacking systems, cloud provider ASNs, and tools like Zmap confirms that the vast majority of attacks are bots and scripts, not humans manually targeting systems.

- **Attackers actively exploit known CVEs years after disclosure.** Seeing CVEs from 2019 and 2020 still being actively exploited in 2026 reinforces why patch management is a critical security function.

- **Credential stuffing targets default and common credentials.** The most attempted usernames and passwords are default system credentials, confirming that systems with weak or default passwords are compromised rapidly.

- **Targeted campaigns exist within the noise.** The presence of Solana validator credentials in the attack data shows that beyond generic brute force, specific threat actors are running targeted campaigns against niche infrastructure like cryptocurrency nodes.

- **Threat intelligence comes from real data.** Analyzing attack patterns, geographic origins, ASN data, and credential patterns provides actionable intelligence that can inform defensive security decisions.

---

## Tools & Technologies

| Tool | Purpose |
|------|---------|
| Vultr | Cloud infrastructure |
| Ubuntu 24.04 LTS | Host OS |
| T-Pot | Multi-honeypot platform |
| Cowrie | SSH/Telnet honeypot |
| Dionaea | Malware capture honeypot |
| Heralding | Credential capture honeypot |
| Honeytrap | Generic network honeypot |
| Elasticsearch | Log storage and indexing |
| Kibana | Dashboard and visualization |
| Suricata | Intrusion detection system |

---

## Screenshots
> <img width="1914" height="775" alt="image" src="https://github.com/user-attachments/assets/71202721-5333-4eed-90b0-da1ba3f079e9" />

> <img width="1914" height="763" alt="image" src="https://github.com/user-attachments/assets/5eecd19e-090b-4180-8107-7c76b43bf446" />

> <img width="1918" height="403" alt="image" src="https://github.com/user-attachments/assets/568ac6f0-3d44-4d68-b87d-4437705bfe0d" />

> <img width="1914" height="944" alt="image" src="https://github.com/user-attachments/assets/775159c5-3fba-4f98-a91e-0634b557ea91" />

---

## Disclaimer

This honeypot was deployed in an isolated cloud environment for educational and research purposes only. No production systems or user data were at risk. All captured data was used solely for threat analysis and learning.
