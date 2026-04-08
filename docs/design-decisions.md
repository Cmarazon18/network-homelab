# Design Decisions
This document outlines the design decisions made during the development of this project. It serves as a reference for understanding the rationale behind certain choices and can help guide future development.

## Self-Hosted NetBox Over Cloud-Hosted Options

Chose to run NetBox locally via Docker Compose rather than using the NetBox Cloud 
free tier.

**Reasoning:** The free tier of NetBox Cloud has a data retention limit — when the 
trial expired, all data was lost. Self-hosting gives full control over the database 
and ensures continuity. It also better reflects real-world enterprise deployments 
where IPAM is internal infrastructure.

**Alternatives considered:** NetBox Cloud (free tier), PHP IPAM  
**Rejected because:** Cloud trial caused actual data loss during this build. 
PHP IPAM is less industry-recognized than NetBox.


## VLAN Segmentation Scheme
  
**Status:** Active (third iteration)

### Context

The lab runs devices with fundamentally different trust levels on a shared physical switch — production infrastructure (pfSense, Pi-hole, NetBox, monitoring), a VoIP system, and a planned security lab for intentionally vulnerable machines. A flat network would mean a compromised test VM could reach the DNS server, the firewall management interface, and everything else with no barriers. Segmentation was needed to contain blast radius and enforce trust boundaries between device categories.

### Decision

Implemented a three-VLAN scheme using the Cisco SG350 for 802.1Q enforcement and pfSense for inter-VLAN routing via subinterfaces on the trunk link (GE8). Each VLAN gets its own /24 subnet.

| VLAN | Subnet | Purpose |
|------|--------|---------|
| VLAN 1 (native) | `192.168.200.0/24` | Main lab — all production infrastructure |
| VLAN 10 | `192.168.10.0/24` | Security lab — intentionally vulnerable test machines |
| VLAN 20 | `192.168.20.0/24` | VoIP — FreePBX, SIP phones |

### Reasoning

- VoIP traffic is latency-sensitive and benefits from isolation — SIP/RTP shouldn't compete with or be affected by security lab experiments
- The security lab exists specifically to run hostile or untested workloads; isolating it from production infrastructure is the entire point of building VLANs
- Each VLAN gets a full /24 to keep subnetting simple and avoid premature optimization — 254 usable addresses per segment is far more than needed, but avoids the complexity of CIDR boundary management for no real benefit
- pfSense as the inter-VLAN router means all cross-VLAN traffic passes through the firewall, giving explicit control over what each segment can reach
- The SG350 handles VLAN enforcement at Layer 2, so segmentation doesn't depend on software controls alone

### Alternatives Considered

| Option | Why Rejected |
|--------|--------------|
| Four-VLAN /26 scheme (Infrastructure, Management, Clients, Security Lab — all carved from 192.168.200.0/24) | Original plan. Overengineered for a homelab with fewer than 10 devices. Splitting a single /24 into four /26 subnets added CIDR math overhead, created artificial address pressure (62 usable hosts per segment), and made the subnet-to-VLAN mapping less readable. Implementation failed during initial SG350 configuration — a sequencing mistake (moving switch management before trunk was stable) caused total access loss and required a factory reset. The failure exposed that the design was adding complexity without proportional benefit. |
| Two-VLAN scheme (VLAN 1 main lab + VLAN 10 security lab only) | Worked well as the initial post-reset design. Superseded when FreePBX was deployed — VoIP has legitimate isolation needs (QoS sensitivity, different security posture than infrastructure) that justified a third VLAN rather than dumping phones onto VLAN 1. |
| Single flat network (no VLANs) | No lateral movement protection. A compromised device anywhere can reach everything. Defeats the security focus of the lab. |
| Dedicated physical switches per segment | Cost and physical complexity not justified. The SG350 handles 802.1Q natively and a single managed switch with proper trunk configuration achieves the same logical separation. |

### Tradeoffs & Accepted Risks

- VLAN 1 at `192.168.200.0/24` breaks the pattern where VLAN ID maps to the third octet (VLAN 10 → 192.168.10.0, VLAN 20 → 192.168.20.0). The main lab subnet was established before VLANs were planned, and re-addressing every device to force aesthetic consistency wasn't worth the disruption.
- Inter-VLAN routing through pfSense means pfSense is a single point of failure for all cross-VLAN communication. If pfSense goes down, VLANs become fully isolated from each other. Acceptable for a homelab; would not fly in production.
- VLAN 20 firewall rules are currently allow-all — VoIP is isolated at Layer 2 but has no meaningful Layer 3 access control yet. This is a known gap being addressed as a separate work item.
- VLAN 10 is configured on the switch and pfSense but has no devices deployed. It's a placeholder waiting for vulnerable test machines once the rest of the network is stable and documented.

### Outcomes

The scheme has survived three iterations. The original four-VLAN /26 design failed during implementation and taught a critical lesson about sequencing switch configuration changes (always establish the trunk before moving management). The simplified two-VLAN design proved stable and was extended cleanly to three VLANs when FreePBX gave a real use case for a third segment. Adding VLAN 20 required no changes to existing VLAN 1 or 10 configuration — just a new VLAN on the SG350 trunk, a new pfSense subinterface, and a tagged VM NIC in Proxmox.




## Dedicated VLAN for VoIP Traffic
**Status:** Active

### Context
The lab needed a VoIP system (FreePBX) to practice PBX administration and SIP configuration. The question was whether to place it on the existing main lab subnet (VLAN 1, 192.168.200.0/24) alongside all other infrastructure, or isolate it on its own VLAN.

### Decision
FreePBX and all IP phones are isolated on VLAN 20 (192.168.20.0/24), a dedicated VoIP segment with its own pfSense sub-interface, DHCP scope, and firewall rules.
 
### Reasoning
 
- VoIP is latency and jitter-sensitive. In production networks, voice traffic is segmented to prevent data traffic (backups, large file transfers, scanning) from degrading call quality. Implementing this in the lab mirrors real-world practice.
- A dedicated VLAN creates a meaningful firewall rule design exercise. Unlike a test VLAN that exists only to prove the feature works, VoIP segmentation requires thinking through what traffic actually needs to cross VLAN boundaries (SIP registration, RTP media, DNS) and what shouldn't.
- Segmentation limits the blast radius if the PBX is compromised. FreePBX exposes SIP services, a web GUI, and runs Asterisk — all potential attack surfaces. Isolating it means a compromise doesn't grant direct access to management infrastructure on VLAN 1.
 
### Alternatives Considered
 
| Option | Why Rejected |
|--------|--------------|
| Place FreePBX on VLAN 1 (flat network) | Would work functionally but eliminates the segmentation learning opportunity and doesn't reflect how VoIP is deployed in production. |
| Share a VLAN with other non-critical services | No other services justified the same isolation. Grouping unrelated services on one VLAN just to avoid creating another one doesn't teach anything useful. |
 
### Tradeoffs & Accepted Risks
Inter-VLAN routing adds complexity. The softphone on VLAN 1 must register to FreePBX across VLANs, which means understanding how firewall rules work. Firewall rules are currently allow-all on VLAN 20, which means the segmentation provides Layer 2 isolation but not yet meaningful access control. Tightening those rules is in progress.
 
### Outcome
VLAN 20 is operational. FreePBX, two desk phones, and a cross-VLAN softphone are all working. The segmentation exposed real troubleshooting scenarios (firewall protocol selection breaking DNS, SIP port mismatches across VLANs) that wouldn't have surfaced on a flat network.