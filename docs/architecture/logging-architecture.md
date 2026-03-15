# Logging Architecture

This document explains how security logs are collected, forwarded, stored, and analyzed in the Enterprise Cybersecurity Lab.

The goal of the logging architecture is to provide centralized visibility across:

- servers
- user workstations
- firewall activity
- DMZ systems
- attack simulations
- network traffic

The logging architecture supports:

- SIEM analysis
- incident response
- threat hunting
- alerting
- forensic investigation

---

# Logging Goals

The lab collects logs in order to:

- monitor authentication activity
- detect suspicious behavior
- identify policy violations
- investigate attacks
- correlate host and network events

---

# Log Sources

The environment contains multiple log-producing systems.

| Source | Type of Logs |
|------|------|
| Active Directory Domain Controller | authentication, directory, group policy, DNS |
| File Server | SMB access, file activity, user access |
| Windows Workstations | logons, PowerShell, process creation, local security events |
| DMZ Web Server | web access, application logs, system logs |
| pfSense Firewall | allowed / blocked traffic, NAT, firewall events |
| Security Onion | Suricata alerts, Zeek logs, traffic metadata |
| OpenVAS | vulnerability scan results |
| Kali Linux | attack activity for correlation and lab validation |

---

# Logging Destinations

Logs are centralized into the SOC environment.

| Tool | Role |
|------|------|
| Wazuh | host-based log collection and detection |
| Splunk | centralized SIEM analysis |
| Elastic Stack | log ingestion, indexing, dashboards |
| Security Onion | network monitoring and alerting |

---

# Host Log Collection

## Windows Systems

Windows systems generate logs such as:

- Event ID 4624 (successful logon)
- Event ID 4625 (failed logon)
- Event ID 4688 (process creation)
- PowerShell operational logs
- service creation events
- privilege escalation events

Windows logs are forwarded using:

- Wazuh agent
- Winlogbeat
- Splunk Universal Forwarder (optional depending on design)

These logs are sent to the SOC network for analysis.

---

## Linux Systems

Linux systems generate logs such as:

- authentication logs
- sudo usage
- SSH activity
- service activity
- web server access logs
- application logs

Linux logs are forwarded using:

- Wazuh agent
- Filebeat
- Syslog

---

# Firewall Log Collection

pfSense generates logs related to:

- inbound connections
- outbound connections
- blocked traffic
- NAT translations
- suspicious scans
- interface events

pfSense can forward logs using Syslog to:

- Splunk
- Elastic Stack
- Security Onion

This provides visibility into perimeter activity.

---

# Network Traffic Monitoring

Security Onion receives mirrored traffic from the switch using **SPAN / port mirroring**.

Traffic is analyzed using:

- Suricata → signature-based detection
- Zeek → network metadata and protocol analysis

Security Onion generates:

- alerts
- connection metadata
- protocol events
- DNS logs
- HTTP logs
- SSL/TLS metadata

These logs can also be forwarded into Splunk or Elastic for correlation.

---

# Example Log Flow

## Example 1 — Windows Failed Login

1. User or attacker attempts login to a Windows workstation
2. Windows generates Event ID 4625
3. Wazuh agent or Winlogbeat forwards the event
4. Event is received by Splunk / Elastic
5. Detection rule identifies repeated failures
6. Alert is created for SOC review

---

## Example 2 — Port Scan from Kali

1. Kali runs Nmap against internal target
2. Traffic passes through firewall and switch
3. SPAN mirrors traffic to Security Onion
4. Suricata detects scan behavior
5. Zeek records connection metadata
6. Alert is forwarded to SOC dashboards

---

## Example 3 — Web Server Suspicious Requests

1. Attacker sends unusual requests to DMZ web server
2. Web server generates HTTP access logs
3. Filebeat or Syslog forwards logs
4. Elastic / Splunk indexes the logs
5. Analyst searches for unusual paths or status codes

---

# Log Forwarding Methods

The lab uses multiple forwarding methods depending on system type.

| Method | Used For |
|------|------|
| Wazuh agent | Windows and Linux host monitoring |
| Winlogbeat | Windows event forwarding |
| Filebeat | Linux and application logs |
| Syslog | firewall and Linux system logs |
| SPAN / mirrored traffic | Security Onion network analysis |

---

# Correlation Across Tools

The SOC environment benefits from combining host and network evidence.

Example:

- Wazuh shows failed login attempts on workstation
- Security Onion shows source IP performing scan
- pfSense logs show connection attempts from WAN
- Splunk correlates repeated events over time
- Elastic dashboards visualize attack timeline

This enables more complete investigations.

---

# Retention and Search

Logs stored in Splunk and Elastic can be used for:

- searching suspicious IP addresses
- identifying failed logon spikes
- reviewing firewall blocks
- investigating web server abuse
- hunting for lateral movement

---

# Security Benefits

A centralized logging architecture provides:

- improved visibility
- faster detection
- stronger investigations
- support for incident response
- validation of attack simulations

---

# Related Documentation

See also:

- `soc-architecture.md`
- `firewall-architecture.md`
- `../detections/windows-event-ids.md`
- `../detections/security-onion-alerts.md`
- `../detections/splunk-searches.md`
