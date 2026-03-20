# Lab Roadmap

This roadmap outlines the development trajectory of my homelab — from initial infrastructure deployment through network segmentation, operational maturity, and an eventual dedicated security lab environment. It reflects the actual state of the lab at each stage, not just aspirational goals.

---

## Phase 1 — Foundation `[Complete]`

Established the core infrastructure that everything else builds on. This phase focused on getting reliable, functional systems in place before adding complexity.

**What was built:**
- Proxmox VE hypervisor deployed on repurposed hardware as the primary virtualization platform
- pfSense firewall VM handling NAT, DHCP, and network policy enforcement
- Raspberry Pi nodes deployed for dedicated service hosting (Pi-hole, NetBox, Netdata, Uptime Kuma)
- Cisco SG350 managed switch providing layer 2 connectivity across lab devices
- Base subnet (`192.168.200.0/24`) established with static IP assignments for all infrastructure

**Outcome:** A stable, functional lab environment with core networking and monitoring operational.

---

## Phase 2 — Network Segmentation `[In Progress]`

Introducing VLAN-based segmentation to isolate traffic by function and reduce the blast radius of any single compromised device or misconfiguration.

**Current VLAN design:**
| VLAN | Name | Purpose |
|------|------|---------|
| 1 | Main Lab | Primary lab subnet — production infrastructure and services |
| 10 | Testing / Unsecured Devices | Isolated segment for experimental configs and untrusted devices |

**What this phase involves:**
- Configuring VLANs on the Cisco SG350 with appropriate trunk and access port assignments
- Building out pfSense VLAN interfaces with dedicated firewall rules per segment
- Validating inter-VLAN routing policy — what can talk to what, and why
- Moving switch management access to a controlled interface once segmentation is stable

**Outcome:** Traffic isolation between production and testing environments, with firewall-enforced boundaries between segments.

---

## Phase 3 — Operational Maturity `[Planned]`

With segmentation in place, the focus shifts to making the lab easier to operate, audit, and hand off to someone else — including future me. This phase is about rigor and documentation depth.

**NetBox as source of truth:**
- Migrate all IP assignments, device records, and VLAN definitions into NetBox
- Treat NetBox as the authoritative reference for the lab's network state rather than scattered notes or memory
- Keep records current as the lab evolves — this is an operational discipline goal, not just a one-time data entry task

**Documentation coverage:**
- Complete remaining infrastructure and network documentation files
- Ensure every major design choice has a corresponding entry in `design-decisions.md`
- Documentation should reflect the lab as it actually exists, not as it was originally planned

**Outcome:** A lab that is fully documented, auditable, and maintainable — not just functional.

---

## Phase 4 — Security Lab `[Planned]`

The long-term goal is a dedicated environment for offensive and defensive security practice — isolated from production infrastructure and purpose-built for hands-on security work.

**What this looks like:**
- A separate network segment (or additional VLAN) with no connectivity to production lab resources
- Vulnerable-by-design targets (e.g., Metasploitable, DVWA, custom VMs) for practice
- Defensive tooling — likely an IDS/IPS such as Snort or Suricata — to monitor and analyze traffic within the segment
- Structured around the skills being built toward CompTIA Security+ and beyond

**Why this comes last:**
A security lab built on a poorly segmented network is a liability. Phases 2 and 3 have to be solid before this environment is safe to run.

**Outcome:** A functional attack/defense environment that supports active security skill development in a controlled, isolated segment.