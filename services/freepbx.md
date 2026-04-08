# FreePBX - VoIP PBX
## Overview
FreePBX is the labs private branch exchange (PBX) It runs as a virtual machine on pve01(proxmox), isolated on VLAN 20 — a dedicated VoIP segment separated from the main lab network. Two IP desk phones are registered and operational. 

The service was deployed to demonstrate VLAN segmentation with a genuine use case. VoIP is sensitive to latency and jitter, and in production environments it's standard practice to isolate voice traffic from data traffic for both performance and security reasons.

### Hypervisor specs

| Property | Value |
|----------| -- |
| Hypervisor | Proxmox VE |
| OS | Debian (FreePBX 17 Distro ISO) |
| Asterisk Version | 23 |
| Install Type | FOG (no DAHDI hardware) |
| CPU | 1 vCPU |
|RAM | 2 GB |
| Disk | 16 GB (scsi0) |
| NIC | VirtIO on vmbr0, VLAN tag 20 |
| IP | 192.168.20.5 (Static on device) | 
| Gateway | 192.168.20.1 (pfSense VLAN 20 Interface) |

### Network Placement 
FreePBX sits on VLAN 20 (192.168.20.0/24) The VLAN is configured across three layers:

- **Proxmox** - Bridge vmbr0 is VLAN aware. The FreePBX NIC has VLAN 20 tag 20 applied at the hypervisor level, so all traffic leaving the VM is tagged before it hits the bridge.
- **SG350** - VLAN 20 is created on the switch and tagged on the trunk port (Gi8) to proxmox.
- **pfSense** - VLAN 20 sub-interface on vtnet1, assigned as the VOIP VLAN interface with IP `192.168.20.1` DHCP is enabled on this interface to serve the IP Phones.
---

### SIP Configuration

Extensions use ***chan_sip*** (UDP port 5160) to work more reliably with older Cisco and Yealink phones.

### Registered Devices

| Device | Extension | IP | Registration |
| ---- | ---| --- | --- |
| Cisco SPA 502G | 1113 |  DHCP on VLAN 20 | chan_sip / port 5160 |
| YeaLink SIP-T21P-E2 | 1112 | DHCP on VLAN 20| chan_sip / port 5160 |
| MicroSIP Softphone (PC) | 1114 | DHCP on VLAN 1 | chan_sip / port 5160 |

Both physcial phones connect through an unmanged switch that is connected to port into access port 4 on the ***SG350*** managed switch.

## Integration
- **pfSense** — Provides gateway, DHCP, and firewall services for VLAN 20. DNS for FreePBX routes through pfSense at 192.168.20.1.

- **MicroSIP (VLAN 1)** - A softphone on the main lab network registers to freePBX through inter-VLAN routing. This works because the softphone initiates registration from VLAN 1 (allowed outbound), and pfSense's state table handles return traffic. FreePBX does not need an explicit outbound rule to VLAN 1 for this to function.

## Status
 
| Component | State |
|-----------|-------|
| FreePBX VM | Operational |
| Asterisk service | Running |
| Phone registration | Registered (chan_sip) |
| Inter-extension calling | Working |
| Cross-VLAN calling (softphone on VLAN 1) | Working |
| VLAN 20 firewall rules | **Allow-all — not yet tightened** |

## References
 
- [`network/ip-address-plan.md`](../../network/ip-address-plan.md) — VLAN 20 subnet and IP assignments
- [`docs/design-decisions.md`](../design-decisions.md) — TCP-only firewall rule mistake, SIP port mismatch troubleshooting
- [`infrastructure/proxmox.md`](../../infrastructure/proxmox.md) — VM host details
- FreePBX admin: http://192.168.20.5