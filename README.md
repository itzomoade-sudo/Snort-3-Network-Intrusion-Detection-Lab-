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


The lab uses Kali Linux as the monitoring system and Metasploitable 2 as the intentionally vulnerable lab target.
🎯 Objectives
Install and configure Snort 3.
Understand Snort configuration and rule structure.
Configure custom local detection rules.
Monitor traffic generated within a controlled lab.
Capture network traffic using tcpdump.
Analyze captured packets using Wireshark.
Validate Snort rules against a PCAP file.
Generate and investigate IDS alerts.
Document findings from a SOC analyst perspective.
🧰 Lab Environment
Component
Details
Monitoring OS
Kali Linux
IDS
Snort 3
Snort Version
3.12.2.0
Target VM
Metasploitable 2
Virtualization
VirtualBox
Packet Capture
tcpdump
Packet Analysis
Wireshark
Protocol Tested
ICMP
Kali IP
10.230.14.136
Metasploitable 2 IP
10.230.14.145
Snort Configuration
/etc/snort/snort.lua
Local Rules
/etc/snort/rules/local.rules
🏗️ Lab Architecture
┌──────────────────────┐
│      Kali Linux      │
│                      │
│    Snort 3 NIDS      │
│      Wireshark       │
│       tcpdump        │
│                      │
│   10.230.14.136      │
└──────────┬───────────┘
           │
           │ Lab Network
           │
           ▼
┌──────────────────────┐
│   Metasploitable 2   │
│                      │
│   10.230.14.145      │
└──────────────────────┘
⚙️ Snort Configuration
Snort uses:
/etc/snort/snort.lua
The custom rules are stored in:
/etc/snort/rules/local.rules
The local rules file contains the following detection rules:
alert icmp any any -> 10.230.14.145 any
(msg:"LAB - ICMP traffic to Metasploitable 2";
sid:10000001;
rev:3;)

alert tcp any any -> 10.230.14.145 any
(flags:S;
msg:"LAB - TCP SYN scan against Metasploitable 2";
sid:10000003;
rev:1;)

alert tcp any any -> 10.230.14.145 80
(msg:"LAB - HTTP traffic to Metasploitable 2";
sid:10000004;
rev:1;)
Rule 1 — ICMP Detection
Detects ICMP traffic directed toward the Metasploitable 2 VM.
SID: 10000001
Protocol: ICMP
Destination: 10.230.14.145
Rule 2 — TCP SYN Detection
Detects TCP SYN packets directed toward the lab target.
SID: 10000003
Protocol: TCP
Flag: SYN
Destination: 10.230.14.145
Rule 3 — HTTP Detection
Detects TCP traffic directed toward port 80 on the lab target.
SID: 10000004
Protocol: TCP
Destination Port: 80
Destination: 10.230.14.145
✅ Configuration Validation
Before running Snort, the configuration was validated using:
sudo snort -T -c /etc/snort/snort.lua -i eth0
The configuration successfully validated with:
Snort successfully validated the configuration (with 0 warnings).
The custom local rules were also successfully loaded.
<img width="1920" height="909" alt="Screenshot_2026-09-28_18_33_01" src="https://github.com/user-attachments/assets/f206d2c8-e3f5-459e-b198-a826d5ad8efd" />

🧪 ICMP Detection Test
A controlled ICMP test was performed against the Metasploitable 2 VM:
ping -c 4 10.230.14.145
The traffic was captured using:
sudo tcpdump -ni eth0 'host 10.230.14.145' \
-w ~/snort-lab-icmp.pcap
The resulting PCAP was:
~/snort-lab-icmp.pcap
<img width="1920" height="909" alt="Screenshot_2026-09-28_18_33_15" src="https://github.com/user-attachments/assets/c52c702c-96b1-40b5-ba8f-b5084988e863" />

📡 Packet Capture
The capture contained ICMP traffic between:
Source:      10.230.14.136
Destination: 10.230.14.145
Protocol:    ICMP
Example capture:
10.230.14.136 → 10.230.14.145
ICMP Echo Request
<img width="1920" height="909" alt="Screenshot_2026-09-28_18_36_01" src="https://github.com/user-attachments/assets/0f802730-30bf-4f68-9cf3-a5e8e544eea5" />

10.230.14.145 → 10.230.14.136
ICMP Echo Reply
The capture was later opened in Wireshark for investigation.
🔎 Wireshark Investigation
The PCAP was opened in Wireshark using the display filter:
icmp
The investigation confirmed:
ICMP Echo Requests were generated.
ICMP Echo Replies were received.
The source IP was 10.230.14.136.
The destination IP was 10.230.14.145.
No packet loss was observed during the test.
The traffic corresponded to the controlled lab activity.
<img width="1920" height="909" alt="Screenshot_2026-09-28_18_32_18" src="https://github.com/user-attachments/assets/406ba709-8624-450f-ac91-1ea00f182933" />

🚨 Snort Rule Testing
The local rule was tested directly against the captured PCAP.
Command:
sudo snort -q \
-R /etc/snort/rules/local.rules \
-r ~/snort-lab-icmp.pcap \
-A alert_talos
Snort produced:
##### snort-lab-icmp.pcap #####
        [1:10000001:3] LAB - ICMP traffic to Metasploitable 2 (alerts: 4)
#####
This confirms that the custom ICMP rule successfully matched 4 packets in the captured traffic.
🧠 SOC Analysis
Detection
The custom Snort rule detected ICMP traffic destined for the Metasploitable 2 VM.
Rule Match
SID: 10000001
Message: LAB - ICMP traffic to Metasploitable 2
Alerts: 4
Source
10.230.14.136
Destination
10.230.14.145
Protocol
ICMP
Classification
True Positive — for the detection rule.
The packets genuinely matched the conditions defined by the Snort rule.
However, the activity itself was authorized and intentionally generated as part of this lab, so it should not be treated as a real-world malicious incident.
🧪 Live Capture Troubleshooting
During the lab, tcpdump successfully observed the ICMP traffic on eth0.
However, the same traffic did not produce visible alerts during the initial live Snort test.
To isolate the issue, the saved PCAP was replayed directly through the Snort rule.
The replay generated:
alerts: 4
This demonstrated that:
PCAP
 ↓
Snort Rule
 ↓
Detection
was functioning correctly.
The remaining troubleshooting area is the live packet-capture path between the network interface and Snort.
This distinction is important because a SOC analyst should avoid incorrectly concluding that a detection rule is broken before testing the rule independently.
🛠️ Troubleshooting Process
The following troubleshooting steps were performed:
1. Validate Snort configuration
sudo snort -T -c /etc/snort/snort.lua -i eth0
Result:
0 warnings
2. Verify network traffic
ping -c 4 10.230.14.145
Result:
4 packets transmitted
4 packets received
0% packet loss
3. Capture traffic
sudo tcpdump -ni eth0 'host 10.230.14.145' \
-w ~/snort-lab-icmp.pcap
4. Analyze the PCAP
Wireshark filter:
icmp
5. Test Snort against the PCAP
sudo snort -q \
-R /etc/snort/rules/local.rules \
-r ~/snort-lab-icmp.pcap \
-A alert_talos
Result:
LAB - ICMP traffic to Metasploitable 2
alerts: 4
6. Check available DAQ modules
sudo snort --daq-list
The system reported:
afpacket(v7)
pcap(v4)
nfq(v8)
...
This confirmed that Snort had live packet acquisition modules available.
📊 Findings
Investigation Item
Result
Snort configuration
Valid
Local rules loaded
Yes
Metasploitable connectivity
Successful
ICMP traffic captured
Yes
PCAP created
Yes
Wireshark analysis
Successful
ICMP rule matched PCAP
Yes
Alerts generated from PCAP
4
Live Snort alert display
Requires further troubleshooting
🔐 Security Lessons Learned
This lab demonstrated several practical SOC concepts:
1. Detection rules must be tested
A rule should not be considered broken simply because a live alert isn't displayed.
Testing the rule against a known-good PCAP helped isolate the problem.
2. Packet capture is valuable for investigation
A PCAP provides evidence that can be analyzed independently of the IDS.
3. Detection and maliciousness are different concepts
A Snort alert means that traffic matched a rule.
It does not automatically mean the activity is malicious.
Analysts must investigate the surrounding context.
4. Network visibility matters
An IDS can only detect traffic that reaches the interface it is monitoring.
5. SOC investigations require multiple tools
This lab combined:
Snort
  +
tcpdump
  +
Wireshark
  +
PCAP
to investigate the same network event.
📸 Evidence
1. Network Configuration
ip -br addr
<img width="1920" height="909" alt="Screenshot_2026-09-28_18_51_13" src="https://github.com/user-attachments/assets/ae661bf8-a988-421c-b619-2ce0926a7933" />

2. Snort Configuration Validation
sudo snort -T -c /etc/snort/snort.lua -i eth0
<img width="1920" height="909" alt="Screenshot_2026-09-28_18_33_01" src="https://github.com/user-attachments/assets/e2e70f7b-3a21-42bc-a45f-2f3f95600d7f" />

3. Local Rules
cat /etc/snort/rules/local.rules
<img width="1920" height="909" alt="Screenshot_2026-09-28_18_36_01" src="https://github.com/user-attachments/assets/e343b7ff-ffa3-44b4-951f-b88e26361cfd" />

4. ICMP Traffic
ping -c 4 10.230.14.145
5. tcpdump Capture
sudo tcpdump -ni eth0 'host 10.230.14.145'
6. Snort PCAP Detection
sudo snort -q \
-R /etc/snort/rules/local.rules \
-r ~/snort-lab-icmp.pcap \
-A alert_talos
7. Wireshark ICMP Analysis
Filter:
icmp
📁 Project Structure
Recommended GitHub structure:
snort-3-nids-lab/
│
├── README.md
│
├── rules/
│   └── local.rules
│
├── captures/
│   └── snort-lab-icmp.pcap
│
├── screenshots/
│   ├── 01-network-config.png
│   ├── 02-snort-validation.png
│   ├── 03-local-rules.png
│   ├── 04-ping-test.png
│   ├── 05-tcpdump.png
│   ├── 06-snort-alert.png
│   └── 07-wireshark-analysis.png
│
└── reports/
    └── investigation-report.md
Note: Avoid committing large PCAP files or sensitive captures to a public repository. Use a sanitized/small lab capture or document how the capture was generated.
⚠️ Ethical and Safety Notice
This project was performed in an authorized virtual laboratory using Kali Linux and Metasploitable 2.
The techniques demonstrated here are intended for:
cybersecurity education
defensive security research
IDS rule development
SOC analyst training
authorized laboratory environments
Do not monitor or analyze networks without authorization.
🚀 Future Improvements
Planned improvements include:
Complete live Snort alert troubleshooting.
Configure persistent alert_fast logging.
Add additional TCP detection rules.
Add DNS monitoring rules.
Investigate HTTP traffic.
Perform controlled port-scan detection in the isolated lab.
Create a structured SOC incident report.
Add automated log analysis.
Explore Snort inline IPS mode in a separate isolated topology.
📚 Technologies Used
Kali Linux
Snort 3
Metasploitable 2
Wireshark
tcpdump
VirtualBox
PCAP analysis
Custom Snort rules
Network intrusion detection
👨‍💻 Author
Yusuf Tajudeen
Cybersecurity Student | SOC Analyst in Training | AWS Certified Cloud Practitioner
Areas of interest:
Cybersecurity
SOC Operations
Cloud Security
AWS
Network Security
Linux
Threat Detection
