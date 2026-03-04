# SOC-HOME-LAB

Overview

This project demonstrates the implementation of a Security Operations Center (SOC) home lab using Wazuh SIEM to monitor and analyze security events generated from simulated attacks.

The lab environment consists of three machines:

Ubuntu Server – Wazuh Manager (SIEM)  - (Security)

Windows Host Machine – Endpoint monitored using Wazuh Agent 

Kali Linux – Attacker machine used to simulate adversarial activity - (attacker)

The objective of this project is to simulate attacker behavior and analyze how a SIEM platform collects, processes, and detects security events.

             Bridged Network
        ┌──────────────┐      ┌──────────────┐
        │  Kali Linux  │ ---> │   Windows    │
        │  (Attacker)  │      │ (WazuhAgent) │
        └──────────────┘      └──────┬───────┘
                                     │ Logs
                                     ▼
                             ┌──────────────┐
                             │    Ubuntu    │
                             │ Wazuh Manager│
                             │  NAT Network │
                             └──────┬───────┘
                                    │
                                    ▼
                             ┌──────────────┐
                             │    Wazuh     │
                             │  Dashboard   │
                             └──────────────┘
  
  It uses a combination of NAT networking, port forwarding, and bridged networking to allow attack simulation and monitoring.

Wazuh Installation

The Wazuh manager was installed on the Ubuntu server using the official installation script.

curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh

sudo bash wazuh-install.sh -a

After installation, the Wazuh dashboard became available . 


