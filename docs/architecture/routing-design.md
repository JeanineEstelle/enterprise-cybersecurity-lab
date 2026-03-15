# Routing Design

This document explains how routing works in the Enterprise Cybersecurity Lab.

The lab uses:

- pfSense as the perimeter firewall
- a router for inter-VLAN routing
- a switch for VLAN segmentation

The goal is to simulate realistic enterprise routing between users, servers, the DMZ, and SOC infrastructure.

---

# Routing Overview

Traffic flows through the following path:

Internet → pfSense Firewall → Router R1 → Switch SW1 → VLANs

The firewall protects the internal network from the internet.

The router performs inter-VLAN routing so that hosts in different VLANs can communicate when allowed.

---

# WAN and Transit Networks

## WAN / Internet Network

Network:

192.168.100.0/24

Example devices:

- Kali Linux attacker → 192.168.100.10
- pfSense WAN → 192.168.100.1

This network simulates the external internet.

---

## Transit Network Between Firewall and Router

Network:

10.255.255.0/30

Example interfaces:

- pfSense LAN → 10.255.255.1
- Router R1 G0/0 → 10.255.255.2

Purpose:

- connect the firewall to the internal router
- separate WAN from internal VLAN routing
- provide a realistic routed handoff

---

# Internal VLAN Gateways

Each VLAN has a gateway IP address configured on the router.

| VLAN | Network | Gateway |
|-----|------|------|
| 10 | 10.10.10.0/24 | 10.10.10.1 |
| 20 | 10.10.20.0/24 | 10.10.20.1 |
| 30 | 10.10.30.0/24 | 10.10.30.1 |
| 40 | 10.10.40.0/24 | 10.10.40.1 |

These gateway IPs are configured using router subinterfaces.

---

# Router-on-a-Stick Design

The router connects to the switch using a trunk port.

The router uses one physical interface with multiple subinterfaces:

- G0/1.10 for VLAN 10
- G0/1.20 for VLAN 20
- G0/1.30 for VLAN 30
- G0/1.40 for VLAN 40

This design is commonly called **router-on-a-stick**.

---

# Example Router Configuration

## Transit Interface to pfSense

```cisco
interface g0/0
 ip address 10.255.255.2 255.255.255.252
 no shutdown
interface g0/1.10
 encapsulation dot1Q 10
 ip address 10.10.10.1 255.255.255.0

interface g0/1.20
 encapsulation dot1Q 20
 ip address 10.10.20.1 255.255.255.0

interface g0/1.30
 encapsulation dot1Q 30
 ip address 10.10.30.1 255.255.255.0

interface g0/1.40
 encapsulation dot1Q 40
 ip address 10.10.40.1 255.255.255.0
