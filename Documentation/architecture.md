
# Network Traffic Investigation Architecture

```mermaid
flowchart TD
    A["Windows Workstation<br/>10.1.17.215"]
    B["HTTP File Server<br/>5.252.153.241"]
    C["TLS Endpoint<br/>45.125.66.32:2917"]
    D["PCAP Evidence<br/>39,427 Packets"]
    E["Wireshark and TShark<br/>Offline Analysis"]

    A -->|"HTTP file requests"| B
    A -->|"TLS 1.2 connections"| C
    A -.->|"Captured network traffic"| D
    D -->|"Forensic examination"| E
```

**Note:** This diagram illustrates observed communications from a training PCAP. The suspected malicious activity has not been independently confirmed through endpoint forensics.

