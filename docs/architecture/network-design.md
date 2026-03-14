# Enterprise Cybersecurity Lab Network Design

## Overview

This lab simulates a realistic enterprise IT environment to practice cybersecurity operations including threat detection, penetration testing, and incident response.

The lab contains multiple segmented networks connected through a firewall.

---

## Network Zones

### Internet
Represents the public internet where the attacker machine resides.

### WAN
External network connected to the firewall.

### Internal Network (LAN)
Corporate network containing:

- Active Directory Domain Controller
- Windows Workstations
- File Server

### DMZ (Demilitarized Zone)
Public-facing services such as a web server.

### SOC Monitoring Network
Security monitoring infrastructure including:

- Splunk SIEM
- Security Onion IDS

---

## Core Infrastructure

### Firewall
pfSense firewall provides:

- Network segmentation
- NAT
- Firewall rules
- traffic monitoring

### Identity Infrastructure
Active Directory manages:

- Users
- authentication
- group policies

### Monitoring Stack
Security monitoring is handled by:

- Splunk SIEM
- Security Onion IDS
- Wazuh EDR

---

## Security Goals

This lab environment allows testing of:

- network reconnaissance
- vulnerability scanning
- lateral movement
- privilege escalation
- data exfiltration
- SIEM detection
- incident response
