# Enterprise Cybersecurity Lab Network Design

This document explains the architecture and network segmentation of the enterprise cybersecurity lab environment.

The lab simulates a real enterprise network with multiple zones:

- Internet (WAN)
- DMZ
- Internal LAN
- SOC monitoring network

The goal is to simulate real-world enterprise infrastructure while enabling cybersecurity monitoring and attack simulation.

---

# Network Zones

## 1. WAN / Internet

This network simulates the external internet where attackers originate.

Network:

192.168.100.0/24

Example host:

Kali Linux attacker  
IP: 192.168.100.10

The WAN network connects to the firewall which protects the internal infrastructure.

---

## 2. Firewall

The network is protected by a **pfSense firewall**.

Firewall interfaces:

WAN interface  
192.168.100.1

LAN interface  
10.255.255.1

The firewall performs:

- NAT
- traffic filtering
- network segmentation
- DMZ access control

---

# Core Network

Behind the firewall is the **enterprise internal network**.

The network uses **VLAN segmentation** to isolate different environments.

A router performs **inter-VLAN routing** and a switch handles VLAN tagging.

---

# VLAN Design

| VLAN | Network | Purpose |
|-----|------|------|
| VLAN 10 | 10.10.10.0/24 | Servers |
| VLAN 20 | 10.10.20.0/24 | Users |
| VLAN 30 | 10.10.30.0/24 | DMZ |
| VLAN 40 | 10.10.40.0/24 | SOC monitoring |

---

# VLAN 10 — Server Network

Network:

10.10.10.0/24

This VLAN hosts the internal enterprise servers.

Servers include:

### Active Directory Domain Controller

Services:

- Active Directory Domain Services
- DNS
- DHCP
- Group Policy
- Kerberos / NTLM authentication

### File Server

Services:

- SMB file sharing
- shared drives
- user home directories
- backup storage

---

# VLAN 20 — User Network

Network:

10.10.20.0/24

This VLAN contains employee workstations.

Devices:

- Windows 10 clients
- domain joined machines

Workstations send logs to the SOC monitoring systems.

---

# VLAN 30 — DMZ Network

Network:

10.10.30.0/24

This VLAN hosts **public facing services**.

Example service:

### Web Server

Services:

- Apache / Nginx / IIS
- HTTP
- HTTPS
- web application hosting
- reverse proxy

The firewall restricts access between the DMZ and internal network.

---

# VLAN 40 — SOC Monitoring Network

Network:

10.10.40.0/24

This VLAN hosts the **security monitoring infrastructure**.

Tools include:

### Security Onion

Used for:

- network intrusion detection
- network traffic monitoring
- Zeek analysis
- Suricata alerts

---

### Wazuh

Host intrusion detection system used for:

- log analysis
- host monitoring
- threat detection
- file integrity monitoring

---

### Splunk

Used as a SIEM for:

- centralized log analysis
- event correlation
- threat hunting

---

### Elastic Stack

Used for:

- log ingestion
- analytics
- security dashboards

---

### OpenVAS

Used for vulnerability management.

OpenVAS scans systems for:

- missing patches
- vulnerable software
- misconfigurations
- known CVEs

---

# Switch Configuration

The enterprise switch uses VLAN trunking.

Trunk ports carry:

VLAN 10  
VLAN 20  
VLAN 30  
VLAN 40

Access ports assign endpoints to specific VLANs.

Example:

AD server → VLAN 10  
Workstations → VLAN 20  
Web server → VLAN 30  
SOC tools → VLAN 40

---

# Network Monitoring

Traffic monitoring is implemented using **port mirroring (SPAN)**.

The switch mirrors network traffic to the **Security Onion sensor** for analysis.

Security Onion analyzes:

- network flows
- suspicious connections
- attack signatures

---

# Log Collection

Logs are forwarded to the SOC monitoring systems using:

- Wazuh agents
- Winlogbeat
- Filebeat
- Syslog

Logs include:

- authentication events
- firewall events
- Windows event logs
- network activity
- attack activity

---

# Traffic Flow

Example traffic flow during an attack:

1. Attacker launches scan from Kali Linux
2. Traffic passes through firewall
3. Traffic reaches internal network
4. Security Onion detects suspicious activity
5. Logs are forwarded to Splunk and Elastic
6. Alerts are generated for investigation

---

# Architecture Diagram

See the architecture diagram in:

docs/architecture/diagrams/enterprise-lab-diagram.png
