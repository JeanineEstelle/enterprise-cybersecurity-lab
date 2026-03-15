# VM Network Design

This document describes the virtual networking configuration used to connect all systems in the Enterprise Cybersecurity Lab.

The goal is to reproduce a realistic enterprise network architecture while running entirely inside a virtualized environment.

The lab network is divided into four major security zones:

- WAN (Internet)
- Internal LAN
- DMZ
- SOC monitoring network

Each zone is isolated using VLAN segmentation and firewall rules.

---

# Virtual Network Overview

The virtualization platform provides multiple virtual networks that simulate enterprise networking.

These virtual networks are connected through pfSense, Router R1, and Switch SW1.

| Zone | VLAN | Network | Purpose |
|-----|-----|-----|-----|
| WAN | External | 192.168.100.0/24 | Simulated internet / attacker network |
| Transit | N/A | 10.255.255.0/30 | Firewall-to-router handoff |
| Internal LAN | VLAN 10 | 10.10.10.0/24 | Internal servers |
| Users | VLAN 20 | 10.10.20.0/24 | Employee workstations |
| DMZ | VLAN 30 | 10.10.30.0/24 | Public-facing services |
| SOC | VLAN 40 | 10.10.40.0/24 | Security monitoring infrastructure |

---

# Virtual Network Components

The network architecture includes the following components.

### Router (R1)

The router provides:

- inter-VLAN routing
- traffic forwarding
- gateway services for internal networks

Router interfaces connect to the VLAN networks.

---

### Switch (SW1)

The switch provides:

- VLAN segmentation
- trunk ports
- access ports for systems
- SPAN / port mirroring

SPAN is used to send traffic to Security Onion.

---

### pfSense Firewall

pfSense acts as the **perimeter firewall**.

It separates:

- the WAN network
- the internal enterprise network
- the DMZ

pfSense enforces firewall rules, NAT, and network segmentation.

---

# Network Zones

## WAN (Internet Zone)

This zone simulates the external internet.

Typical systems:

- Kali attacker machine
- simulated internet hosts
- pfSense WAN interface

Example network:

`192.168.100.0/24`

Example IP assignments:

| System | IP |
|------|------|
| pfSense WAN | 192.168.100.1 |
| Kali Linux | 192.168.100.10 |

The WAN network connects to the pfSense WAN interface.

---

## Transit Network

This small routed network connects pfSense to Router R1.

Example network:

`10.255.255.0/30`

Example IP assignments:

| System | IP |
|------|------|
| pfSense LAN / Transit | 10.255.255.1 |
| Router R1 G0/0 | 10.255.255.2 |

This network acts as the handoff between the firewall and the internal enterprise routing domain.

---

## Internal Server Network (VLAN 10)

This network contains core internal infrastructure servers.

Example network:

`10.10.10.0/24`

Typical systems:

- Active Directory Domain Controller
- File Server

Example IP assignments:

| System | IP |
|------|------|
| Domain Controller | 10.10.10.10 |
| File Server | 10.10.10.20 |

Purpose:

- authentication
- identity services
- DNS
- DHCP
- file sharing
- internal infrastructure

---

## User Network (VLAN 20)

This network contains employee workstations.

Example network:

`10.10.20.0/24`

Typical systems:

- Windows Client 1
- Windows Client 2
- Admin workstation

Example IP assignments:

| System | IP |
|------|------|
| Windows Client 1 | 10.10.20.50 |
| Windows Client 2 | 10.10.20.51 |
| Admin Workstation | 10.10.20.60 |

These systems generate endpoint logs that are forwarded to the SOC network.

---

## DMZ (VLAN 30)

The DMZ contains systems exposed to external traffic.

Typical systems:

- web server
- public services

Example network:

`10.10.30.0/24`

Example IP assignments:

| System | IP |
|------|------|
| Web Server | 10.10.30.10 |

Firewall rules strictly control access between the DMZ and internal networks.

---

## SOC Monitoring Network (VLAN 40)

This network hosts the security monitoring infrastructure.

Typical systems:

- Security Onion
- Wazuh
- Splunk
- Elastic Stack
- OpenVAS

Example network:

`10.10.40.0/24`

Example IP assignments:

| System | IP |
|------|------|
| Security Onion | 10.10.40.10 |
| Wazuh | 10.10.40.20 |
| Splunk | 10.10.40.30 |
| Elastic Stack | 10.10.40.40 |
| OpenVAS | 10.10.40.50 |

The SOC network collects logs and monitoring data from all other networks.

---

# Default Gateways

Each network uses the following default gateway.

| Network | Gateway |
|------|------|
| VLAN 10 | 10.10.10.1 |
| VLAN 20 | 10.10.20.1 |
| VLAN 30 | 10.10.30.1 |
| VLAN 40 | 10.10.40.1 |

These gateway IPs are configured on Router R1 using subinterfaces.

---

# Port Mirroring for Monitoring

To allow Security Onion to inspect traffic, the switch uses **SPAN (port mirroring)**.

Traffic from the following networks is mirrored:

- VLAN 10 (Servers)
- VLAN 20 (Users)
- VLAN 30 (DMZ)

Mirrored traffic is sent to:

`Security Onion monitoring interface`

This allows Security Onion to analyze network behavior.

---

# Traffic Flow Example

Example attack scenario:

1. Kali machine sends traffic toward the web server.
2. Traffic enters the firewall through the WAN interface.
3. pfSense forwards traffic to Router R1.
4. Router forwards traffic to VLAN 30 (DMZ).
5. Switch mirrors the traffic to Security Onion.
6. Security Onion analyzes the mirrored packets.
7. Suricata generates an alert if suspicious behavior is detected.

---

# Benefits of Network Segmentation

The segmented architecture improves security by:

- isolating critical systems
- reducing lateral movement
- limiting attack surface
- improving monitoring visibility
- protecting internal systems from DMZ compromise

---

# Related Documentation

See also:

- `03-gns3-integration.md`
- `../../architecture/vlan-design.md`
- `../../architecture/network-design.md`
- `../../architecture/routing-design.md`
