# Nmap Reconnaissance Task 1

## Objective
The goal of this task was to explore a target machine on my local lab network, discover which ports are open, identify the services running on them, and understand the potential security implications. Essentially, it’s about seeing what a hacker might see if they were scanning the network — all in a safe, controlled environment.

## Environment
- **Attacker VM:** Kali Linux  
- **Target VM:** Metasploitable 2 (a vulnerable machine)  
- **Network:** NAT (both VMs on the same subnet for easy communication) 

## Tools Used
- **Nmap** – for scanning ports, detecting services, and running some default NSE scripts  
- **Wireshark and tcpdump** – to capture network traffic and to get to know what’s happening behind the scenes

## Discovering IP Range & Hosts
Before scanning for open ports, I first needed to identify which hosts were alive in my lab network.

**Steps Taken:**
1. Checked my Kali VM’s IP address:
$ ifconfig
inet 10.0.2.15  netmask 255.255.255.0
From this, we determined 10.0.2.0/24, which includes all addresses from 10.0.2.1 to 10.0.2.254.

2. Performed a TCP SYN scan on the subnet using Nmap:

## Summary of Findings
| Port | Service | Notes |
|------|---------|-------|
| 21   | FTP     | vsFTPd 2.3.4, anonymous login allowed |
| 22   | SSH     | OpenSSH 4.7p1 Debian |
| 23   | Telnet  | Open, plain-text login possible |
| 25   | SMTP    | Open, not responding properly |
| 53   | DNS     | ISC BIND 9.4.2 |
| 80   | HTTP    | Apache 2.2.8 |
| 111  | RPC     | rpcbind 2 |
| 139/445 | Samba | OS: Unix, message signing disabled |
| 3306 | MySQL   | Database server |
| 5432 | PostgreSQL | Database server, SSL info available |
| 5900 | VNC     | Remote desktop access |
| 8180 | HTTP    | Apache Tomcat 5.5 |

### Key Observations
- Multiple open ports expose services with known vulnerabilities (lab environment).  
- FTP allows anonymous login → example of a real-world security risk.  
- Samba message signing is disabled, exposing potential attack vectors.  
- Packet capture confirms SYN → SYN-ACK handshake and service banners for open ports.  

## Files in Repository
- `scan_results.txt` → Nmap plain-text output  
- `scan_full_nse.txt` → Full NSE output (optional reference)  
- `packet_capture.pcap` → tcpdump/Wireshark capture of Nmap scan  
- `screenshots/` → Optional visual evidence  

## Skills Demonstrated
- Network reconnaissance & IP range discovery  
- TCP SYN scanning & service detection  
- NSE script usage  
- Packet capture & analysis (Wireshark/tcpdump)  
- Understanding security risks of open ports and exposed services  

