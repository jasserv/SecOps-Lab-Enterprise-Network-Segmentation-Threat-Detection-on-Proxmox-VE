# SecOps-Lab-Enterprise-Network-Segmentation-Threat-Detection-on-Proxmox-VE
Built a segmented enterprise security lab on Proxmox VE using OPNsense, FortiGate-VM, Suricata IDS, and Wazuh SIEM. Simulated real attacks from Kali Linux across isolated network zones, automated alert triage with Python, and produced incident response documentation.

# Enterprise Dual-Firewall Segmented Lab & Detection Engineering Pipeline

## Executive Summary
This project demonstrates the architecture, deployment, and validation of a highly segmented enterprise network environment hosted on a single-node hypervisor. The lab features a dual-firewall topology (Perimeter and Internal) designed to mimic a corporate infrastructure, an isolated attacker zone, and a dedicated corporate zone protected by a Network Intrusion Detection System (NIDS) and a centralized Security Information and Event Management (SIEM) pipeline.

The primary objective of this environment is to safely simulate advanced adversary tactics and techniques (MITRE ATT&CK) and engineer robust, custom detection logic to identify them.

---

## Network Architecture
The infrastructure utilizes explicit hardware/software isolation via distinct virtual switches (Linux Bridges) to enforce strict boundary controls.

* **`vmbr0` (WAN):** External bridging providing real-world internet connectivity to the perimeter.
* **`vmbr1` (Attacker Zone):** An isolated segment containing the adversarial toolkit (Kali Linux), completely decoupled from the corporate network.
* **`vmbr2` (DMZ / Transit Zone):** A highly regulated buffer zone sitting between the Perimeter and Internal firewalls.
* **`vmbr3` (CORP Zone):** The deepest internal segment housing protected corporate assets, the NIDS engine, and the SIEM indexer.

---

## Hardware & System Specifications
* **Host CPU:** AMD Ryzen 5 7600X (6 Cores / 12 Threads)
* **Host RAM:** 32 GB DDR5 6000MHz
* **Host Storage:** 256 GB NVMe SSD (Optimized with Thin Provisioning & Discard flags)
* **Hypervisor:** Proxmox VE

---

# Enterprise Cybersecurity Homelab

> A production-inspired enterprise cybersecurity homelab built on **Proxmox VE** featuring layered firewall architecture, network segmentation, virtualization, and centralized security monitoring. This project is designed to simulate an enterprise environment for learning **Firewall Engineering, Network Security, Blue Team Operations, and SOC Engineering**.

---

## 📌 Project Overview

This project documents the design and deployment of a multi-tiered enterprise cybersecurity homelab. The lab follows industry best practices by implementing a layered security architecture with multiple network zones protected by dedicated firewalls.

The environment is continuously expanded to include intrusion detection, security monitoring, log management, and attack simulation.

---

# 🎯 Objectives

- Design an enterprise-inspired segmented network.
- Deploy a dual-firewall architecture.
- Separate Attacker, DMZ, and Corporate networks.
- Configure Layer 3 routing between isolated segments.
- Implement outbound NAT and secure internet access.
- Deploy centralized logging and SIEM.
- Implement IDS/IPS for threat detection.
- Simulate enterprise attack and defense scenarios.

---

# 🏗️ Current Architecture

```
                               INTERNET
                                   │
                           Home Router
                                   │
                            vmbr0 (WAN)
                                   │
                          +----------------+
                          |    OPNsense    |
                          | Perimeter FW   |
                          +----------------+
                           │            │
             vmbr1         │            │ vmbr2
        Attacker Zone      │           DMZ
        172.16.1.0/24       │      10.0.0.0/24
                           │
                      Kali Linux
                           │
                     Ubuntu SIEM
                    (10.0.0.10/24)
                           │
                     +---------------+
                     |   FortiGate   |
                     | Internal FW   |
                     +---------------+
                    10.0.0.2
                    10.50.10.1
                           │
                    vmbr3 Corporate
                    10.50.10.0/24
                           │
                     Ubuntu-FG
                   (10.50.10.10)
```

---

# 🖥️ Hypervisor

- **Platform:** Proxmox VE
- **Virtualization:** KVM
- **Virtual Switching:** Linux Bridges
- **Storage:** SSD optimized with TRIM (Discard)

---

# 🌐 Network Layout

| Network | Address | Purpose |
|----------|----------|----------|
| WAN | DHCP | Internet Access |
| Attacker Zone | 172.16.1.0/24 | Offensive Security |
| DMZ | 10.0.0.0/24 | Security Infrastructure |
| Corporate | 10.50.10.0/24 | Internal Hosts |

---

# 💻 Virtual Machines

| Virtual Machine | Role | IP Address |
|-----------------|------|------------|
| OPNsense | Perimeter Firewall | WAN / 172.16.1.1 / 10.0.0.1 |
| FortiGate VM | Internal Firewall | 10.0.0.2 / 10.50.10.1 |
| Kali Linux | Attacker Machine | 172.16.1.x |
| Ubuntu SIEM | SIEM Server | 10.0.0.10 |
| Ubuntu-FG | Corporate Endpoint | 10.50.10.10 |

---

# 🔥 Firewall Architecture

## OPNsense (Perimeter Firewall)

Responsibilities

- WAN Gateway
- NAT
- Internet Access
- Static Routing
- Gateway for Attacker Network
- Gateway for DMZ

---

## FortiGate (Internal Firewall)

Responsibilities

- Internal Layer 3 Routing
- Corporate Gateway
- Internal Segmentation
- Future Security Policies
- Future IPS Policies

---

# 📡 Routing Flow

```
Corporate Host
      │
      ▼
FortiGate
      │
      ▼
OPNsense
      │
      ▼
Internet
```

---

# ✅ Completed Features

## Phase 1 – Infrastructure

- [x] Installed Proxmox VE
- [x] Configured Linux Bridges
- [x] Optimized VM storage using TRIM
- [x] Installed OPNsense
- [x] Configured WAN
- [x] Configured LAN
- [x] Configured OPT1
- [x] Verified Internet Connectivity

---

## Phase 2 – Internal Security

- [x] Installed FortiGate VM
- [x] Configured dual-homed interfaces
- [x] Created Corporate Network
- [x] Added Ubuntu-FG
- [x] Moved Ubuntu SIEM to DMZ
- [x] Updated Corporate addressing scheme
- [x] Configured Manual Outbound NAT
- [x] Verified Layer 2 connectivity
- [x] Verified Layer 3 routing
- [x] Verified Internet Connectivity
- [x] Verified DNS Resolution

---

# 🔍 Validation

The following tests have been successfully completed.

| Validation | Status |
|------------|--------|
| Layer 2 Connectivity | ✅ |
| Layer 3 Routing | ✅ |
| Gateway Reachability | ✅ |
| Internet Access | ✅ |
| ICMP Testing | ✅ |
| DNS Resolution | ✅ |
| Outbound NAT | ✅ |
| Cross-Network Routing | ✅ |

---

# ⚠ Challenges Encountered

## 1. FortiGate Firmware Compatibility

### Issue

The initial FortiOS deployment (v8.0.0) repeatedly logged out after authentication via the web interface.

### Resolution

- Researched supported FortiGate KVM images.
- Migrated to the VM64 KVM appliance.
- Downgraded to FortiOS 7.6.7.
- Successfully restored stable GUI access.

---

## 2. Outbound NAT

### Issue

The Corporate network was unable to access external resources despite successful routing.

### Resolution

Configured Manual Outbound NAT in OPNsense.

Source Network

```
10.50.10.0/24
```

Translation Interface

```
WAN
```

Translation Target

```
WAN Address
```

Result

All hosts successfully reach external networks and resolve DNS.

---

# 🚀 Roadmap

## Phase 3 – Security Monitoring

- [ ] Install Wazuh Manager
- [ ] Install Wazuh Agent
- [ ] Configure Log Collection
- [ ] Linux Log Monitoring

---

## Phase 4 – Intrusion Detection

- [ ] Install Suricata
- [ ] Configure IDS
- [ ] Configure IPS
- [ ] Custom Detection Rules
- [ ] Alert Validation

---

## Phase 5 – Enterprise Infrastructure

- [ ] Windows Server
- [ ] Active Directory
- [ ] DNS
- [ ] DHCP
- [ ] Group Policy
- [ ] Domain Joined Endpoints

---

## Phase 6 – Blue Team Operations

- [ ] Attack Simulation
- [ ] MITRE ATT&CK Mapping
- [ ] Threat Hunting
- [ ] Incident Response
- [ ] Security Monitoring

---

# 🧰 Technologies

- Proxmox VE
- OPNsense
- FortiGate VM
- Ubuntu Server
- Kali Linux
- Linux Bridges
- KVM Virtualization
- Static Routing
- Manual Outbound NAT

### Planned

- Wazuh
- Suricata
- Windows Server
- Active Directory
- Sysmon
- Zeek

---

# 📂 Repository Structure

```
Enterprise-Cybersecurity-Homelab/
│
├── README.md
│
├── diagrams/
│   ├── topology.png
│   ├── architecture.png
│   └── routing.png
│
├── screenshots/
│
├── docs/
│   ├── Build-Logs/
│   ├── Firewall-Configs/
│   ├── Routing/
│   └── Validation/
│
└── assets/
```

---

# 📖 Learning Outcomes

This homelab demonstrates practical experience with:

- Enterprise Network Design
- Firewall Engineering
- Network Segmentation
- Virtualization
- Routing
- NAT
- Linux Administration
- Security Architecture
- Infrastructure Troubleshooting
- Enterprise Documentation

---

# 👨‍💻 Author

**Jasser Vergara**

Cybersecurity • Firewall Engineering • Network Security • Blue Team • SOC • Homelab Enthusiast

---

## ⭐ Support

If you found this project helpful or interesting, consider giving the repository a **Star** to follow future updates as the lab evolves into a full enterprise Security Operations Center (SOC) environment.
