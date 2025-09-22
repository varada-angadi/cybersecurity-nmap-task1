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
Before scanning for open ports, I first needed to identify which hosts were alive in my lab network
**Steps:**
1. Checked my Kali VM’s IP address: <br/>
$ ifconfig <br/>
inet 10.0.2.15  netmask 255.255.255.0 <br/>
From this, we determined 10.0.2.0/24, which includes all addresses from 10.0.2.1 to 10.0.2.254

2. Performed a TCP SYN scan on the subnet using Nmap: <br/>
$ nmap -sS 10.0.2.0/24 <br/>
- sS is a stealthy SYN scan that identifies live hosts and open ports

3. Target machine identified: <br/>
Target machine Metasploitable 2 was identified at 10.0.2.4 <br/>
Nmap reported which ports were open helps for next reconnaissance steps

4. Saved Output: <br/>
$ sudo nmap -sS -sV 10.0.2.4 -oN scan_results.txt <br/>
This file is included in the repository for review

5. To gather extra information, I ran Nmap default NSE scripts: <br/>
$ sudo nmap -sS -sV --script=default 10.0.2.4 -oN scan_nse.txt <br/>
- FTP allows anonymous login <br/>
- Samba exposes OS info and security mode <br/>
- HTTP server headers detected <br/>
Full NSE output is saved in scan_full_nse.txt for reference 

6. Packet Capture & Wireshark Analysis <br/>
$ sudo tcpdump -i eth0 -w packet_capture.pcap <br/>
Analysis with Wireshark, Opened the capture and follow TCP streams for services like FTP <br/>

Observations: <br/>
- Credentials are sent in plain text → easy for an attacker to sniff <br/>
- Commands like SYST and STAT reveal system type and server status <br/>
- Confirms that FTP service is live and accessible from the network <br/>

Security Implication: <br/>
- FTP without encryption exposes sensitive information <br/>
- Real networks should use SFTP or FTPS to protect credentials <br/>

## Some of the important findings
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

## Potential Security Risks
- FTP (Port 21): Anonymous login allowed → anyone can access files.
- Telnet (Port 23): Transmits data in plain text → credentials can be sniffed.
- Samba (Ports 139/445): Message signing disabled → susceptible to tampering or spoofing.
- Database ports (3306, 5432): Open without network restrictions → potential for unauthorized access.
- VNC (Port 5900): Remote access could be brute-forced if weak authentication.
- HTTP / Tomcat (Ports 80/8180): Server headers reveal software version → attackers can look up vulnerabilities.

## Skills Demonstrated
- Network reconnaissance & IP range discovery
- TCP SYN scanning & service detection
- NSE script usage for additional service info
- Packet capture & analysis (Wireshark/tcpdump)
- Understanding security risks of open ports and exposed services

## Files in Repository
- scan_results.txt → Nmap plain-text output
- scan_full_nse.txt → Full NSE script output 
- packet_capture.pcap → Captured network traffic
- Task1_screenshot.pdf → Wireshark streams, Nmap outputs
