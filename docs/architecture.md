## Lab Archetucture

# Overview
This homelab is a personal network infrastructure project built to develop hands-on skills in networking, virtualization, and cybersecurity. The lab runs on repurposed and single-board hardware, connected through a managed switch, and segmented into isolated VLANs routed by a virtualized pfSense firewall. Core services — DNS filtering, IP address management, and monitoring — are self-hosted across dedicated devices.
The lab does not have a direct internet connection. WAN access is provided through a Wi-Fi bridge and USB-to-Ethernet adapter connected to the home network, with pfSense performing NAT for all lab traffic.

---

## Physical Architecture
### Device Inventory
| Device | Role | Hardware | OS / Platform |
|---|---|---|---|
| Proxmox Host | Hypervisor | HP All-in-One (repurposed) | Proxmox VE |
| pfSense VM | Firewall / Router | VM on Proxmox Host | pfSense |
| freePBX VM | PBX Phone Server | VM on Proxmox Host | FreePBX Debain |
| pi01 | Monitoring Server | Raspberry Pi | Ubuntu Server 24.04 LTS |
| pi02 | DNS / IPAM Server | Raspberry Pi 4 | Ubuntu Server 24.04 LTS |
| Lab Switch | Network Switching | Cisco SG350 | Cisco IOS |
| WAN Bridge | Internet Uplink | Wi-Fi bridge | — |

## Physical Connectivity
All devices connect through the **Cisco SG350** managed switch, which serves as the central point of connectivity for the lab. The switch is configured with multiple VLANs to segment traffic between different parts of the lab network. The **pfSense VM** is connected to the switch and serves as the gateway for all lab traffic, routing between VLANs and providing NAT for outbound internet access through the WAN bridge. The Raspberry Pi servers are also connected to the switch and assigned to appropriate VLANs based on their roles (e.g., Main Lab, Intentionally Vulnerable Test Machines.)

---

## Logical Architecture

### Network Segments
The lab uses 3 main Ip address ranges.
|Network | Subnet | Purpose |
|---|---|---|
|Home Network |`192.168.145.0/24` | Main home network, separate from lab |
|Lab Network | `192.168.200.0/24` | Main lab subnet, managed by pfSense |
|Test Machines(VLAN 10) | `192.168.10.0/24` | Isolated subnet for intentionally vulnerable machines |
|PBX Phone Server(VLAN 20)| `192.168.20.0/24` | Isolated subnet for VoIP traffic |

---

## Routing and Firewall
The pfSense VM serves as the central router and firewall for all VLANS. Inter-VLAN routing is handled by pfSense, with firewall rules controlling traffic flow between segments. Firewall rules are configued to controll traffic between the lab and home networks. Full firewall ruleset documentation lives in `network/overview.md` and `network/dns-filtering.md` as it relates to DNS traffic.

## DNS Architecture
Pi-Hole on pi02 is the primary DNS resolver for all the lab devices. pfSense distributes the Pi-Hole IP as the primary DNS server via DHCP, and forwards any requests it receives to Pi-Hole. Pi-Hole then performs recursive resolution for all queries, with filtering rules applied to block ads and malicious domains. This architecture allows for centralized DNS management and monitoring through Pi-Hole's dashboard, while pfSense handles DHCP and routing for the lab network.
## Implementation Status
 
The core architecture is designed and partially implemented. VLAN segmentation is currently in progress.
 
| Component | Status |
|---|---|
| Proxmox hypervisor | ✅ Operational |
| pfSense VM | ✅ Operational |
| Pi-hole (pi02) | ✅ Operational |
| NetBox (pi02) | ✅ Operational |
| Netdata + Uptime Kuma (pi01) | ✅ Operational |
| Cisco SG350 managed switch | ✅ Online |
| VLAN configuration (SG350 + pfSense) | ✅ Operational |
| Network diagram | ⏳ Planned |
 
---
 
## Related Documentation
 
- [`infrastructure/overview.md`](../infrastructure/overview.md) — Hardware details for each device
- [`network/overview.md`](../network/overview.md) — Network layer configuration
- [`network/ip-address-plan.md`](../network/ip-address-plan.md) — IP addressing and VLAN subnets
- [`network/dns-filtering.md`](../network/dns-filtering.md) — Pi-hole DNS setup
- [`docs/design-decisions.md`](design-decisions.md) — Reasoning behind key architectural choices
 
 