
# Enterprise Cybersecurity Homelab

> A production-inspired cybersecurity homelab built on **Proxmox VE** featuring layered firewalls, network segmentation, virtualization, security monitoring, and intrusion detection.

---

## 📌 Project Overview

This homelab simulates an enterprise network using **OPNsense** as the perimeter firewall and **FortiGate** as the internal firewall. The environment is designed to practice firewall engineering, network security, blue team operations, and SOC concepts.

Current capabilities include:

- Multi-tier segmented network
- Dual-firewall architecture
- DMZ and Corporate network separation
- Centralized security monitoring with Wazuh
- Intrusion detection with Suricata
- Enterprise routing and NAT

---

# 🏗️ Architecture

<img width="492" height="492" alt="Topology drawio" src="https://github.com/user-attachments/assets/15baa224-e614-4ded-b601-1545cf594e1c" />

---

# 🖥️ Infrastructure

| Component | Technology |
|-----------|------------|
| Hypervisor | Proxmox VE |
| Perimeter Firewall | OPNsense |
| Internal Firewall | FortiGate VM |
| SIEM | Wazuh |
| IDS | Suricata |
| Endpoint | Ubuntu Server |
| Attacker | Kali Linux |

# 🌐 Networks

| Network | Purpose |
|----------|----------|
| WAN | Internet |
| 172.16.1.0/24 | Attacker |
| 10.0.0.0/24 | DMZ |
| 10.50.10.0/24 | Corporate |

# ✅ Completed

## Phase 1 – Infrastructure

- [x] Installed Proxmox VE
- [x] Configured Linux Bridges
- [x] Installed OPNsense
- [x] Configured WAN, LAN and DMZ
- [x] Configured NAT
- [x] Internet connectivity verified

## Phase 2 – Internal Network

- [x] Deployed FortiGate VM
- [x] Configured dual-homed interfaces
- [x] Created Corporate subnet (10.50.10.0/24)
- [x] Added Ubuntu-FG endpoint
- [x] Moved Ubuntu SIEM to the DMZ
- [x] Configured outbound NAT
- [x] Verified end-to-end routing
- [x] All hosts can access the Internet

## Phase 3 – Security Monitoring

- [x] Installed Wazuh Manager on Ubuntu SIEM
- [x] Installed Wazuh Agent on Ubuntu-FG
- [x] Registered agent successfully
- [x] Configured centralized log collection
- [x] Verified Linux endpoint monitoring

## Phase 4 – Intrusion Detection

- [x] Installed Suricata on OPNsense
- [x] Enabled IDS service
- [x] Monitoring network traffic

# ⚠ Challenges Encountered

## FortiGate Firmware

FortiOS 8.0 repeatedly logged out after authentication.

**Resolution**

- Switched to the VM64 KVM image.
- Downgraded to FortiOS 7.6.7.

---

## Outbound NAT

Corporate hosts could not access the Internet.

**Resolution**

Configured Manual Outbound NAT in OPNsense.

- Source: 10.50.10.0/24
- Interface: WAN
- Translation: WAN Address

---

## Static Routing

Ubuntu SIEM could not communicate with Ubuntu-FG.

**Root Cause**

The Ubuntu SIEM did not have a route to the Corporate subnet.

**Resolution**

Added a static route:

- Destination: 10.50.10.0/24
- Gateway: 10.0.0.2 (FortiGate DMZ Interface)

Communication between the DMZ and Corporate network was successfully restored.

# 🚀 Roadmap

## Phase 5

- [ ] Windows Server
- [ ] Active Directory
- [ ] Sysmon
- [ ] Windows Endpoint

## Phase 6

- [ ] Generate attack traffic
- [ ] Validate Wazuh detections
- [ ] Tune Suricata rules
- [ ] MITRE ATT&CK mapping
- [ ] Threat hunting

# 📚 Learning Outcomes

This project demonstrates hands-on experience with:

- Enterprise network segmentation
- Firewall engineering
- Routing and NAT
- Proxmox virtualization
- Linux administration
- Wazuh SIEM
- Suricata IDS
- Troubleshooting enterprise networking

# 👨‍💻 Author

**Jasser Vergara**

Cybersecurity • Firewall Engineering • Network Security • SOC • Blue Team
