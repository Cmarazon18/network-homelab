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

## Configuration
### Port 53 Conflict resolution
*** Setps to resolve port 53 conflict**
