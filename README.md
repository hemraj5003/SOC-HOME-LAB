Introduction

For understanding how real attacks happen and how defenders detect them, I decided to design my own SOC home lab. My goal was simple: understand how security architecture works, how attackers try to access systems, and how monitoring tools detect suspicious activity.

When I started learning cybersecurity, I had many questions. Watching tutorials helped, but I realized the best way to learn was by building something myself. That’s why I decided to create a small lab environment where I could simulate attacks and monitor them.

Lab Setup

I created a small environment using three systems:

Kali Linux – attacker machine
Windows – victim system with Wazuh agent
Ubuntu Server – Wazuh manager and dashboard

Architecture of the lab:

Kali Linux (Attacker) -- >  Attack Simulation
Windows Host (Wazuh Agent) -- > Security Logs
Ubuntu Server (Wazuh Manager) --> Wazuh Dashboard

In this setup, Kali performs attack simulations, Windows generates logs, and Ubuntu collects and analyzes those logs using Wazuh SIEM.

 ---- >>> Network Configuration
I configured the network like this:
Ubuntu Server – NAT network (Wazuh manager)
Windows – Bridged network
Kali Linux – Bridged network
This allows Kali and Windows to communicate directly while Ubuntu receives logs from the Windows Wazuh agent.

 ---- >>> Installing Wazuh
Wazuh was installed on the Ubuntu server using the official installation script.
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash wazuh-install.sh -a

After installation, the Wazuh dashboard became accessible through the browser where security alerts and logs can be monitored.

Attack Simulation
Once the lab was ready, I tested some basic attack activities from Kali Linux.

---- >>> Network Discovery (ARP Scan)

First, I performed an ARP scan to discover live devices on the local network.
 ------#### arp-scan --localnet

This helps identify active hosts in the network before performing deeper reconnaissance.

---- >>> Network Scanning
After identifying the target system, I used Nmap to scan for open ports.
Basic SYN scan:
  ------#### nmap -sS <target-ip>

---- >>> Full Port Scan
To discover all open ports on the target machine, I performed a full port scan.
------#### nmap -sS -p- <target-ip>
 This scan checks all 65,535 ports, which helps identify services that may not appear in a basic scan.

This simulates reconnaissance by discovering open ports.

---- >>>  SMB Enumeration and Access

After discovering that port 445 (SMB) was open on the Windows machine, I attempted to enumerate and access the SMB shares from Kali Linux.
First, I checked the available shares using smbclient.
 ------#### smbclient -L //<target-ip> -p 445 -U username
 This command lists the available SMB shares on the target system.

At first, I tried using fake credentials, which resulted in authentication failures. These failed login attempts were recorded by the Windows system and were visible in the Wazuh dashboard as authentication errors.

After that, I attempted the connection again using valid credentials.
------####  smbclient //<target-ip>/C$ -p 445 -U username
 Once authenticated successfully, I was able to see the shared resources such as C drive and D drive.

However, the connection closed shortly after, which also generated events in the system logs. Observing these events helped me understand how authentication attempts and SMB access activities appear in monitoring tools like Wazuh.

--- >>> DoS Simulation
 ------####   hping3 --flood -S <target-ip>
 This helped generate unusual network activity and observe alerts in Wazuh.

What I Learned ? 
-- > Building this SOC home lab helped me understand:
     Basic SOC architecture
     How SIEM tools collect logs
     How attack activities generate security events
     How monitoring tools detect suspicious behavior

Even though this is a simple setup, it helped me understand both attacker behavior and defensive monitoring.

Future Plans
In the future, I plan to improve this lab by adding:

-> pfSense firewall
-> More attack simulations
-> Additional monitoring techniques (AI for security)

This project helped me move from theory to practical learning.
