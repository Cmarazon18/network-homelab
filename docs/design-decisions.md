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


## Four-VLAN Network Segmentation Design

**Status:** Active

### Context
The lab runs several categories of devices with very different trust levels — core 
infrastructure (pfSense, NetBox), monitoring systems, and a dedicated security lab 
segment intended for running potentially hostile or untested workloads. Running 
everything on a single flat network meant that any compromise in one area, 
particularly the security lab, could spread laterally to critical infrastructure 
with no barriers. A segmented design was needed to contain blast radius and enforce 
trust boundaries between device categories.

### Decision
Implemented a four-VLAN scheme on the Cisco SG350 with pfSense handling inter-VLAN 
routing via subinterfaces, dividing the network into Infrastructure, Management & 
Monitoring, Clients, and Security Lab segments.

### Reasoning
- Security lab workloads need to be isolated by design — experiments with untrusted 
  tools or configurations should not have unrestricted access to core infrastructure
- Infrastructure and management devices benefit from static, predictable addressing 
  separate from general client traffic
- Separating monitoring onto its own VLAN ensures visibility into all segments 
  without placing monitoring tools at the same trust level as the devices they watch
- The Cisco SG350 supports 802.1Q trunking natively, making VLAN enforcement 
  manageable at the switch level rather than relying on software controls alone

### Alternatives Considered

| Option | Why Rejected |
|--------|--------------|
| Single flat network | No lateral movement protection — a compromise anywhere affects everything |
| Two-VLAN split (lab vs. everything else) | Too coarse — doesn't separate security lab risk from core infrastructure |
| Dedicated physical switches per segment | Cost and physical complexity not justified at this lab scale |

### Tradeoffs & Accepted Risks
Adds configuration complexity — inter-VLAN routing rules, trunk port setup, and 
DHCP scopes all have to be maintained in sync. During initial implementation this 
caused access loss that required a factory reset of the switch. The ordered 
implementation approach (trunk and VLANs before moving switch management) was 
established as a direct result of that failure.

### Outcome
VLAN implementation is in progress following a switch factory reset. The corrected 
implementation sequence has been established and is being executed methodically to 
avoid repeat access loss.