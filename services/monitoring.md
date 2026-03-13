# Monitoring (Netdata and Uptime Kuma)

## Purpose
**Netdata:** Provides real time system metrics for devices in the home lab. The live metrics are available online via the netdata cloud website.
**Uptime Kuma:** Provides real time monitoring of service availability via ping tests. The pi-hole server is monitored by a DNS test inside netdata. All monitors run on intervals of 20 seconds.
Both run as Docker containers on the Monitoring Server (Raspberry Pi). The goal is to maintain visibility into host health and service uptime across the lab subnet.

## Infrastructure
| Item | Detail |
|---|---|
| Host Device | Monitoring Server (Raspberry Pi 3) |
| OS | Ubuntu Server 24.04 LTS |
| Deployment method | Docker via Docker Compose |
| Netdata local access | `http://192.168.200.5:19999`|
| Netdata Cloud access | `https://app.netdata.cloud/` |
| Uptime Kuma Access | `http://192.168.200.5:3001` |

## How It Works in the Lab
Netdata runs an agent on the Monitoring Server and collects per-second system metrics including CPU, memory, disk I/O, and network throughput.
It exposes a real-time dashboard and fires alerts when configurable thresholds are exceeded.
Uptime Kuma performs HTTP checks against lab services on a set interval and reports their availability on a status dashboard. 
It sends notifications when a monitored service goes down or recovers. 
Both tools are isolated on a dedicated Monitoring Server rather than
running on the infrastructure they watch — consistent with how observability
stacks are deployed in production environments.

## Installation
### 1. Install Docker
Docker was installed on the Monitoring Server before deploying either tool. Docker was installed using the `curl -fsSL https://get.docker.com | sudo bash`  after updating ubuntu server.

### 2. Deploy Netdata via Docker Compose
Netdata was deployed using its official Docker Compose configuration. 
The container is given read access to host system paths so it can collect hardware-level metrics from the underlying Raspberry Pi.
 
```bash
docker compose up -d
```

### 3. Deploy Uptime Kuma via Docker Compose
Uptime Kuma was deployed alongside Netdata using a separate Docker Compose
service definition. Persistent data is stored in a named Docker volume so
monitor configurations and history survive container restarts.
 
```bash
docker compose up -d
```

### 4. Configure Uptime Kuma Monitor
After deployment, monitors were added through the Uptime Kuma web UI for each lab service.
| Service | Check type | Target |
|---|---|---|
| pfSense server | Ping | `http://192.168.200.1` |
| Pi-hole admin | HTTP | `http://192.168.200.6/admin`|
|Home Router | Ping | `192.168.145.1`| 

## Current Status
- Netdata agent is connected to cloud and reporting live metrics.
- Uptime Kuma running with monitors configured for core lab services.

## Known Limitations / Future Work
- Firewall metrics are not currently visible in netdata
- No monitoring of health of lab switch
- NetFlow / traffic analysis not yet configured - pfSense supports NetFlow export, planned for the future
- Monitoring server is a single point of failure - if the pi goes down, all monitoring is *lost*; this is acceptable at this time for the current lab scale but will be expanded in the future for redundancy.


