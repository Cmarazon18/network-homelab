# Cameron's Homelab

A working infrastructure lab built and documented by a high school cybersecurity student — designed to develop real skills in networking, systems administration, and security operations. The lab runs on a dedicated subnet behind a pfSense firewall VM, double-NAT'd off my home network. All devices connect through a physical switch.


---

## Who this is for
This repository contains all documentation for my personal homelab. It's designed for two main audiences, *future me* and *anyone evaluating my work* who wants to see *how I think*, *what I build* and *how I document it*. 

## Real world roles I'm practicing
**Network Engineer**
I design and operate a segmented lab network run by a virtualized instance of pfSense on a proxmox hypervisor. This includes configuration for DHCP, DNS forwarding, and NAT and Firewall Rules as well as routing across the `192.168.200.0 /24 ` subnet - isolated from my home network and managed separately.

**Systems Administrator**
I manage and deploy services across different types of devices such as Proxmox Hypervisors and Raspberry Pi Servers. Current stack includes, DNS filtering, IP address management, real time system monitoring, and uptime tracking.

---

## Deployed Services
The lab runs a core stack of self-hosted services across two Raspberry Pi servers — including DNS filtering, real-time monitoring, uptime tracking, and IP address management. Full documentation for each service lives in the `services/` folder.

---
## What I'm learning
This lab is intentionally tied to concepts I learn in class. It is meant to take what I learn in class and deploy it on a real infrastructure. It also allows me to experiment with new concepts that extend my learning in the classroom.

- **Routing and NAT -** I am learning how firewalls handle traffic between the lab subnet and the internet, including double-NAT behavior and its tradeoffs.
- **DNS Architecture -** Recursive resolution, DNS stub listeners, upstream forwarding, and how filtering layers (Pi-hole) fit into the resolution chain
- **Network Segmentation -**  Why subnets matter and how they work, how DHCP scope and Gateway Configurations enforce boundaries, as well as what VLANS add.
- **Container orchestration** — Writing and managing Docker Compose stacks, understanding service dependencies, volume mounts, and port conflicts
- **Infrastructure documentation** — Modeling documentation after professional practices: architecture decisions, configuration records, and change rationale — not just "here's what I installed"
- **IPAM concepts** — Using NetBox to track IP allocations, subnet ownership, and device records the way an enterprise network team would
 
---

## Repository Structure

```
/
├── README.md               ← You are here
├── docs/                   ← Main documentation
│   ├── architecture.md
│   ├── design-decisions.md
│   └── roadmap.md
├── infrastructure/         ← Physical and virtual hardware documentation
│   ├── device-inventory.md
│   ├── overview.md
│   └── proxmox.md
├── network/                ← Network architecture and configuration docs
│   ├── dns-filtering.md
│   ├── ip-address-plan.md
│   └── overview.md
└── services/               ← Per-service documentation for each deployed component
    ├── monitoring.md
    ├── netbox.md
    └── pihole.md
```
Each file in `network/` contains the documentation for that specific aspect of the network.
 
Each file in `services/` is self-contained documentation for a single deployed service.

## Current Status
 
| Area | Status | Notes |
|---|---|---|
| pfSense firewall / routing | ✅ Operational | NAT, DHCP, DNS forwarding all active |
| Pi-hole DNS filtering | ✅ Operational | Confirmed blocking ads lab-wide |
| Netdata monitoring | ✅ Operational | Running on pi01 |
| Uptime Kuma | ✅ Operational | Running on pi01; monitor list being documented |
| NetBox IPAM | ✅ Operational| Running on pi02; backing up to mounted USB drive |
| Firewall rules doc | 🔄 In Progress | Drafting |
| VLANs | 🔄 In Progress | Implementing on Cisco SG350 with pfSense inter-VLAN routing |

---


