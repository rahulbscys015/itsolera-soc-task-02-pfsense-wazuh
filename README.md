# ITSOLERA SOC Internship — Task 02: pfSense Firewall Integration with Wazuh

**Programme:** ITSOLERA Cybersecurity (SOC) Winter Internship  
**Task:** 02 — Firewall Integration, Rule Configuration & Monitoring  
**Prepared by:** Rahul Kumar  
**Organization:** IT Solera Pvt. Ltd.  
**Date:** 3 February 2026  

## Objective

Implement a software firewall, connect it to Wazuh, create and test security rules in a home lab, monitor the resulting files and logs, and produce an analytical traffic report.

## Lab environment

| Component | Specification |
|---|---|
| Firewall | pfSense 2.7.0 virtual machine |
| SIEM | Wazuh 4.7.3 on Kali Linux 2024.1 |
| pfSense LAN | `192.168.40.100/24` |
| Wazuh dashboard | `https://192.168.93.142` |
| Network mode | Bridged home lab |
| Test clients | Windows 11, Ubuntu 22.04 |

## Implementation

### 1. Firewall deployment

- Downloaded pfSense and created a VM with separate WAN and LAN adapters.
- Installed pfSense with default settings.
- Configured the LAN interface and enabled DHCP for lab clients.

### 2. Wazuh deployment

- Updated the Kali Linux VM.
- Installed the Wazuh manager/indexer components with the official installer.
- Confirmed dashboard access on `192.168.93.142`.

### 3. pfSense → Wazuh log forwarding

- Set the pfSense firewall log format to **Legacy**.
- Installed the `syslog-ng` package.
- Configured remote logging to `192.168.93.142:514/UDP`.

### 4. Firewall rules

| Rule | Purpose | Result |
|---|---|---|
| `GeoBlock_Countries` | Block traffic from China and Russia | Effective, about 98% success |
| `Blocked_Sites` | Restrict access to selected websites such as YouTube | Fully functional |
| `Admin_IPs` | Limit administrative access to trusted source addresses | Working as intended |

### 5. Monitoring and reporting

- Searched Wazuh Security Events with `data.srcip:"GeoIP" OR rule.groups:"firewall"`.
- Built views for top blocked countries, most blocked websites and failed admin attempts.
- Generated a seven-day report covering firewall activity, GeoIP blocking, website restrictions and administrative access.

## Alerts analysed

| Alert | Time | Rule | Severity | Meaning |
|---|---|---|---|---|
| Syslog service stopped | 1 Feb 2026, 14:52:56 EST at SIEM | `1004` — Syslogd exiting | Level 5 | Log forwarding from `192.168.40.100` stopped |
| Syslog service restarted | 1 Feb 2026, 13:30:51 EST at SIEM | `1005` — Syslogd restarted | Level 5 | Log forwarding restored |

The stop/restart sequence highlighted that loss of syslog availability creates a visibility gap even when firewall rules themselves are working.

## Key findings

- GeoIP, website and administrator rules all functioned in the lab.
- About 2% of blocked events were false positives, mainly legitimate VPN users.
- Trusted foreign IPs should be whitelisted.
- GeoIP lists should be updated weekly, and critical firewall events should trigger email or SMS alerts.
- Wazuh agents should be deployed to all internal systems for fuller coverage.

## Repository contents

- `Rahul_Kumar_Task_02_Report.pdf` and `.docx` — full implementation and analysis report.
- `Rahul_Kumar_Task_02_Brief.pdf` — original SOC team task brief.
- `evidence/screenshots/` — 38 screenshots of the pfSense rules, Wazuh configuration and dashboard results.
