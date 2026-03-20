# IP Address Plan
This document outlines the IP addressing scheme and VLAN segmentation for the home lab network. The lab is designed to be a self-contained environment with multiple network segments for different purposes, all managed through pfSense as the central router and firewall.

---
## Subnets
|VLAN| Subnet | Purpose | Gateway|
|---|---|---|---|
|| VLAN 1 | `192.168.200.0/24` | Main lab network | `192.168.200.1` (pfSense) |
| VLAN 10 | `192.168.10.0/24` | Security lab (intentionally vulnerable devices) | `192.168.10.1` (pfSense) |

---
## VLAN 1 — Address Allocation (`192.168.200.0/24`)
 
| Range | Role | Assignment Method |
|-------|------|-------------------|
| `.1` | Gateway (pfSense) | Static (DHCP Reservation) |
| `.2–.20` | Infrastructure (networking, DNS, monitoring) | Static (DHCP Reservation) |
| `.21–.99` | Unassigned / Future use | — |
| `.100–.250` | DHCP pool | Dynamic (pfSense DHCP server) |
| `.251–.254` | Unassigned/Future use | — |

## VLAN 10 — Address Allocation (`192.168.10.0/24`)
 
Reserved for intentionally vulnerable machines used in cybersecurity testing and learning. This VLAN will be isolated from the main lab network through pfSense firewall rules to prevent lateral movement.
 
Addressing details will be documented once the VLAN is fullyimplemented.
 
---

## Additional Notes
 - The proxmox host sits on the home network at `192.168.145.200` and bridges traffic into the lab through a virtulized instance of pfSense.
 - Pi01 and pi02 are classified as infrastructure despite running server-class services (monitoring, DNS, IPAM) because those services directly support lab operations.
- The `.21–.99` range is intentionally unassigned to allow future expansion for servers or VMs without overlapping the DHCP pool.
- The `.251–.254` range is reserved for potential future use, such as additional infrastructure devices or special-purpose servers that require static IPs outside the DHCP pool.