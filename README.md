# Network-Enumeration-and-Stealth-Analysis-Report
Document the progression from infrastructure discovery to identifying critical source code leakages.



Network Enumeration and Stealth Analysis Report
Target IPs: 10.3.145.19, 10.0.2.3, 192.168.64.2
Objective: Document the progression from infrastructure discovery to identifying critical source code leakages.

# 1. Infrastructure Reconnaissance
The initial phase focused on identifying active hosts and scanning the full TCP port range (1-65535).

Full port scan on initial target
```bash
sudo nmap -p- 10.3.145.19
```
Result: Established a baseline of open ports on the target infrastructure.


# 2. Timing and Stealth Comparison
A comparative study was conducted to analyze the trade-off between scan speed and network "noise."

Paranoid Stealth Scan (T1)
Exhaustive stealth scan with maximum delay
```bash
sudo nmap -p- -sS -T1 10.0.2.3
```
Observation: Highly evasive, designed to bypass IDS/IPS by serializing packets with long delays.

Polite Stealth Scan (T2)
Adjusted timing for better efficiency
```bash
sudo nmap -p- -sS -T2 10.0.2.3
```
Observation: Faster than T1 while still maintaining a lower profile than default settings. Established the "Noisy vs. Stealthy" baseline.


# 3. Targeted Service & Version Enumeration
Shifting to a specific subnet, the scan was refined to focus on the top 10,000 ports with version detection and default scripting enabled.
bash

Version detection and default script scanning
```bash
sudo nmap -p 1-10000 -sT -sV -sC 192.168.64.2
```


# Critical Findings:
```bash
Port  Service    Version            Status/Risk
22    SSH        OpenSSH 7.6p1      OS: Ubuntu
80    HTTP       Apache 2.4.29      CRITICAL: .git directory exposed
8585  HTTP       Gitea (Golang)     Internal Git service active
```


# 4. Key Discovery: Source Code Leakage
The Nmap Scripting Engine (NSE) http-git script successfully identified a high-risk misconfiguration on port 80.
Evidence:

    Location:             http://192.168.64.2
    Repository Info:      PHP application found.
    Remote Origin:        http://devguru.local
    Username Identified:  frank
    Last Commit:          "first commit"


# 5. Summary of Methodology

    Discovery:        Scanned all ports to ensure no non-standard services were hidden.
    Evasion Testing:  Compared T1 and T2 timing templates to determine detection thresholds.
    Enumeration:      Used -sV and -sC to fingerprint services and identify high-value targets.
    Identification:   Confirmed a .git exposure, providing a direct path to source code extraction and potential credential harvesting.
