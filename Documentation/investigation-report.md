# Network Traffic Forensics Investigation Report

**Project:** Network Traffic Investigation and Threat Analysis  
**Analyst:** Cybersecurity Portfolio Project  
**Tools:** Wireshark, TShark, Ubuntu Server, VirtualBox  
**Evidence Source:** Malware-Traffic-Analysis.net — January 22, 2025 Exercise  
**Capture Date:** January 22, 2025  
**Investigation Type:** Offline PCAP Analysis

---

## 1. Executive Summary

This investigation analyzed a network packet capture containing 39,427 packets to identify potentially malicious network activity.

The analysis identified suspicious communications involving a Windows workstation (`10.1.17.215`) and external infrastructure.

Key findings included:

- HTTP requests for PowerShell scripts and TeamViewer-related resources.
- Successful HTTP responses associated with suspicious file requests.
- Repeated communication with an external server (`5.252.153.241`).
- Network messages reporting PowerShell execution and startup shortcut creation.
- Multiple encrypted TLS connections to `45.125.66.32` over TCP port 2917.
- A self-signed TLS certificate associated with the second external server.

The evidence is consistent with possible malicious script delivery, persistence, and remote-access activity. However, successful execution, persistence, and attacker control cannot be conclusively established from network evidence alone.

## 2. Investigation Objectives

The investigation aimed to:

1. Identify the primary workstation involved in suspicious communications.
2. Analyze network protocols and DNS activity.
3. Identify suspicious external connections.
4. Examine HTTP requests and responses for evidence of file delivery.
5. Reconstruct an evidence-based incident timeline.
6. Identify potential indicators of compromise.
7. Recommend defensive security measures.

## 3. Investigation Environment

The investigation was conducted in an isolated virtual laboratory using Ubuntu Server and VirtualBox.

**Tools used:**
- Wireshark for packet inspection.
- TShark for command-line traffic analysis.
- Ubuntu Server for evidence processing.
- SSH for remote laboratory administration.

The supplied PCAP was analyzed offline without executing any captured files.

## 4. Evidence Overview

| Property | Value |
|---|---|
| Evidence filename | 2025-01-22-traffic-analysis-exercise.pcap |
| Capture date | January 22, 2025 |
| Total packets | 39,427 |
| Capture duration | Approximately 5 minutes 22 seconds |
| Capture size | Approximately 26 MB |
| Link-layer format | Ethernet |
| Primary investigated workstation | 10.1.17.215 |

The original PCAP SHA-256 hash is recorded separately in `documentation/evidence-hash.txt`.

## 5. Network Traffic Analysis

Protocol hierarchy analysis identified TCP, UDP, DNS, HTTP, TLS, SMB, LDAP, and Kerberos traffic.

The presence of Windows-related protocols suggests that the workstation operated within a Windows network environment.

IPv4 endpoint analysis identified `10.1.17.215` as the dominant internal endpoint, appearing in 39,045 packets.

Two external addresses were prioritized for investigation:

- `5.252.153.241` — suspicious HTTP file requests and responses.
- `45.125.66.32` — repeated encrypted TLS communications over TCP port 2917.

## 6. Suspicious HTTP Activity

The workstation contacted `5.252.153.241` and requested several resources.

| Request packet | Requested resource | HTTP response |
|---|---|---|
| 5031 | /api/file/get-file/264872 | 200 OK |
| 5063 | /api/file/get-file/29842.ps1 | 200 OK |
| 8002 | /api/file/get-file/TeamViewer | 200 OK |
| 12890 | /api/file/get-file/Teamviewer_Resource_fr | 200 OK |
| 13643 | /api/file/get-file/TV | 200 OK |
| 13671 | /api/file/get-file/pas.ps1 | 200 OK |

The requests included PowerShell scripts and remote-access-related filenames.

Successful HTTP responses support that the server provided resources associated with these requests. The complete contents and subsequent execution of the files were not independently verified.

## 7. Incident Timeline

**All timestamps are in UTC on January 22, 2025.**

| Time | Packet | Observation |
|---|---|---|
| 19:45:56 | 5031 | Request for an unidentified resource |
| 19:45:58 | 5063 | Request for 29842.ps1 |
| 19:47:01 | 8002 | Request for TeamViewer-related resource |
| 19:47:04 | 12890 | Request for Teamviewer_Resource_fr |
| 19:47:05 | 13671 | Request for pas.ps1 |
| 19:47:05 | 13677 | Message reporting startup shortcut creation |
| 19:59:38 | 19292 | Message reporting PowerShell process started |
| 20:25:09 | 28335 | Additional PowerShell status message |
| 20:27:54 | 33356 | Additional PowerShell status message |

The timeline suggests possible staged file delivery followed by persistence-related and continued script activity.

The status messages are network observations and do not independently prove that the reported actions occurred.

## 8. Encrypted TLS Communication

Additional analysis identified nine TCP conversations between `10.1.17.215` and `45.125.66.32` using TCP port 2917.

The traffic included TLS 1.2 handshakes and certificate messages.

Certificate inspection revealed:

- Common Name: `45.125.66.32: Self-signed certificate`
- Issuer: Self-signed certificate
- Subject: Matching self-signed certificate identity
- Destination port: 2917

The combination of repeated encrypted communication, a nonstandard TLS port, and a self-signed certificate warrants further investigation.

These characteristics are not independently sufficient to establish command-and-control activity.

## 9. Potential Indicators of Compromise

| Indicator | Type | Assessment |
|---|---|---|
| 10.1.17.215 | Internal IP | Investigated workstation |
| 5.252.153.241 | External IP | Suspicious file-delivery infrastructure |
| 45.125.66.32 | External IP | Suspicious encrypted communication endpoint |
| 29842.ps1 | Filename | Potentially suspicious PowerShell script |
| pas.ps1 | Filename | Potentially suspicious PowerShell script |
| TCP 2917 | Network port | Unusual TLS communication observed |
| Startup shortcut status message | Network artifact | Possible persistence indicator |

These are investigation-specific indicators. They should not automatically be treated as universally malicious without additional validation.

## 10. Limitations

This investigation relied on a network packet capture rather than direct access to the investigated Windows workstation.

Consequently, the following could not be conclusively verified:

- Whether downloaded scripts executed successfully.
- Whether the requested files were malicious.
- Whether persistence was established.
- Whether unauthorized remote access occurred.
- Whether sensitive information was exfiltrated.
- Whether the encrypted TLS sessions carried malicious commands.

Additional endpoint logs, process execution records, file hashes, and host forensic artifacts would be required.

## 11. Security Recommendations

1. Isolate the affected workstation during an active incident.
2. Investigate the suspicious external IP addresses and associated network connections.
3. Review PowerShell execution logs and Windows security events.
4. Examine startup folders and scheduled tasks for unauthorized persistence.
5. Review remote-access software installations and execution history.
6. Apply endpoint detection and response monitoring.
7. Implement appropriate network segmentation and outbound traffic monitoring.
8. Preserve forensic evidence and maintain an incident response timeline.

## 12. Conclusion

The network investigation identified a sequence of suspicious HTTP file requests, successful server responses, persistence-related status messages, and repeated encrypted communications.

These observations support a hypothesis of potentially malicious activity involving script delivery and remote-access mechanisms.

The investigation demonstrates the use of Wireshark and TShark for protocol analysis, network forensics, incident reconstruction, and evidence-based security reporting.

**Overall assessment:** Suspicious activity identified; full host compromise not conclusively verified.

---

**End of Report**
