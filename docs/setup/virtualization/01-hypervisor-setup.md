# Hypervisor Setup

This document describes the virtualization and emulation environment used to build the Enterprise Cybersecurity Lab.

The lab combines:

- a virtual machine platform for servers, endpoints, and security tools
- a network emulator for routers and switches

This allows the lab to simulate a realistic enterprise environment without physical devices.

---

# Lab Hosting Model

The lab is hosted on a single physical computer using virtualization.

The host system runs:

- VMware Workstation or VirtualBox
- GNS3

These platforms work together to simulate:

- enterprise servers
- user workstations
- firewalls
- attacker systems
- SOC tools
- CCNA routing and switching components

---

# Virtualization Components

## Virtual Machine Platform

The virtual machine platform is used to run operating systems such as:

- pfSense
- Windows Server
- Windows workstations
- Kali Linux
- Security Onion
- Wazuh
- Splunk
- Elastic Stack
- OpenVAS
- Linux web server

Recommended options:

- VMware Workstation
- VirtualBox

---

## Network Emulator

The network emulator is used to run network devices such as:

- router R1
- switch SW1

Recommended platform:

- GNS3

This allows practice with:

- VLANs
- trunk ports
- inter-VLAN routing
- SPAN / port mirroring
- routing design

---

# Why This Design Is Used

This hybrid design is used because:

- VMware / VirtualBox is ideal for real operating systems
- GNS3 is ideal for routing and switching labs
- the combination supports both CCNA and cybersecurity training
- the entire lab can run without physical networking hardware

---

# Host Requirements

A strong host machine is recommended.

Minimum recommended host resources:

| Resource | Recommended |
|------|------|
| CPU | 8 cores or more |
| RAM | 32 GB |
| Storage | 500 GB SSD or more |

More memory improves the experience significantly.

---

# Example Virtual Machine Allocation

Below is an example VM allocation plan.

| VM | Role | Suggested RAM |
|------|------|------|
| pfSense | Firewall | 2 GB |
| Domain Controller | Active Directory | 4–6 GB |
| File Server | Internal file services | 2–4 GB |
| Windows Client 1 | User workstation | 4 GB |
| Windows Client 2 | User workstation | 4 GB |
| Kali Linux | Attacker system | 2–4 GB |
| Web Server | DMZ | 2 GB |
| Security Onion | Network monitoring | 8–12 GB |
| Wazuh | Host detection | 4 GB |
| Splunk | SIEM | 4–8 GB |
| Elastic Stack | Logging / analytics | 4–8 GB |
| OpenVAS | Vulnerability scanner | 4 GB |

Not all systems must run at the same time during early testing.

---

# Integration Between GNS3 and VMs

GNS3 routers and switches connect to VMware / VirtualBox machines using a shared virtual network.

This allows:

- router and switch traffic to reach the VMs
- VLAN segmentation to apply to real systems
- Security Onion to inspect mirrored traffic
- pfSense to control perimeter access

This makes the lab behave like a real enterprise network.

---

# Key Virtual Zones

The virtualization environment supports these main zones:

- WAN / Internet
- Internal LAN
- DMZ
- SOC Network

These zones are connected using the firewall, router, and switch design documented in the architecture section.

---

# Lab Benefits

Using virtualization provides:

- low cost
- easy snapshots and rollback
- repeatable testing
- flexible network design
- support for attack simulation and detection testing

---

# Related Documentation

See also:

- `../../architecture/network-design.md`
- `../../architecture/vlan-design.md`
- `../../architecture/routing-design.md`
- `02-vm-network-design.md`
- `03-gns3-integration.md`
