# GNS3 Integration

This document explains how GNS3 is integrated with the virtualization platform to create a realistic enterprise network environment.

The lab combines:

- virtual machines (servers, endpoints, security tools)
- virtual routers and switches
- a simulated enterprise network topology

This allows both **cybersecurity testing** and **CCNA networking practice**.

---

# Why GNS3 Is Used

GNS3 allows the lab to simulate real networking equipment.

Using GNS3 we can configure:

- routers
- switches
- VLAN segmentation
- trunk ports
- routing
- port mirroring
- enterprise traffic flows

This is significantly more realistic than relying only on hypervisor networking.

---

# Lab Architecture Overview

The enterprise lab network is structured as follows:
Internet (WAN)
│
Kali Linux (Attacker)
│
pfSense Firewall
│
Router R1
│
Switch SW1
│
────────────────────────────
│ │ │ │
VLAN10 VLAN20 VLAN30 VLAN40
Servers Users DMZ SOC

VMware / VirtualBox machines connect to the **GNS3 switch ports** using the GNS3 Cloud node.

---

# Key GNS3 Components

The following devices are created inside GNS3.

| Device | Role |
|------|------|
Router R1 | Inter-VLAN routing |
Switch SW1 | VLAN segmentation |
Cloud node | Connects GNS3 to virtualization networks |

The **Cloud node** allows GNS3 to communicate with virtual machines.

---

# Cloud Node Purpose

The Cloud node connects GNS3 to the virtualization platform.

It links GNS3 interfaces to:

- VMware VMnet adapters
- VirtualBox host-only networks
- physical network interfaces (optional)

Example mapping:

| VM Network | Purpose |
|------|------|
VMnet1 | Internal LAN |
VMnet2 | DMZ |
VMnet3 | SOC Network |
VMnet8 | WAN |

This allows virtual machines to behave as if they are connected to a physical switch.

---

# Network Connections

## WAN Connection
Kali VM
│
pfSense WAN
│
Internet network (192.168.100.0/24)

The WAN network simulates external internet traffic.

Example IP assignments:

| Device | IP |
|------|------|
pfSense WAN | 192.168.100.1 |
Kali Linux | 192.168.100.10 |

---

## Firewall to Router (Transit Network)
pfSense LAN
│
Router R1 G0/0

This is the **transit network** between the firewall and internal routing.

Example network:
10.255.255.0/30


Example IP assignments:

| Device | IP |
|------|------|
pfSense LAN | 10.255.255.1 |
Router R1 G0/0 | 10.255.255.2 |

---

## Router to Switch
Router R1 G0/1
│
Switch SW1 G0/1 (trunk)

This trunk carries all VLAN traffic.

---

# VLAN Configuration

The switch contains multiple VLANs.

| VLAN | Network | Purpose |
|-----|------|------|
VLAN 10 | 10.10.10.0/24 | Internal servers |
VLAN 20 | 10.10.20.0/24 | User workstations |
VLAN 30 | 10.10.30.0/24 | DMZ |
VLAN 40 | 10.10.40.0/24 | SOC monitoring |

Each VLAN corresponds to a separate subnet.

Router R1 performs **inter-VLAN routing** using subinterfaces.

Example router gateways:

| VLAN | Gateway |
|------|------|
VLAN 10 | 10.10.10.1 |
VLAN 20 | 10.10.20.1 |
VLAN 30 | 10.10.30.1 |
VLAN 40 | 10.10.40.1 |

---

# VM Connections to GNS3

Virtual machines connect to specific switch ports through the Cloud node.

Example mapping:

| VM | VLAN | Network |
|------|------|------|
Domain Controller | VLAN 10 | 10.10.10.10 |
File Server | VLAN 10 | 10.10.10.20 |
Windows Client 1 | VLAN 20 | 10.10.20.50 |
Windows Client 2 | VLAN 20 | 10.10.20.51 |
Web Server | VLAN 30 | 10.10.30.10 |
Security Onion | VLAN 40 | 10.10.40.10 |
Wazuh | VLAN 40 | 10.10.40.20 |
Splunk | VLAN 40 | 10.10.40.30 |
Elastic Stack | VLAN 40 | 10.10.40.40 |
OpenVAS | VLAN 40 | 10.10.40.50 |

---

# SPAN Port for Security Onion

Security Onion monitors network traffic using **port mirroring (SPAN)**.

The switch mirrors traffic from:

- VLAN 10 (Servers)
- VLAN 20 (Users)
- VLAN 30 (DMZ)

Mirrored traffic is sent to the **Security Onion monitoring interface**.

Security Onion analyzes traffic using:

- Suricata (IDS)
- Zeek (network analysis)

This allows detection of:

- port scans
- lateral movement
- malware traffic
- suspicious connections

---

# Example Traffic Flow

Example attack scenario:

1. Kali runs an **Nmap scan** against the web server.
2. Traffic enters through **pfSense WAN**.
3. pfSense forwards traffic to **Router R1**.
4. Router forwards traffic to **VLAN 30 (DMZ)**.
5. Switch mirrors the packets to **Security Onion**.
6. Suricata detects suspicious scanning activity.
7. Alerts are generated and sent to the SOC tools.

---

# Benefits of GNS3 Integration

Integrating GNS3 with virtualization provides several advantages.

It enables:

- realistic router and switch configuration
- CCNA-level networking practice
- true VLAN segmentation
- advanced traffic monitoring
- enterprise-style network simulation

This architecture closely resembles real corporate networks used in enterprise environments.

---

# Related Documentation

See also:

- `01-hypervisor-setup.md`
- `02-vm-network-design.md`
- `../../architecture/network-design.md`
- `../../architecture/routing-design.md`


