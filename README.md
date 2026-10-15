# Network Traffic & Incident PCAP Analysis Lab

## Overview

A hands-on cybersecurity investigation project focused on network reconnaissance, packet analysis, suspicious traffic detection, and SOC incident reporting using Nmap and Wireshark.

The project analyzes a publicly available training PCAP to investigate suspicious NetSupport remote-access communication and document evidence using a structured SOC investigation workflow.

## Tools & Technologies

- Wireshark — Packet capture and network traffic analysis
- Nmap — Network reconnaissance and service detection
- TCP/IP, DNS, DHCP, HTTP — Network protocol analysis
- MITRE ATT&CK — Threat behavior mapping
- CSV and Markdown — IOC documentation and incident reporting

## Investigation Methodology

1. Performed authorized localhost port scanning using Nmap.
2. Captured and inspected DNS traffic to establish a network baseline.
3. Analyzed a suspicious network traffic PCAP.
4. Identified the affected Windows endpoint using DHCP evidence.
5. Investigated HTTP POST requests and NetSupport protocol indicators.
6. Examined HTTP streams and periodic communication patterns.
7. Reviewed network conversations and extracted investigation indicators.
8. Documented findings, ATT&CK mappings, and response recommendations.

## Key Findings

| Finding | Evidence |
|---|---|
| Affected hostname | DESKTOP-TEYQ2NR |
| Affected IP | 10.2.28.88 |
| Suspicious external IP | 45.131.214.85 |
| HTTP destination | http://45.131.214.85/fakeurl.htm |
| User-Agent | NetSupport Manager/1.3 |
| Server header | NetSupport Gateway/1.92 (Windows NT) |
| Observed behavior | Repeated HTTP POST communication |
| Investigation assessment | Suspicious remote-access activity requiring further validation |

The network evidence demonstrates NetSupport communication but does not independently establish unauthorized installation, malware execution, or data exfiltration.

## Evidence

Investigation screenshots are available in the `screenshots/` directory.

The investigation report is available at:

[**SOC Incident Investigation Report**](reports/SOC_Incident_Investigation_Report.md)

Documented indicators are available at:

[**IOC List**](iocs/indicators.csv)

## MITRE ATT&CK

- **T1219 — Remote Access Software:** Potential misuse of remote-access software.
- **T1071.001 — Application Layer Protocol: Web Protocols:** HTTP-based communication consistent with potential command-and-control activity.

Mappings are investigative hypotheses rather than confirmed adversary attribution.

## Skills Demonstrated

Network Reconnaissance, Nmap, Wireshark, PCAP Analysis, DNS Analysis, DHCP Analysis, HTTP Traffic Investigation, Network IOC Extraction, Incident Timeline Reconstruction, MITRE ATT&CK Mapping, SOC Incident Reporting.

## Dataset

Training PCAP sourced from Malware Traffic Analysis:

https://malware-traffic-analysis.net/2026/02/28/index.html

The dataset is used for educational analysis. Raw packet captures are not included in this repository.

## Disclaimer

This project was completed in an authorized training environment for educational and defensive security purposes. Findings reflect the available packet capture and do not represent a confirmed live enterprise security incident.