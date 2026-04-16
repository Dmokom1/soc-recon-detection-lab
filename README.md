# Phase 1: Reconnaissance Detection and Custom SIEM Rule Validation in Security Onion

## Overview

In this phase of my home SOC lab, I simulated reconnaissance from a Kali Linux attacker VM against an internal Active Directory environment and validated that Security Onion could detect the activity. I then pivoted from the generated alerts to identify the attacker source IP, built a custom threshold detection rule in Elastic, and confirmed that the rule fired successfully when the reconnaissance was repeated against the domain controller.

This phase focuses on alert triage, attacker identification, and basic detection engineering.

---

## Objective

The goal of this phase was to:

- Perform internal reconnaissance using Nmap
- Identify the domain controller from scan results
- Review alerts generated in Security Onion
- Pivot to identify the attacker source IP
- Create a custom detection rule in Elastic
- Validate detection through repeated attack simulation

---

## Lab Environment

### Systems
- Kali Linux (attacker)
- Windows Server 2022 (Domain Controller)
- Security Onion 2.4.211

### Network
- Host-only network: 192.168.30.0/24
- Management network: 192.168.9.0/24

### Key Hosts
- Kali: 192.168.30.136
- Domain Controller: 192.168.30.128
- Security Onion: 192.168.9.131

---

## Tools Used

- Nmap
- Security Onion
- Suricata
- Elastic / Kibana
- Windows Server 2022

---

## Attack Simulation

### 1. Nmap Scan

Command used:

nmap -A 192.168.30.128

This revealed:
- Kerberos (88)
- LDAP (389)
- Global Catalog (3268)

Confirmed target as domain controller.

---

### 2. Alert Detection in Security Onion

Observed alerts:
- ET SCAN Nmap Scripting Engine User-Agent Detected
- ET SCAN Possible Nmap User-Agent Observed
- ET SCAN NMAP OS Detection Probe

---

### 3. Source Identification

From alert drilldown:
- source.ip: 192.168.30.136 (Kali)
- destination.ip: 192.168.30.128 (DC)

---

## Detection Engineering

### 4. Kibana Query

source.ip:192.168.30.136 AND event.dataset.keyword:suricata.alert

---

### 5. Custom Rule

- Name: Kali Suricata Alert
- Type: Threshold
- Condition: >= 10 events
- Severity: High
- Runs every: 1 minute

---

## Validation

Re-ran scan:

nmap -A 192.168.30.128

Result:
- Custom rule triggered successfully
- Alert generated in Elastic

---

## Key Takeaways

- Nmap reconnaissance generates detectable telemetry
- Source IP identification is critical
- Alerts can be converted into reusable detections
- Detection must be validated with real activity

---

## Screenshots

![Nmap Scan](screenshots/02-nmap-scan-dc-identification.png)
![Security Onion Alerts](screenshots/03-so-alerts-after-nmap-scan.png)
![Alert Drilldown](screenshots/04-so-alert-drilldown-kali-source-ip.png)
![Kibana Query](screenshots/05-kibana-query-kali-suricata-activity.png)
![Rule Creation](screenshots/06-kibana-search-threshold-rule-created.png)
![Rule Metadata](screenshots/06b-kibana-rule-metadata-and-schedule.png)
![Custom Rule](screenshots/07-kibana-custom-rule-visible.png)
![Targeted Scan](screenshots/08-targeted-nmap-scan-dc.png)
![Rule Fired](screenshots/09-custom-rule-fired-history.png)
![RDP Enabled](screenshots/10-rdp-enabled-on-dc.png)

---

## Conclusion

This phase demonstrates how reconnaissance activity can be detected, investigated, and transformed into a working SIEM detection rule. The lab is now prepared for the next phase involving initial access and credential-based attacks.