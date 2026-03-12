# SOC Home Lab – Attack Simulation & Monitoring with Wazuh

## Introduction

To understand how real cyber attacks occur and how defenders detect them, I designed my own **SOC (Security Operations Center) home lab**.
My goal was simple: understand **security architecture, attacker techniques, and how monitoring tools detect suspicious activity**.

When I first started learning cybersecurity, I had many questions. Watching tutorials helped, but I realized the best way to truly understand security was by **building and experimenting myself**.

So I created a small lab environment where I could **simulate attacks and observe how defensive tools detect them**.

---

# Lab Setup

The lab environment consists of **three machines**:

 System             Role                           
 **Kali Linux**    | Attacker machine               
 **Windows**       | Victim system with Wazuh Agent 
**Ubuntu Server** | Wazuh Manager + Dashboard      

### Architecture

```
Kali Linux (Attacker)  --->  Attack Simulation

Windows Host (Wazuh Agent)  --->  Security Logs

Ubuntu Server (Wazuh Manager)  --->  Wazuh Dashboard
```

In this setup:

* **Kali Linux** performs attack simulations
* **Windows** generates system and security logs
* **Ubuntu Server** collects and analyzes logs using **Wazuh SIEM**

---

# Network Configuration

The virtual network was configured as follows:

| Machine           | Network Type    |
| ----------------- | --------------- |
| **Ubuntu Server** | NAT Network     |
| **Windows**       | Bridged Network |
| **Kali Linux**    | Bridged Network |

This configuration allows:

* Kali and Windows to **communicate directly**
* Ubuntu to **collect logs from the Windows Wazuh agent**

---

# Installing Wazuh

Wazuh was installed on the Ubuntu server using the **official installation script**.

```
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash wazuh-install.sh -a
```

After installation, the **Wazuh dashboard** became accessible through the browser where security alerts and logs can be monitored.

---

# Attack Simulation

Once the lab environment was ready, I simulated several attack activities from **Kali Linux**.

---

## Network Discovery (ARP Scan)

First, I performed an **ARP scan** to discover live devices on the local network.

```
arp-scan --localnet
```

This helps identify active hosts before performing deeper reconnaissance.

---

## Network Scanning

After identifying the target system, I performed **port scanning using Nmap**.

### Basic SYN Scan

```
nmap -sS <target-ip>
```

This scan identifies **open ports and running services** on the target machine.

---

## Full Port Scan

To discover all open ports on the target machine:

```
nmap -sS -p- <target-ip>
```

This scans **all 65,535 ports** and helps identify services that may not appear in basic scans.

---

## SMB Enumeration and Access

After discovering that **port 445 (SMB)** was open on the Windows machine, I attempted to enumerate SMB shares.

### Listing SMB Shares

```
smbclient -L //10.36.140.137 -p 445 -U labuser
```

This command lists the available **SMB shares** on the target system.

At first, I tried using **invalid credentials**, which resulted in authentication failures.

These failed login attempts were **logged by Windows and detected by Wazuh**, appearing as authentication errors in the dashboard.

---

### Accessing SMB Share

Afterward, I attempted access using valid credentials:

```
smbclient //<target-ip>/C$ -p 445 -U username
```

Once authenticated successfully, I could see shared resources such as:

* C Drive
* D Drive

The connection closed shortly afterward, which also generated system log events.

Observing these events helped me understand **how authentication attempts and SMB activity appear in monitoring tools like Wazuh**.

---

## DoS Simulation

To simulate abnormal network activity, I performed a **DoS flood test**:

```
hping3 --flood -S <target-ip>
```

This generated unusual traffic, allowing me to observe how **Wazuh detects abnormal network behavior**.

---

# What I Learned

Building this SOC home lab helped me understand:

* SOC architecture and monitoring workflows
* How SIEM tools collect and analyze logs
* How attacker activities generate security events
* How monitoring tools detect suspicious behavior

Even though this is a simple setup, it helped me understand **both attacker behavior and defensive monitoring techniques**.

---

# Future Improvements

In the future, I plan to expand this lab by adding:

* **pfSense firewall**
* **More attack simulations**
* **Advanced monitoring techniques**
* **AI-based security analytics**

---

# Conclusion

This project helped me move from **theoretical cybersecurity knowledge to practical hands-on learning** by simulating attacks and observing how security monitoring systems respond.
