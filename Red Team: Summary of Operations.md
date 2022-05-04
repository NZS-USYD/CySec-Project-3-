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
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%202.%20NMAP%20scan-%20Port%20Discovery%20Of%20Target%201(192.168.1.110).png" width="500" height="600">
  
  Fig. 2: Detailed information gathering regarding `192.168.1.110/24` (ports, services and vulnerabilities).
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%203.%20NMAP%20scan-%20Port%20and%20service%20Discovery%20Of%20Target%201(192.168.1.110).PNG" width="500" height="600">
  
  Fig. 3: Detailed information gathering regarding `192.168.1.110/24` (ports, services and vulnerabilities).
  
    <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%204.%20NMAP%20scan-%20Port%20and%20service%20Discovery%20Of%20Target%201(192.168.1.110).PNG" width="500" height="600">
  
  Fig. 4: Detailed information gathering regarding `192.168.1.110/24` (ports, services and vulnerabilities).
  
 Table 1: reconnaissance findings of the target `192.168.1.110`
  
| Open Ports | Services                         |
|------------|----------------------------------|
| 22         | SSH                              |
| 80         | HTTP                             |
| 111        | rpcbind                          |
| 139        | netbios-ssn Samba smbd 3.X - 4.X |
| 445        | netbios-ssn Samba smbd 3.X - 4.X |

### Critical Vulnerabilities

We decided to use two scanners for finding vulnerability in target `192.168.1.110/24`. They are:
- Nikto (an open source vulnerability scanner)
- WPScan (a balackbox security scanner)

Nikto lists a number of vulnerabilities in `192.168.1.110`.
- Command: `$ nikto -h 192.168.1.110`
- Output: 
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%205%20Nikto%20scan.PNG" width="500" height="300">
  
  Fig. 5: Nikto revealing information about possible vulnerabilities.
  Nikto reveals that:
  - the target is vulnerable to XSS.
  - the target is running Apache webserver.
  - the target is vulnerable to minor information disclousure vulnerability (OSVDB-3092)
  - the target is providing directory listing, i.e., which may allow an attacker to read arbitrary files which is akin to directory traversal attack. 

Based on these preliminary finding we will use another tool (WPScan) to get more information.
- Command: `$ wpscan --url http://192.168.1.110/wordpress --enumerate u`
- Output: 

 
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%206%20WPscan.PNG" width="500" height="300">
  
  Fig. 6: WPScan revealing information about possible vulnerabilities.
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%207%20WPscan2.PNG" width="500" height="300">
  
  Fig. 7: WPScan revealing information about possible vulnerabilities.
  
  WPScan reveals that:
  

  
  
   
