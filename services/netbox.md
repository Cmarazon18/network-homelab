# NetBox
## Purpose
NetBox serves as the source of truth for IP Address Management, Device inventory management, Subnet/VLAN allocations, and IP address reservations to maintain an accurate record of the network.
## Deployment
**Host Device:** *pi02* (192.168.200.6)
**Method:** Docker Compose (custom stack)
**Stack:** NetBox, PostgreSQL 15, Redis 7
## Containers
| Container | Purpose|
|---|---|
|`netbox` | Web application and API |
|`netbox-postgres` | PostgreSQL database backend|
|`netbox-redis` | Redis cache and task queue |

After bringing the stack up, the database schema is initialized via Django migrations
and a superuser account is created through the NetBox management CLI.
## Access
- Web UI: `http://192.168.200.6:8000`

## Backup
PostgreSQL is backed up daily at 2:00 AM via cron to a USB flash drive mounted at `/mnt/netbox-backups`. Backups that are older than 30 days are automatically removed via another cron script.

## Why Self Hosted
A previous instance that was hosted on a NetBox Cloud trial expired and resulted in data loss. This deployment uses explicit Docker volume mounts and scheduled backups to ensure persistence.

## Tracked Network Prefixes
- `192.168.145.0/24`- Home Wifi Network
- `192.168.200.0/24` - Lab subnet (managed by pfSense)
- `192.168.10.0/24` - Cybersecurity testing lab (intentionally vulnerable VMs.)