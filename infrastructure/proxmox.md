<<<<<<< HEAD
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
| pfSense | pfSense | Firewall, router, DHCP server | 2 vCPU | 2.98 GiB | 20 GB | Running |
| freePBX | Debian, FreePBX 17/Asterisk 23 | PBX Phone server for lab | 1 vCPU | 2 GiB | 32 GB | Running |


---

## Planned Expansion

The current setup uses Proxmox primarily as a platform for pfSense. As the lab matures, the following VMs are planned:

- **SIEM (Wazuh or similar)** — Centralized log collection and security alerting across lab devices. Planned after VLAN segmentation is complete, since reliable network segmentation is a prerequisite for meaningful SIEM data.
- **Additional lab VMs** — Attack/defense lab environments, Windows Server for Active Directory practice, or other service VMs as the project progresses.

=======
# Proxmox VE

## Overview

Proxmox VE is the hypervisor running on the lab's primary physical host — a repurposed HP All-in-One PC. It serves as the virtualization layer for the lab, enabling isolated virtual machines to run on a single piece of hardware.

The primary reason Proxmox exists in this stack is to run pfSense as a VM. The HP All-in-One only has one onboard NIC, but pfSense requires two separate network interfaces (WAN and LAN) to route traffic correctly. Proxmox solves this by managing both the onboard NIC and a Belkin USB-to-Ethernet adapter as separate Linux bridges, each passed through to pfSense as a distinct virtual interface.

---

## Host

| Property | Value |
|---|---|
| Hardware | HP All-in-One PC |
| Proxmox Version | 9.1.1 (Kernel 6.17.2-1-pve) |
| Management IP | 192.168.145.200 |
| CPU | AMD A9-9425 (2 cores) |
| RAM | 7.2 GB |
| Storage | 931.5 GB HDD (LVM-thin) |

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
| pfSense | pfSense CE | Firewall, router, DHCP server | 2 vCPU | 3 GB | 20 GB | Running |

---

## Planned Expansion

The current setup uses Proxmox primarily as a platform for pfSense. As the lab matures, the following VMs are planned:

- **SIEM (Wazuh or similar)** — Centralized log collection and security alerting across lab devices. Planned after VLAN segmentation is complete, since reliable network segmentation is a prerequisite for meaningful SIEM data.
- **Additional lab VMs** — Attack/defense lab environments, Windows Server for Active Directory practice, or other service VMs as the project progresses.

>>>>>>> 97f57b131645603734073f817f786c4ad1507110
Proxmox was chosen over bare-metal pfSense installation specifically to preserve this flexibility — new VMs can be spun up without additional hardware.