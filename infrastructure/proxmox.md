# Proxmox VE

## Overview

Proxmox VE is the hypervisor running on the lab's primary physical host — a repurposed HP All-in-One PC. It serves as the virtualization layer for the lab, enabling isolated virtual machines to run on a single piece of hardware.


---

## Host

| Property | Value |
|---|---|
| Hardware | HP All-in-One PC |
| Proxmox Version | <!-- fill in --> |
| Management IP | 192.168.145.200 |


---



## Virtual Machines

| VM | OS | Purpose | vCPU | RAM | Disk | Status |
|---|---|---|---|---|---|---|
| freePBX | Debian, FreePBX 17/Asterisk 23 | PBX Phone server for lab | 1 vCPU | 2 GiB | 32 GB | Running |


---

## Planned Expansion

The current setup uses Proxmox primarily as a platform for freePBX. As the lab matures, the following VMs are planned:

- **SIEM (Wazuh or similar)** — Centralized log collection and security alerting across lab devices. Planned after VLAN segmentation is complete, since reliable network segmentation is a prerequisite for meaningful SIEM data.
- **Additional lab VMs** — Attack/defense lab environments, Windows Server for Active Directory practice, or other service VMs as the project progresses.

Proxmox was chosen over bare-metal freePBX specifically to preserve this flexibility — new VMs can be spun up without additional hardware.