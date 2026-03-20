# Proxmox VE

## Overview

Proxmox VE is the hypervisor running on the lab's primary physical host — a repurposed HP All-in-One PC. It serves as the virtualization layer for the lab, enabling isolated virtual machines to run on a single piece of hardware.

The primary reason Proxmox exists in this stack is to run pfSense as a VM. The HP All-in-One only has one onboard NIC, but pfSense requires two separate network interfaces (WAN and LAN) to route traffic correctly. Proxmox solves this by managing both the onboard NIC and a Belkin USB-to-Ethernet adapter as separate Linux bridges, each passed through to pfSense as a distinct virtual interface.

---

## Host

| Property | Value |
|---|---|
| Hardware | HP All-in-One PC |
| Proxmox Version | <!-- fill in --> |
| Management IP | 192.168.145.200 |
| CPU | <!-- fill in --> |
| RAM | <!-- fill in --> |
| Storage | <!-- fill in --> |

---

## Network Interfaces

The host has two physical NICs configured as Linux bridges in Proxmox. Each bridge is attached to a pfSense virtual interface.

| Bridge | Physical NIC | Connected To | pfSense Interface | Purpose |
|---|---|---|---|---|
| vmbr0 | Belkin USB-to-Ethernet | Cisco SG350 (lab switch) | LAN | Internal lab network |
| vmbr1 | Onboard NIC | Home router (192.168.145.0/24) | WAN | Upstream internet access |

The USB adapter was added specifically to give pfSense a dedicated LAN-facing interface without requiring additional PCIe hardware. Proxmox treats it as a standard Linux bridge, so pfSense has no visibility into the underlying USB transport.

---

## Virtual Machines

| VM | OS | Purpose | vCPU | RAM | Disk | Status |
|---|---|---|---|---|---|---|
| pfSense | pfSense | Firewall, router, DHCP server | <!-- fill in --> | <!-- fill in --> | <!-- fill in --> | Running |

> **Note:** Resource allocation details to be updated after review in Proxmox UI.

---

## Planned Expansion

The current setup uses Proxmox primarily as a platform for pfSense. As the lab matures, the following VMs are planned:

- **SIEM (Wazuh or similar)** — Centralized log collection and security alerting across lab devices. Planned after VLAN segmentation is complete, since reliable network segmentation is a prerequisite for meaningful SIEM data.
- **Additional lab VMs** — Attack/defense lab environments, Windows Server for Active Directory practice, or other service VMs as the project progresses.

Proxmox was chosen over bare-metal pfSense installation specifically to preserve this flexibility — new VMs can be spun up without additional hardware.