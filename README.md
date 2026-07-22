
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
| Endpoint | Ubuntu Server, Windows 10 |
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
- [x] Enabled Eve JSON logging
- [x] Configured Suricata Syslog output
- [x] Installed Wazuh Agent on OPNsense
- [x] Enrolled OPNsense into Wazuh
- [x] Integrated firewall and Suricata logs with Wazuh
- [x] Monitoring network traffic

## Phase 5 – Endpoint Detection & Response

- [x] Deployed Windows 10 endpoint
- [x] Installed Wazuh Agent
- [x] Installed Sysmon
- [x] Applied SwiftOnSecurity Sysmon configuration
- [x] Verified Sysmon event generation
- [x] Integrated Sysmon logs into Wazuh
- [x] Installed Invoke-AtomicRedTeam framework
- [x] Ready for MITRE ATT&CK attack simulations

# ⚠ Challenges Encountered

## FortiGate Firmware and Evaluation License

### Issue

The initial FortiOS 8.0 VM deployment presented two issues:

- The web management interface repeatedly logged out immediately after authentication, making administration unreliable.
- The evaluation license had expired, preventing the firewall from forwarding traffic. As a result, hosts behind the FortiGate were unable to access external networks, and inbound connections through the firewall also failed despite correct routing and firewall policy configurations.

### Root Cause

The deployed FortiGate image was running an unsupported or expired evaluation version, causing both management instability and functional limitations on traffic forwarding.

### Resolution

- Researched the appropriate FortiGate virtual appliance for Proxmox KVM.
- Downloaded the **FortiGate VM64 KVM** image.
- Deployed **FortiOS 7.6.7**, which provided a stable management interface and restored expected firewall behavior under the evaluation license.

### Result

- ✅ Stable access to the FortiGate Web GUI.
- ✅ Firewall routing and policy configuration functioned as expected.
- ✅ Corporate hosts successfully accessed external networks.
- ✅ Inbound and outbound traffic through the FortiGate was restored.
---

## Outbound NAT

Corporate hosts could not access the Internet.

**Resolution**

Configured Manual Outbound NAT in OPNsense.

- Source: 10.50.10.0/24
- Interface: WAN
- Translation: WAN Address

---

## Static Routing Between the DMZ and Corporate Network

### Issue

The Ubuntu-FG endpoint could successfully reach the Ubuntu SIEM using ICMP (ping), but TCP-based services such as **SSH (port 22)** and **Wazuh Agent registration (port 1515)** consistently failed.

### Root Cause

The Ubuntu SIEM did not have a routing entry for the Corporate subnet (`10.50.10.0/24`). While ICMP connectivity appeared functional, the Ubuntu SIEM was unable to return TCP response packets to the Corporate network because it did not know that the FortiGate was the next-hop router.

As a result, the TCP three-way handshake could not complete, causing connection attempts to time out.

### Resolution

Added a static route on the Ubuntu SIEM:

- **Destination Network:** `10.50.10.0/24`
- **Gateway:** `10.0.0.2` (FortiGate DMZ Interface)

### Result

- ✅ SSH connectivity between the DMZ and Corporate network was restored.
- ✅ Wazuh Agent successfully registered with the Wazuh Manager over TCP port **1515**.
- ✅ Bidirectional communication between both network segments was fully established.

Communication between the DMZ and Corporate network was successfully restored.

## Suricata Log Integration with Wazuh

### Issue

The initial approach was to forward Suricata IDS alerts from OPNsense to Wazuh using Syslog. Although Suricata was generating events, they were not being reliably ingested by Wazuh.

### Resolution

Instead of relying solely on Syslog forwarding:

- Installed the **Wazuh Agent** on OPNsense.
- Enrolled OPNsense as a monitored endpoint.
- Configured the agent to collect Suricata and firewall logs directly from the firewall.
- Verified successful log ingestion in Wazuh.

### Result

- ✅ Suricata alerts successfully integrated with Wazuh.
- ✅ Centralized monitoring of firewall and IDS events.
- ✅ More reliable log collection for security monitoring.

---

## Excessive Sysmon Event Noise

### Issue

A default Sysmon installation generated a large volume of Windows events, making it difficult to identify meaningful security activity within Wazuh.

### Resolution

Applied the **SwiftOnSecurity Sysmon configuration**, which filters common benign events while preserving high-value security telemetry.

### Result

- ✅ Reduced unnecessary log volume.
- ✅ Improved signal-to-noise ratio.
- ✅ Clearer and more actionable endpoint telemetry in Wazuh.

# 🚀 Roadmap

## Phase 6

- [ ] Deploy Windows Server
- [ ] Configure Active Directory Domain Services
- [ ] Join Windows endpoint to the domain
- [ ] Execute Atomic Red Team attack simulations
- [ ] Validate Wazuh detections
- [ ] Tune Wazuh detection rules
- [ ] Tune Suricata signatures
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
- Endpoint Detection & Response (EDR)
- Sysmon endpoint telemetry
- Windows event monitoring
- Wazuh agent deployment
- SIEM log ingestion and correlation
- MITRE ATT&CK attack simulation

# 👨‍💻 Author

**Jasser Vergara**

Cybersecurity • Firewall Engineering • Network Security • SOC • Blue Team
