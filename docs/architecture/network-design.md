# Enterprise Lab Network Design

This document describes the architecture and segmentation of the enterprise cybersecurity lab environment.

The lab simulates a real enterprise infrastructure with multiple network zones and security monitoring.

---

# Network Zones

The environment is divided into four security zones.

| Zone | Purpose |
|-----|------|
| WAN | External network / Internet |
| DMZ | Public-facing services |
| Internal LAN | Corporate users and servers |
| SOC Network | Security monitoring infrastructure |

---

# Network Diagram

See:

docs/architecture/diagrams/enterprise-lab-diagram.png

---

# IP Addressing Plan

| Network | VLAN | Purpose |
|------|------|------|
| 192.168.100.0/24 | WAN | External attacker network |
| 10.10.10.0/24 | VLAN 10 | Internal servers |
| 10.10.20.0/24 | VLAN 20 | User workstations |
| 10.10.30.0/24 | VLAN 30 | DMZ |
| 10.10.40.0/24 | VLAN 40 | SOC monitoring |

---

# Infrastructure Components

## Firewall

pfSense firewall controls traffic between zones.

Functions:

- NAT
- firewall rules
- network segmentation
- traffic filtering

---

## Router

Router R1 performs:

- inter-VLAN routing
- network segmentation
- routing between VLANs

---

## Switch

Switch SW1 performs:

- VLAN segmentation
- trunk ports
- access ports
- SPAN port for network monitoring

---

# VLAN Design

## VLAN 10 — Servers

Network:
