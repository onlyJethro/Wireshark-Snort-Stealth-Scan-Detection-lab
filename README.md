# Wireshark-Snort-Stealth-Scan-Detection-lab
# Intrusion Detection & Packet-Level Analysis of TCP SYN Stealth Scanning

## Project Overview

This project demonstrates the complete cybersecurity workflow of simulating an attack, performing packet-level traffic analysis, engineering a custom Intrusion Detection System (IDS) rule, and validating the detection mechanism using real network artifacts.

Using Nmap, Wireshark, and Snort 3, I simulated a TCP SYN stealth scan against an authorized target and developed a custom IDS rule capable of detecting reconnaissance activity based on TCP SYN packets.

This lab showcases both offensive and defensive cybersecurity skills commonly used by Security Operations Center (SOC) Analysts and Detection Engineers.

---

## Technologies Used

* Kali Linux
* Nmap
* Wireshark
* Snort 3
* TCP/IP Networking
* Signature-Based Intrusion Detection
* Oracle Virtualbox

---

## Skills Demonstrated

* Network Traffic Analysis
* Packet-Level Investigation
* Intrusion Detection
* Detection Engineering
* TCP/IP Fundamentals
* Security Monitoring
* Threat Detection
* Signature Validation
* Incident Documentation
* Reconnaissance Detection Techniques

---

## Lab Objective

The objective of this lab was to:

* Simulate a TCP SYN stealth scan.
* Capture and analyse the generated network traffic.
* Understand how TCP handshakes are manipulated during reconnaissance.
* Develop a custom Snort 3 detection rule.
* Validate IDS alerts using captured PCAP files.
* Document findings and provide mitigation recommendations.

---

## Attack Simulation

A TCP SYN stealth scan was performed using Nmap against an authorised target.

Command used:

sudo nmap -sS -p 22,80 scanme.nmap.org

The scan was configured to probe:

* Port 22 (SSH)
* Port 80 (HTTP)

The SYN scan was selected because it performs reconnaissance without completing the TCP three-way handshake, making it a common stealth scanning technique.

Screenshot:

screenshots/nmap_stealth_scan.png

---

## Packet-Level Analysis Using Wireshark

During the scan, network traffic was captured using Wireshark.

The packet capture revealed the complete behaviour of a TCP SYN stealth scan.

### SYN Packet

The attacking machine sent a SYN packet to determine whether the target port was open.

Observed Behaviour:

* Source IP: 10.0.2.15
* Destination IP: 45.33.32.156

Purpose:

* Initiates communication with the target.

Screenshot:

screenshots/wireshark_syn_packet.png

---

### SYN-ACK Packet

The target responded with a SYN-ACK packet, indicating that the requested port was open and available.

Purpose:

* Confirms that the target is listening on the requested port.

Screenshot:

screenshots/wireshark_syn_ack_packet.png

---

### RST Packet

Instead of completing the TCP three-way handshake, the attacking machine immediately transmitted a TCP Reset (RST) packet.

Purpose:

* Abruptly terminates the connection.
* Prevents a full TCP session from being established.
* Enables stealth reconnaissance behaviour.

Screenshot:

screenshots/wireshark_rst_packet.png

---

## Understanding the TCP Handshake

Normal TCP Connection:

SYN → SYN-ACK → ACK

TCP SYN Stealth Scan:

SYN → SYN-ACK → RST

By terminating the connection immediately after receiving the SYN-ACK response, the attacker can determine whether a port is open while minimising connection establishment.

---

## Detection Engineering

To detect this activity, a custom Snort 3 IDS rule was developed.

Snort Rule:

alert tcp any any -> any any (msg:"NMAP TCP Stealth Scan Detected"; flags:S; sid:1000001; rev:1;)

Rule Breakdown:

* alert = Generate an alert when a match occurs.
* tcp = Inspect TCP traffic.
* any any -> any any = Monitor all source and destination traffic.
* flags:S = Match packets containing only the SYN flag.
* sid = Unique Signature ID.
* rev = Rule revision number.

Screenshot:

screenshots/snort_rule.png

---

## Signature Validation

The custom rule was validated against the captured PCAP file.

Command Used:

sudo snort -r stealth_syn_scan.pcapng -R local.rules -A alert_fast

Snort successfully generated alerts whenever the SYN packets generated during the Nmap stealth scan were encountered.

The IDS generated multiple alerts corresponding to the SYN probes observed during the attack simulation.

Screenshot:

screenshots/snort_alerts.png

---

## Findings

The investigation revealed:

* TCP SYN packets were used to probe the target.
* Open ports returned SYN-ACK responses.
* Connections were immediately terminated using RST packets.
* The custom Snort 3 rule successfully detected reconnaissance behaviour.
* Snort analysed all captured packets and generated accurate alerts.
* Signature-based detection can effectively identify TCP SYN stealth scanning activity.

---

## Mitigation Recommendations

Organisations can mitigate TCP SYN reconnaissance activity by:

* Deploying IDS/IPS solutions such as Snort or Suricata.
* Monitoring for abnormal SYN packet activity.
* Implementing network segmentation.
* Restricting unnecessary exposed services.
* Monitoring port-scanning behaviour across network devices.
* Configuring alert thresholds for excessive SYN requests.

---

## Key Takeaways

This project demonstrates how offensive reconnaissance techniques can be analysed and detected using packet-level traffic analysis and signature-based intrusion detection.

By combining Nmap, Wireshark, and Snort 3, I successfully simulated an attack, analysed the resulting network traffic, developed a custom IDS rule, and validated the detection mechanism against real packet captures.

The skills demonstrated in this project align closely with the responsibilities of SOC Analysts, Threat Hunters, and Detection Engineers.
