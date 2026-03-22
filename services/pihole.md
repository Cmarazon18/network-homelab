# Pi-hole (DNS Filtering)

## Purpose
Pi-hole provides network-wide DNS filtering for the lab subnet, blocking ads
and telemetry domains before they reach client devices. It runs as a Docker
container on the Pi-hole Server (pi02).

## Infrastructure
| Item | Detail |
|---|---|
| Host Device | Pi-hole Server (pi02) |
| OS | Ubuntu Server 24.04 LTS |
| IP Address | 192.168.200.6 (DHCP reservation in pfSense) |
| Deployment Method | Docker via Docker Compose |

## How It Works in the Lab
pfSense's DHCP server hands out `192.168.200.6` as the DNS server for all
devices on the lab subnet (`192.168.200.0/24`). All DNS queries from lab
devices flow through Pi-hole before being resolved upstream, allowing
Pi-hole to block requests to known ad and tracking domains.

## Installation

### 1. Install Docker
Docker was installed on pi02 before deploying Pi-hole. Standard Docker
installation for Ubuntu Server.

### 2. Resolve Port 53 Conflict
Ubuntu Server 24.04 runs `systemd-resolved` by default, which binds a stub
DNS listener to port 53. This conflicts with Pi-hole's DNS listener.

**Fix:** Edit `/etc/systemd/resolved.conf` and change the following line:
```
#DNSStubListener=yes  →  DNSStubListener=no
```
Uncomment the line and set the value to `no`, then restart the service:
```bash
sudo systemctl restart systemd-resolved
```

### 3. Deploy Pi-hole via Docker Compose
Pi-hole was deployed using the official Docker Compose configuration with
default settings. The only modification was setting a secure admin password
in the compose file for the web UI.

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
      WEBPASSWORD: "<your-password-here>"        # Replace with a strong password
    volumes:
      - "./etc-pihole:/etc/pihole"
      - "./etc-dnsmasq.d:/etc/dnsmasq.d"
    restart: unless-stopped
```
>**Note:** Volumes are bind-mounted to the local directory so blocklists and configuration persist across container restarts and updates.

```bash
docker compose up -d
```
---

### 4. Configure pfSense DHCP
In pfSense, the DHCP server for `192.168.200.0/24` was updated to hand out
`192.168.200.6` as the DNS server for all lab devices.

**Path:** Services → DHCP Server → LAN → DNS Servers → `192.168.200.6`

## Current Status
- Pi-hole container running and healthy
- pfSense DHCP handing out `192.168.200.6` as DNS
- Ad and telemetry blocking confirmed working across lab subnet

## Known Limitations / Future Work
- Docker Compose file uses mostly default values — worth revisiting resource
  limits and logging config as the lab matures
- No upstream DNS redundancy configured yet (single point of failure for
  lab DNS)
- Pi-hole Server has capacity for additional services (e.g. Homer/Heimdall
  dashboard) — to be explored