# TailScale (VPN Service)

## Purpose
Tailscale provide a VPN (Virtual Private Network)  service for the lab. The VPN provides a secure tunnel for remote access for the lab, without directly exppsing internal services to the public internet.

## Infrastructure

| Item            | Detail                           |
| ---             | ---                              |
| Host device     | PfSense Box( Protectli Mini-PC)  |
| OS | PfSense FreeBSD | 
| IP Address | 192.168.200.1 (pfSense IP)|
| Deployment Method | pfSense Package Manager |

## Installation
The VPN service was installed on the pfSense firewall.

General installation process:

1. Install the VPN package/service
2. Authenticate the VPN node to the VPN control plane
3. Join the home lab firewall to the private VPN network
4. Verify external connectivity and peer visibility

After installation, the firewall became the primary VPN gateway for the lab environment.

## 3. Subnet Advertisement

Internal lab networks were configured for routed access through the VPN.

Example advertised networks:

```text
192.168.10.0/24   - Client VLAN
192.168.20.0/24   - PBX VLAN
192.168.30.0/24   - IoT VLAN
192.168.200.0/24  - Infrastructure/Lab network
```

## Current Status
- Devices can remotely connect to the lab services securely
- VPN devices are treated as trusted clients and follow similar firewall rules to VLAN 10.
- The VPN works as intended and the pfSense box acts as an optional exit node.


