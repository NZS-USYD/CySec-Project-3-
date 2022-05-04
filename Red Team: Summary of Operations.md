# Red Team's Summary of Operations

### Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services
Nmap scan results for each machine reveal the below services and OS details:
- Command: `$ nmap -sV 192.168.1.0/24 |cat nmapscan.txt`
- Output: 
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%201.%20NMAP%20scan-Discovery.PNG" width="500" height="600">
  
  Fig. 1: Network mapping with NMAP.
 
 From the scan, we are able to identify the target VM defined by the IP `192.168.1.110/24`.
 In order to find the detailed information and services related to port the following comad was used:
 
- Command: `$ nmap -T4 -A -v 192.168.1.110 |cat scan_target1.txt`
- Output: 
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%202.%20NMAP%20scan-Discovery%20Of%20Target%201(192.168.1.110).PNG" width="500" height="600">
  
  Fig. 2: Detailed information gathering regarding 192.168.1.110/24 (ports, services and vulnerabilities).
