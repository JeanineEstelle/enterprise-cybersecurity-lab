# Enterprise Cybersecurity Lab Architecture

This lab simulates a realistic enterprise cybersecurity environment including:

- Internet attacker simulation using Kali Linux
- pfSense firewall with WAN, LAN, and DMZ segmentation
- Active Directory domain controller
- Windows client workstations
- DMZ web server
- SOC monitoring stack using Splunk and Security Onion

The goal of this architecture is to simulate real enterprise infrastructure and allow testing of offensive and defensive cybersecurity techniques.

## Network Segmentation

The network is divided into three main segments:

### WAN
Represents the internet where the attacker machine resides.

### Internal Network
Contains domain infrastructure and user workstations.

### DMZ
Hosts public-facing services such as the web server.

### SOC Monitoring Network
Contains logging and intrusion detection systems.

## Core Components

Firewall:
pfSense

Domain Infrastructure:
Windows Server Active Directory

Workstations:
Windows 10 clients joined to domain

Monitoring Stack:
Splunk SIEM  
Security Onion IDS

Attacker System:
Kali Linux
