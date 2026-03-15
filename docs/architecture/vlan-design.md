# VLAN Design

This document describes the VLAN structure used in the Enterprise Cybersecurity Lab.

The lab uses VLAN segmentation to separate users, servers, DMZ services, and SOC monitoring systems.

This improves:

- security
- isolation
- traffic control
- monitoring visibility

---

# VLAN Overview

| VLAN | Name | Network | Purpose |
|-----|------|------|------|
| 10 | SERVERS | 10.10.10.0/24 | Internal infrastructure servers |
| 20 | USERS | 10.10.20.0/24 | Employee workstations |
| 30 | DMZ | 10.10.30.0/24 | Public-facing web services |
| 40 | SOC | 10.10.40.0/24 | Security monitoring tools |

---

# VLAN 10 — Servers

Network:

10.10.10.0/24

Typical systems:

- Active Directory Domain Controller
- File Server

Purpose:

- identity management
- authentication
- file storage
- internal infrastructure

---

# VLAN 20 — Users

Network:

10.10.20.0/24

Typical systems:

- Windows 10 Workstation 1
- Windows 10 Workstation 2
- Admin workstation

Purpose:

- employee access
- internal user traffic
- endpoint monitoring

---

# VLAN 30 — DMZ

Network:

10.10.30.0/24

Typical systems:

- Web Server

Purpose:

- host public-facing services
- isolate internet-facing applications
- reduce risk to internal network

---

# VLAN 40 — SOC

Network:

10.10.40.0/24

Typical systems:

- Security Onion
- Wazuh
- Splunk
- Elastic Stack
- OpenVAS

Purpose:

- centralized monitoring
- threat detection
- SIEM analysis
- vulnerability scanning

---

# Switch Port Design

The switch separates devices by assigning each interface to the correct VLAN.

## Example access ports

| Port | VLAN | Device |
|-----|------|------|
| Fa0/2 | 10 | AD Domain Controller |
| Fa0/3 | 10 | File Server |
| Fa0/4 | 20 | Windows Client 1 |
| Fa0/5 | 20 | Windows Client 2 |
| Fa0/6 | 30 | DMZ Web Server |
| Fa0/7 | 40 | Security Onion |
| Fa0/8 | 40 | Wazuh |
| Fa0/9 | 40 | Splunk |
| Fa0/10 | 40 | Elastic Stack |
| Fa0/11 | 40 | OpenVAS |

---

# Trunk Port Design

The router connects to the switch using a **trunk port**.

This trunk carries:

- VLAN 10
- VLAN 20
- VLAN 30
- VLAN 40

Example trunk port:

Fa0/1 → Router R1

Purpose:

- allow inter-VLAN routing
- centralize network segmentation

---

# Example Cisco Switch Configuration

## Create VLANs

```cisco
vlan 10
 name SERVERS

vlan 20
 name USERS

vlan 30
 name DMZ

vlan 40
 name SOC

## **Congiguration of access port**

interface fa0/2
 switchport mode access
 switchport access vlan 10

interface fa0/3
 switchport mode access
 switchport access vlan 10

interface fa0/4
 switchport mode access
 switchport access vlan 20

interface fa0/5
 switchport mode access
 switchport access vlan 20

interface fa0/6
 switchport mode access
 switchport access vlan 30

interface fa0/7
 switchport mode access
 switchport access vlan 40


## **Congiguration trunk port to router**

interface fa0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40

## Example of SPAN / PORT Mirroring
monitor session 1 source vlan 10 , 20 , 30
monitor session 1 destination interface fa0/7
