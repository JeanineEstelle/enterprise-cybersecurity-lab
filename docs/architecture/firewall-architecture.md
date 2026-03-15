# Firewall Architecture

This document explains the firewall design used in the Enterprise Cybersecurity Lab.

The lab uses **pfSense** as the perimeter firewall to protect the internal network from external threats.

The firewall controls traffic between:

- Internet (WAN)
- Internal LAN
- DMZ
- SOC monitoring network

---

# Firewall Overview

The firewall sits between the **Internet and the internal infrastructure**.

Traffic flow:

Internet → pfSense Firewall → Router → VLAN Networks

The firewall performs:

- NAT
- traffic filtering
- network segmentation
- DMZ protection
- outbound access control

---

# Firewall Interfaces

## WAN Interface

Purpose:

Connects to the simulated internet.

Network:

192.168.100.0/24

Example configuration:

| Interface | IP Address |
|------|------|
WAN | 192.168.100.1 |

Example host on WAN:

Kali Linux attacker  
192.168.100.10

---

## LAN Interface

Purpose:

Connects pfSense to the internal router.

Network:

10.255.255.0/30

Example configuration:

| Interface | IP Address |
|------|------|
LAN | 10.255.255.1 |

Router interface:

10.255.255.2

This network acts as a **transit network** between firewall and internal routing.

---

# Firewall Responsibilities

pfSense is responsible for:

- protecting the internal network
- filtering inbound traffic
- controlling outbound access
- isolating the DMZ
- logging network activity

---

# Network Zones Protected by Firewall

| Zone | Network | Description |
|-----|------|------|
WAN | 192.168.100.0/24 | Internet / attacker network |
LAN | 10.10.10.0/24 | Internal servers |
USERS | 10.10.20.0/24 | Employee workstations |
DMZ | 10.10.30.0/24 | Public web server |
SOC | 10.10.40.0/24 | Security monitoring tools |

---

# Firewall NAT Configuration

The firewall performs **Network Address Translation (NAT)** for outbound traffic.

Example:

Internal client:

10.10.20.10

Translated to:

192.168.100.1

This allows internal systems to access external networks safely.

---

# Inbound Firewall Rules

Inbound rules control what traffic from the internet is allowed.

Example rules:

| Source | Destination | Port | Purpose |
|------|------|------|------|
Internet | DMZ Web Server | 80 | HTTP |
Internet | DMZ Web Server | 443 | HTTPS |

All other inbound traffic is blocked.

---

# Outbound Firewall Rules

Outbound traffic from internal networks is allowed but monitored.

Example:

| Source | Destination | Purpose |
|------|------|------|
Users VLAN | Internet | software updates |
Servers VLAN | Internet | system updates |
SOC VLAN | All VLANs | log collection |

---

# DMZ Isolation

The DMZ is separated from internal networks.

Rules include:

- DMZ cannot freely access internal servers
- only specific services allowed
- inbound traffic limited to web services

Example:

Allowed:

DMZ Web Server → SOC log server

Blocked:

DMZ Web Server → Active Directory

---

# Logging

pfSense logs important events such as:

- blocked connections
- allowed connections
- port scans
- suspicious traffic

Logs can be forwarded to:

- Splunk
- Elastic Stack
- Security Onion

This enables centralized monitoring.

---

# Security Benefits

Using a firewall provides:

- protection from external attackers
- controlled access to internal systems
- isolation of exposed services
- logging and monitoring capabilities

---

# Example Attack Scenario

Example attack:

1. Kali Linux attacker scans the DMZ web server.
2. Traffic reaches pfSense firewall.
3. pfSense blocks unauthorized ports.
4. Allowed HTTP traffic reaches the web server.
5. Security Onion detects scanning activity.
6. Logs are forwarded to Splunk and Elastic.

---

# Related Documentation

See also:

- `network-design.md`
- `vlan-design.md`
- `routing-design.md`
- `soc-architecture.md`
