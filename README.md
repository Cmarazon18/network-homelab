# Homelab Network & Security Environment

This repository contains documentation and configurations for a segmented homelab focused on networking, security, and infrastructure troubleshooting.

The lab is actively developed and reflects real-world configuration changes, failures, and iterative improvements.

---

## Overview

- Multi-VLAN network using pfSense (bare metal)
- Managed switching with VLAN trunking
- Segmented environments for lab, VoIP, and IoT
- Self-hosted infrastructure and monitoring services

---

## Documentation Structure

All detailed documentation is separated into dedicated files:

- Network topology and architecture → `/docs/network/`
- VLAN configuration and IP addressing → `/docs/network/vlans.md`
- Firewall rules and segmentation → `/docs/network/firewall.md`
- Services and infrastructure → `/docs/services/`
- DNS configuration → `/docs/network/dns.md`
- Troubleshooting and failure cases → `/docs/troubleshooting/`

---

## Infrastructure

Hardware and service details are documented here:

- Hardware inventory → `/docs/infrastructure/hardware.md`
- Service stack (Pi-hole, NetBox, etc.) → `/docs/services/overview.md`

---

## Design Notes

- WAN operates behind a home network (double NAT)
- VLAN segmentation is used for isolation and testing
- Firewall rules follow a least-privilege model

---

## Purpose

This project is used to:

- Develop networking and cybersecurity skills
- Simulate real infrastructure environments
- Practice troubleshooting and root cause analysis
- Document system design and changes over time

---

## Status

This lab is continuously evolving.  
