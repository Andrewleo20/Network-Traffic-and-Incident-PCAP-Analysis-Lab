# SOC Incident Investigation Report

**Case ID:** SOC-LAB-001  
**Incident Title:** Suspicious NetSupport Remote-Access Communication  
**Investigation Type:** Network Traffic / PCAP Analysis  
**Evidence Source:** `2026-02-28-traffic-analysis-exercise.pcap`  
**Analysis Tools:** Wireshark, Nmap  
**Investigation Status:** Analysis completed for identified NetSupport traffic; broader compromise scope not established  
**Assessment:** Suspicious remote-access communication requiring further investigation

---

## 1. Executive Summary

A network traffic investigation identified repeated HTTP POST requests originating from an internal Windows endpoint, `DESKTOP-TEYQ2NR` (`10.2.28.88`), to an external IP address, `45.131.214.85`.

Analysis of the HTTP conversation revealed NetSupport Manager and NetSupport Gateway protocol indicators, including `CMD=POLL` and `CMD=ENCD`. The endpoint communicated with the external destination over TCP port 443 using plaintext HTTP.

The observed behavior is consistent with potential remote-access or command-and-control communication. However, the available network evidence does not independently establish whether the remote-access activity was authorized, whether malware was installed, or whether sensitive data was exfiltrated.

## 2. Affected Asset

| Field | Value |
|---|---|
| Hostname | DESKTOP-TEYQ2NR |
| Internal IP | 10.2.28.88 |
| MAC Address | 00:19:d1:b2:4d:ad |
| Internal DNS Server | 10.2.28.2 |
| Internal Domain | easyas123.tech |
| Suspicious External IP | 45.131.214.85 |
| Destination Port | TCP 443 |

The hostname and IP association was established through DHCP Request and ACK traffic.

## 3. Technical Findings

### Finding 1: Endpoint Identification

DHCP traffic identified `DESKTOP-TEYQ2NR` as the hostname requesting `10.2.28.88`. A DHCP ACK confirmed assignment of that IP address.

**Evidence:** DHCP Frame 2 and corresponding ACK.

### Finding 2: Suspicious HTTP Communication

The endpoint repeatedly contacted:

`http://45.131.214.85/fakeurl.htm`

Observed characteristics:

- HTTP method: POST
- Destination port: TCP 443
- User-Agent: NetSupport Manager/1.3
- Server header: NetSupport Gateway/1.92 (Windows NT)
- HTTP responses: 200 OK
- Protocol indicators: CMD=POLL and CMD=ENCD

Plaintext HTTP was observed on a port commonly associated with HTTPS.

**Evidence:** Frame 2638 and TCP Stream 40.

### Finding 3: Periodic Communication

The investigation identified 264 HTTP POST requests to the suspicious destination.

- First observed POST: 45.048059 seconds
- Last observed POST: 1582.044303 seconds
- Observed duration: approximately 25 minutes and 37 seconds

Many subsequent requests occurred approximately 60 seconds apart, consistent with periodic polling behavior.

These times are relative to the beginning of the packet capture.

### Finding 4: Network Conversation Analysis

The endpoint participated in 101 IPv4 conversations.

Communication with `45.131.214.85` accounted for approximately 550 packets and 96 kB of observed network traffic.

Other external connections were identified but were not classified as malicious without further supporting evidence.

### Finding 5: DNS Analysis

The visible DNS queries included Windows connectivity, telemetry, proxy discovery, and Active Directory-related requests.

No clearly malicious domain was identified among the inspected DNS queries.

The suspicious NetSupport communication used a direct IP address rather than requiring an observed DNS lookup.

## 4. Incident Timeline

| Capture-relative time | Event |
|---|---|
| Initial capture | DHCP and internal network activity observed |
| 45.048059 s | First identified HTTP POST to 45.131.214.85 |
| Approximately 45–46 s | Initial NetSupport protocol exchanges |
| 105.932070 s | Subsequent HTTP POST |
| 166.005869 s | Continued periodic communication |
| 1582.044303 s | Last observed HTTP POST |

The observed timeline does not establish the initial infection or execution time.

## 5. Indicators of Compromise and Investigation Indicators

| Indicator | Value |
|---|---|
| Suspicious external IP | 45.131.214.85 |
| Suspicious URL | http://45.131.214.85/fakeurl.htm |
| User-Agent | NetSupport Manager/1.3 |
| Server header | NetSupport Gateway/1.92 (Windows NT) |
| Protocol markers | CMD=POLL, CMD=ENCD |
| Affected hostname | DESKTOP-TEYQ2NR |
| Affected internal IP | 10.2.28.88 |

Full indicator details are available in `iocs/indicators.csv`.

## 6. MITRE ATT&CK Mapping

**T1219 — Remote Access Software**

The observed NetSupport communication is consistent with remote-access software use. Its classification as adversary activity depends on establishing that the software was unauthorized or maliciously deployed.

**T1071.001 — Application Layer Protocol: Web Protocols**

Repeated HTTP POST communication with the external gateway is consistent with web-protocol-based communications. Adversary command-and-control intent remains suspected rather than confirmed.

These mappings represent hypotheses supported by observed behavior, not proof of an attacker-controlled operation.

## 7. Recommended Response Actions

1. Verify whether NetSupport Manager is approved and authorized on the affected endpoint.
2. If unauthorized activity is suspected, isolate the endpoint using approved incident-response procedures.
3. Investigate endpoint processes, executable paths, persistence mechanisms, and file hashes.
4. Review EDR, Windows Event Logs, Sysmon, and proxy/firewall telemetry for related activity.
5. Search other endpoints for connections to the suspicious IP and URL.
6. Preserve relevant forensic evidence before remediation.
7. Block confirmed unauthorized destinations according to organizational policy.
8. Assess potential data exposure using additional endpoint and network evidence.

## 8. Investigation Limitations

- Analysis was performed on a supplied training PCAP rather than a live enterprise incident.
- Endpoint process execution and persistence were not independently verified.
- Authorization status of the remote-access software is unknown.
- Encoded application data was not fully decoded.
- Data exfiltration was not established.
- Additional external connections require investigation before classification.

## 9. Conclusion

The investigation identified an internal Windows endpoint communicating repeatedly with an external NetSupport gateway through HTTP POST requests.

DHCP evidence linked the endpoint hostname to the source IP, while HTTP stream analysis established NetSupport protocol characteristics and periodic communication behavior.

The findings justify further endpoint investigation and validation of remote-access authorization.

**Final assessment:** Suspicious NetSupport remote-access communication identified; malicious deployment and full compromise scope remain unconfirmed.