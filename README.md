# Cloud Honeypot — T-Pot on Vultr

A public-facing honeypot deployed on a cloud server and left exposed to the open internet for 30 days to capture and observe real-world cyberattacks. Over the course of the month, the honeypot recorded **2 million+ attack attempts** from threat actors across the globe.

---

## Overview

This project involved deploying T-Pot — a multi-honeypot platform — on a cloud-based Ubuntu server intentionally exposed to the public internet with no restrictions. The goal was simple: see what happens when you put a server on the internet and leave it open.

T-Pot bundles multiple honeypot services that simulate vulnerable systems, alongside an ELK stack (Elasticsearch + Kibana) for real-time visualization and Suricata IDS for intrusion detection.

> <img width="1916" height="943" alt="image" src="https://github.com/user-attachments/assets/60339425-f5f7-41da-9021-4972724ba9ae" />

---

## Setup

1. Deployed **Ubuntu 24.04 LTS** on a Vultr cloud instance
2. Cloned the T-Pot repository and ran the installer
3. Configured login credentials
4. Post-install: SSH automatically moved to port **64295**
5. Accessed Kibana web UI via browser on port **64297**
6. Left all honeypot services exposed to the public internet for 30 days

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

## How It Works

T-Pot runs each honeypot service in its own isolated Docker container, allowing multiple fake vulnerable systems to run simultaneously on one server without conflicting with each other. Here's what each component does:

**Honeytrap** — A generic honeypot that listens on a wide range of ports and logs anything that connects to them. Designed to catch broad automated scanning activity.

**Cowrie** — Simulates an SSH and Telnet server. When attackers try to brute force their way in, Cowrie lets them "log in" with any credentials and records everything they type and every command they run, giving a window into exactly what attackers do once they think they have access.

**Dionaea** — Simulates vulnerable services like SMB and FTP commonly exploited by malware. It captures malware samples that attackers attempt to deploy.

**Heralding** — A credential harvesting honeypot that accepts logins across multiple protocols and logs every username and password combination attempted.

**Sentrypeer** — A VoIP honeypot that captures SIP scanning and fraud attempts targeting phone systems.

**ConPot** — Simulates industrial control systems to attract attackers targeting critical infrastructure.

**Elasticsearch + Kibana (ELK Stack)** — All logs from every honeypot service get stored in Elasticsearch and visualized through Kibana dashboards, making it easy to see attack trends, geographic origins, and credential patterns in real time.

**Suricata IDS** — Monitors all incoming network traffic at the packet level and categorizes what type of activity it sees — port scans, malformed packets, and known attack tool signatures. While the honeypot services log what attackers try to do, Suricata logs how they're doing it at the network level.

---

## What I Observed

### Attack Volume (30-day window)
The volume of attacks was the first thing that stood out. Within minutes of the server going live it was already receiving attack attempts — I didn't expect it to happen that fast.

| Honeypot | Attacks |
|----------|---------|
| Total | 2,000,000+ |
| Honeytrap | 1,000,000+ |
| Cowrie (SSH) | 616k |
| Dionaea | 227k |
| Heralding | 119k |
| Sentrypeer | 99k |

### Where Attacks Came From
Looking at the attack map and country breakdown, attacks were coming in from all over the world simultaneously — United States, Brazil, France, Romania, China, Netherlands and more. There was no single dominant source, which suggested to me that most of this was automated and not targeted at me specifically.

### What Credentials Were Being Tried
The username and password tag clouds were interesting to look at. The most common usernames were things like `root`, `admin`, `ubuntu`, and `administrator` — all default credentials you'd find on a freshly deployed Linux server. The passwords were just as predictable: `123456`, `password`, `admin`, and even blank passwords.

This made it clear that attackers aren't trying complex or targeted credentials — they're just spraying common defaults hoping someone left their server unsecured.

### How Quickly Attacks Started
Within minutes of the server going live it was already receiving probes. By the end of the first day there were already thousands of attempts logged — which showed me that internet-facing infrastructure has essentially zero grace period before it gets discovered.

---

## What I Learned

- **Servers get attacked immediately.** The moment a server is public-facing, it starts receiving probes and attack attempts. Security can't be an afterthought.

- **Most attacks are automated.** The geographic spread, the volume, and the credential patterns all point to bots and scripts — not people sitting at computers manually trying to break in.

- **Default credentials are a real problem.** Seeing `root`, `admin`, and blank passwords constantly being tried showed me why default credentials need to be changed immediately on any new system.

- **A honeypot gives you a window into real attacker behavior.** Instead of reading about how attackers operate in a textbook, I got to see it directly in the data — what they target, how they probe, and what they're looking for.

- **Deception is a legitimate security strategy.** By making attackers think they found a real vulnerable system, a honeypot wastes their time and resources while gathering intelligence on their methods.

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

This honeypot was deployed in an isolated cloud environment for educational and research purposes only. No production systems or user data were at risk. All captured data was used solely for learning.
