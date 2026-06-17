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

## Lab Component Inventory

| VM Name | Role | OS | vCPU | RAM | Assigned Bridges |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **OPNsense** | Perimeter Firewall | FreeBSD (UFS) | 2 Cores | 2 GB | `vmbr0` (WAN), `vmbr1` (LAN/Attacker), `vmbr2` (OPT1/DMZ) |
| **FortiGate VM** | Internal Firewall | FortiOS | 1 Core | 2 GB | `vmbr2` (DMZ), `vmbr3` (CORP) |
| **Ubuntu Server** | SIEM / NIDS | Ubuntu Server LTS | 4 Cores | 12 GB | `vmbr3` (CORP) |
| **Kali Linux** | Attacker | Kali Linux | 2 Cores | 4 GB | `vmbr1` (Attacker) |

---

## Project Milestones & Artifacts

### Day 1: Host Infrastructure & Perimeter Setup
*The following artifacts validate the foundational configuration of the hypervisor and the perimeter gateway.*

**1. Hypervisor Storage Optimization**
* **Artifact:** `https://kommodo.ai/i/ZZhxYJR1mHpyOs23dn7u`
* **Context:** To ensure the longevity and performance of the host's 256GB NVMe SSD, the OPNsense virtual disk was configured with the `Discard` flag. This allows the guest OS to issue TRIM commands through to the hypervisor, preventing storage exhaustion and maintaining optimal write speeds on flash media.

**2. Multi-Homed Perimeter Gateway Configuration**
* **Artifact:** `https://kommodo.ai/i/iGI1vOXQvbLCaAZVBBMY`
* **Context:** OPNsense was deployed as the edge firewall with three distinct virtual network interfaces. Consumer-grade defaults were discarded in favor of enterprise routing principles:
  * **WAN (`vtnet0`):** Upstream DHCP
  * **LAN (`vtnet1`):** `172.16.1.1/24` (Attacker Gateway)
  * **OPT1 (`vtnet2`):** `10.0.0.1/24` (DMZ Gateway)

**3. Virtual Switching Fabric Map**
* **Artifact:** `https://kommodo.ai/i/VUlotei5US334yKTBsig`
* **Context:** This map validates the logical isolation of the environment. Traffic between the Attacker Zone, the DMZ, and the physical internet is strictly enforced via the Proxmox Linux Bridges (`vmbr0`, `vmbr1`, `vmbr2`, `vmbr3`), ensuring no internal cross-talk bypasses the firewall inspection engines.

## 🚀 Update: Internal Firewall & Segmentation Completed 

**Milestone Reached:** Successfully deployed the internal security boundary and finalized L3 routing across all isolated virtual network segments (Attacker, DMZ, and Corporate Core).

**Key Additions:**
* **FortiGate KVM Deployment (VM 102):** Ingested and configured a FortiOS 8.0 virtual appliance via Proxmox CLI. Successfully restricted compute resources (1 vCPU, 2048MB RAM) to comply with the strict Fortinet evaluation license.
* **Dual-Homed Routing:** Bridged the `vmbr2` (DMZ) and `vmbr3` (Corporate) networks, allowing the FortiGate to act as the central L3 gateway for internal traffic.
* **Network Verification:** Verified 100% ICMP transit from the deep corporate zone (Ubuntu SIEM) -> through the FortiGate -> through the OPNsense Perimeter -> out to the external internet (`8.8.8.8`).

### Internal Firewall & Corporate Zone Segmentation
*The following artifacts validate the deployment of the internal security boundary and the finalization of L3 routing across all isolated virtual network segments.*

**1. Proxmox Hypervisor Inventory**
* **Artifact:** : https://kommodo.ai/i/Jx9oinKOw9MJlIOmZyxd
* **Context:** All four endpoints are successfully provisioned and bound to their respective virtual bridges (`vmbr1`, `vmbr2`, `vmbr3`), ensuring complete collision domain isolation.

**2. Internal Firewall Interface Mapping (FortiGate)**
* **Artifact:** : https://kommodo.ai/i/SicRvzn9wJYDs655q3Wq
* **Context:** A FortiOS 8.0 KVM appliance was deployed and restricted to 1 vCPU and 2048MB RAM to comply with licensing. Dual-homed routing was established with `port1` handling the DMZ (`10.0.0.2`) and `port2` acting as the Corporate Core gateway (`10.50.0.1`).

**3. L2/L3 Attacker Zone Verification (Kali Linux)**
* **Artifact:** : https://kommodo.ai/i/bHlK36QchzfNsp1Wo26s
* **Context:** The Kali Linux attacker endpoint successfully resolves the OPNsense gateway MAC (`ip neighbor`) and achieves 0% ICMP packet loss to the perimeter firewall (`172.16.1.1`).

**4. Internal Path Verification (Ubuntu SIEM)**
* **Artifact:** : https://kommodo.ai/i/NcG7yHqfxoK66h0CwoPU
* **Context:** Structural transport is verified. ICMP transit successfully routes from the deep Corporate Zone (`10.50.0.10`) to the FortiGate interior gateway interface (`10.50.0.1`).

---

## Next Steps
* Configure outbound IPv4 security policies via the FortiGate Web GUI.
* Initialize the Ubuntu SIEM (`101`) OS updates and data ingestion pipelines.

**Next Steps:**
- Configure outbound IPv4 security policies via the FortiGate Web GUI.
- Initialize the Ubuntu SIEM (`101`) data ingestion pipelines.
