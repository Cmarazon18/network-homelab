# Device Inventory

Complete inventory of all physical and virtual devices in the homelab environment.

---

## Physical Devices

| Device | Model | Role | OS | IP Address | Location |
|--------|-------|------|----|------------|----------|
| Proxmox Host | HP All-in-One | Primary Hypervisor | Proxmox VE | 192.168.145.200 | Home Lab |
| pi01 | Raspberry Pi 3 | Monitoring Server | Ubuntu Server 24.04 LTS | 192.168.200.5 | Home Lab |
| pi02 | Raspberry Pi 4 | DNS Filtering / IPAM | Ubuntu Server 24.04 LTS | 192.168.200.6 | Home Lab |
| Lab Switch | Cisco SG350 | Managed Switch | — | 192.168.200.7 | Home Lab |

---

## Virtual Machines

| VM | Host | Role | OS | IP Address |
|----|------|------|----|------------|
| pfSense | pve01 | Firewall / Router / DHCP | pfSense CE | 192.168.200.1 (LAN) |
| freePBX | pve01 | PBX VoIP Server. | Debian, FreePBX 17/Asterisk 23 | 192.168.20.5 (VLAN 20) |

---

## Device Details

### Proxmox Host
- **Model:** HP All-in-One (repurposed)
- **Role:** Primary hypervisor running all lab VMs
- **OS:** Proxmox VE
- **Management IP:** 192.168.145.200
- **WAN Connectivity:** Belkin USB-to-Ethernet adapter connected to home Wi-Fi bridge
- **Hosted VMs:** pfSense and freePBX

---

### pi01 — Monitoring Server
- **Model:** Raspberry Pi 3
- **Role:** Real-time system metrics and service uptime monitoring
- **OS:** Ubuntu Server 24.04 LTS
- **IP:** 192.168.200.5
- **Storage:** microSD
- **Services:** Netdata (system metrics), Uptime Kuma (service monitoring) — both running as Docker containers

---

### pi02 — DNS Filtering & IPAM
- **Model:** Raspberry Pi 4
- **Role:** Network-wide DNS filtering and IP address management
- **OS:** Ubuntu Server 24.04 LTS
- **IP:** 192.168.200.6 (static)
- **Storage:** 32GB microSD + USB flash drive (PostgreSQL database backups)
- **Services:** Pi-hole (DNS filtering), NetBox (IPAM/DCIM) — both running via Docker Compose
- **Notes:** Wi-Fi disabled via systemd. Backup cron job runs against USB flash drive.

---

### Cisco SG350 — Lab Switch
- **Model:** Cisco SG350
- **Role:** Managed Layer 2/3 switch connecting all lab devices
- **Management IP:** 192.168.200.7
- **Notes:** GE8 configured as trunk port to pfSense. Supports 802.1Q VLAN tagging.

---

## Network Summary

| Subnet | Purpose |
|--------|---------|
| 192.168.145.0/24 | Home Wi-Fi network (upstream) |
| 192.168.200.0/24 | Lab subnet — managed by pfSense |
| 192.168.10.0/24 | VLAN 10 — Intentionally Vulnerable Test Machines|
| 192.168.20.0/24 | VoIP VLAN

---
