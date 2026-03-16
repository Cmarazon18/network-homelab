# DNS Filtering
## Overview
DNS filtering is implemented in the network via pi-hole to create a network wide sinkhole that blocks ads, trackers and malicious domains at the DNS level before they reach any device. 
Rather than installing ad blockers and web filters on each node individually, all clients route their DNS quieries through Pi-Hole allowing for centralized, network wide control and visibility over all DNS requests on the `192.168.200.0 /24` subnet

## Architecture
| Component | Details |
|-----------------|----------------------------------------------|
|**Host** | Pi-Hole Server (Raspberry Pi - pi02) |
|**IP Address** | `192.168.200.6` (static) |
|**Operating System** | Docker Container inside ubuntu server |
|**DNS Port** | TCP/UDP 53 |
|**Admin UI** | HTTP via port 80 (web dashboard)|
|**Subnet** | `192.168.200.0/24` (lab network) |

Pi-hole runs as a docker container on *pi02*. All lab devices use pi-holes ip address as as their DNS server through pfSense DHCP server configuration, meaning DNS filtering is applied to all devices on the lab subnet without any device-specific configuration.

```
Lab Device
    │
    │  DNS query (port 53)
    ▼
Pi-hole (192.168.200.6)
    │
    ├── Blocked? → Return NXDOMAIN (sinkhole)
    │
    └── Allowed? → Forward to upstream DNS (e.g., 1.1.1.1, 8.8.8.8)
```
---


## Configuration
### Port 53 Conflict resolution
By Default, ubuntu server uses `systemd-resolved`, which binds port 53 on localhost and conflicts with Pi-hole's DNS listener. Therefore, this mus be resolved before deploying the Pi-Hole container.
**Steps to free port 53**
```bash
# Disable the systemd-resolved stub listener
sudo sed -i 's/#DNSStubListener=yes/DNSStubListener=no/' /etc/systemd/resolved.conf
 
# Restart systemd-resolved to apply the change
sudo systemctl restart systemd-resolved
 
# Verify port 53 is no longer in use
sudo ss -tulnp | grep ':53'
```
---
### Docker Compose
Pi-Hole is deployed using Docker Compose, The container is configured with a fixed IP on a custom docker netowrk to ensure the address stays consistent.

```yaml
version: "3"
 
services:
  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "80:80/tcp"
    environment:
      TZ: "America/New_York"
      WEBPASSWORD: "changeme"        # Replace with a strong password
    volumes:
      - "./etc-pihole:/etc/pihole"
      - "./etc-dnsmasq.d:/etc/dnsmasq.d"
    restart: unless-stopped
```
>**Note:** Volumes are bind-mounted to the local directory so blocklists and configuration persist across container restarts and updates.
---
### Blocklists
Pi-Hole ships with a default blocklist (SevenBlack Unified Hosts) enabled out of the box. Additional lists can be added through the admin UI however under **Group Management -> Adlists**
## Intregation
**pfSense DHCP -> Pi-Hole DNS Distribution**
The correct point to integrate the Pi-Hole DNS server is via the pfSense DHCP server. Therefore, all devices automatically obtian Pi-Hole as their DNS server. 
