# Snort-3-Network-Intrusion-Detection-Lab
# Snort 3 Network Intrusion Detection Lab

A hands-on cybersecurity lab demonstrating how to deploy and configure **Snort 3** as a Network Intrusion Detection System (NIDS), generate controlled network traffic against a **Metasploitable 2** virtual machine, create custom detection rules, and investigate captured traffic using **Wireshark**.

> **Lab purpose:** Detection and investigation in an isolated, authorized VirtualBox environment.

---

## 📌 Project Overview

This project demonstrates a basic SOC-style network monitoring workflow:

```text
Network Traffic
       ↓
   Snort 3 IDS
       ↓
 Custom Detection Rule
       ↓
   Alert Generated
       ↓
 SOC Analyst Investigation
       ↓
 Wireshark / PCAP Analysis
       ↓
 Identify:
 ├── Source IP
 ├── Destination IP
 ├── Protocol
 ├── Ports
 └── Packet details
       ↓
Determine:
True Positive / False Positive