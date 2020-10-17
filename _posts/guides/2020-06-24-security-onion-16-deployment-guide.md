---
layout: post
title:  'Security Onion 16: Deployment Guide'
category: guides
permalink: 'guides/security-onion/16/deployment'
---

## Table of Contents
* [Overview](#overview)
  * [Security Onion Requirements](#security-onion-requirements)
  * [Steps for deploying a distributed grid of intrusion sensors](#steps-for-deploying-a-distributed-grid-of-intrusion-sensors)
* [Installing Security Onion](#installing-security-onion)
* [Configuring a Master Node](#configuring-a-master-node)
  * [Configuring SSH accounts for Heavy Nodes](#configuring-ssh-accounts-for-heavy-nodes)
  * [Enabling remote access to analyst applications](#enabling-remote-access-to-analyst-applications)
  * [Creating analyst accounts](#creating-analyst-accounts)
* [Configuring a Heavy Node](#configuring-a-heavy-node)
  * [Ingesting Microsoft Windows events](#ingesting-microsoft-windows-events)
  * [Ingesting Syslog events](#ingesting-syslog-events)
* [Download links](#download-links)

## Overview
### Security Onion Requirements
* CPU: 4 cores 
* Memory: 8 GBs
* Disk space: 500 GBs (a Solid State Drive is preferred)
* Network Interface Cards: 2 (one for remote management, one to sniff locally)
* Routing between the “Master” and “Heavy Nodes”
* Firewall ports: when the "Master" is the destination
    * SSH (TCP port 22): nodes share their Elasticsearch databases here
    * Kibana (TCP port 443): analysts view all logs here
    * Salt dispatch (TCP port 4505): the "Master" publishes orchestration commands from here
    * Salt reporting (TCP port 4506): nodes send orchestration results here
    * Squil front-end (TCP port 7734): analysts manage Snort/Suricata alerts here
    * Squil back-end (TCP port 7736): nodes send Snort/Suricata alerts here
* Firewall ports: when your "Heavy Nodes" are the destination
    * SSH (TCP port 22): node Elasticsearch databases are accessed here
    * Syslog (TCP 514): Syslog events (ex: from routers, web servers, etc.) are sent here
    * Winlogbeat (TCP port 5044): Microsoft Windows events are sent here

### Steps for deploying a distributed grid of intrusion sensors
1. Coordinate & confirm routing, firewall, DNS, and browser support
2. Deploy "Analyst Workstations" 
3. Establish network-based monitoring
    * Deploy a “Master”
        * Create SSH accounts for each of your “Heavy Nodes”
        * Enable remote access to Analyst applications
        * Create additional Analyst accounts 
    * Deploy your “Heavy Nodes”
        * Allow Microsoft Windows events to be ingested
        * Allow Syslog events to be ingested 
4. Establish host-based monitoring
    * Enable “Microsoft Windows Remote Management (WinRM)” 
    * Enable “Microsoft Windows Event Forwarding (WEF)” 
    * Deploy “Event Collectors” 
    * Deploy “Sysmon" from Microsoft Windows SysInternals
    * Deploy “Winlogbeat" by Elastic
    * Enable "Syslog Forwarding" 

## Installing Security Onion
1. Load and boot from a bootable “Security Onion” DVD or .iso file
2. Select “English” and click-on “Continue”
3. DO NOT select “Download updates while installing” or “Install this third-party software”
    * Just click-on “Continue”
4. Select “Erase disk and install Security Onion”
    * Select “Use LVM with the new ‘Security Onion’ installation”
        * Click-on “Install Now”
        * Click-on “Continue” to “Write the changes to disk”
5. Accept the default “Timezone” and click-on “Continue”
6. Accept the default “Keyboard Layout” and click-on “Continue”
7. Fill-out the following information:
    * Your name: `vfernandez`
    * Your computer’s name: `foxhound-sensor1`
    * Pick a username: `vfernandez`
    * Choose a password: `YourLongAndStrongPasswordGoesHere`
    * Confirm your password: `YourLongAndStrongPasswordGoesHere`   
    *  Select “Require my password to log in"
        * DO NOT select “Encrypt my home folder”
    * Click-on “Continue”
8. Click-on “Restart” when prompted
9. Create a backup or snapshot of the OS if possible
    *  ex: `FreshOSInstall_20200205`

## Configuring a Master Node
1. Install the “Security Onion” OS (see above)
2. Login
3. Click-on the “Setup” icon
    * Click-on “Yes, Continue!”
    * Click-on “Yes, configure /etc/network/interfaces!”
    * Select the first NIC as your “Management Interface”
    * Select “Static” addressing and click-on “OK”
    * Enter your static IP address and click-on “OK”
    * Enter your network’s subnet mask and click-on “OK”
    * Enter your default gateway’s IP address and click-on “OK”
    * Enter your DNS server IP addresses and click-on “OK”
    * Enter the name of your domain and click-on “OK”
    * Click-on “Yes, configure sniffing interfaces”
    * Accept the default interfaces to sniff
    * Click-on “Yes, make changes!”
4. Click-on “Yes, reboot!” when prompted
5. Login
6. Click-on the “Setup” icon AGAIN
    * Click-on “Yes, Continue!”
    * Click-on “Yes, skip network configuration!”
    * Select “Production Mode” and click-on “OK”
    * Select “New” and click-on “OK”
    * Specify a username to create an Analyst account and click-on “OK”
    * Specify a password for the Analyst account and click-on “OK
    * Select “Best Practices” and click-on “OK”
    * Select the “Emerging Threats Open” IDS Ruleset and click-on “OK”
    * Select the “Suricata” IDS Engine and click-on “OK”
    * Select “Enable network sensor services” and click-on “OK”
    * Accept the default PF_RING slot size of 4096 and click-on “OK”
    * Accept the pre-selected interfaces to monitor and click-on “OK”
    * Set the “HOME_NET” variable to the sensor’s subnet and click-on “OK”
    * Click-on “Yes, store logs locally.”
    * Accept the default “LOG_SIZE_LIMIT” and click-on “OK”
    * Click-on “Yes, proceed with the changes!”
    * Click-on “OK” for the remaining pop-ups

### Configuring SSH accounts for Heavy Nodes
1. Login to the "Master"
2. Open a terminal (Click-on "Applications > System Tools > Xfce Terminal")
3. Create a user account
4. Add the newly created account to the `sudo` group
5. Repeat for each Heavy Node
```bash
# step 3
sudo adduser <unit-role_id>
# ex: sudo adduser foxhond-sensor1
```
```bash
# step 4
sudo usermod -aG sudo <unit-role_id>
# ex: sudo usermod -aG sudo foxhound-sensor1
```

### Enabling remote access to analyst applications
1. Login to the "Master"
2. Open a terminal (Click-on "Applications > System Tools > Xfce Terminal")
3. Add a rule to allow the analyst's workstation through the "Master's" firewall
```bash
# step 3
sudo so-allow
# type 'a' for analyst
# enter the subnet of the analyst; ex: '10.10.10.0/24'
# press 'Enter' when prompted
```

### Creating analyst accounts 
1. Login to the "Master"
2. Open a terminal (Click-on "Applications > System Tools > Xfce Terminal")
3. Create an analyst account within the "Master's" database
```bash
# step 3
sudo so-user-add
# enter a username for the analyst; ex: 'vfernandez'
# enter & confirm a password for the analyst
# press 'Enter' when prompted to create the analyst account
```

## Configuring a Heavy Node
1. Install the “SecurityOnion” OS (see above)
2. Login
3. Click-on the “Setup” icon
    * Click-on “Yes, Continue!”
    * Click-on “Yes, configure /etc/network/interfaces!”
    * Select the first NIC as your “Management Interface”
    * Select “Static” addressing and click-on “OK”
    * Enter your static IP address and click-on “OK”
    * Enter your network’s subnet mask and click-on “OK”
    * Enter your default gateway’s IP address and click-on “OK”
    * Enter your DNS server IP addresses and click-on “OK”
    * Enter the name of your domain and click-on “OK”
    * Click-on “Yes, configure sniffing interfaces”
    * Accept the default interfaces to sniff and click-on “OK”
    * Click-on “Yes, make changes!”
4. Click-on “Yes, reboot!” when prompted
5. Login
6. Click-on the “Setup” icon AGAIN
    * Click-on “Yes, Continue!”
    * Click-on “Yes, skip network configuration!”
    * Select “Production Mode” and click-on “OK”
    * Select “Existing” deployment and click-on “OK”
    * Enter the IP address of your “Master”
    * Enter the name of the SSH account associated with this sensor
    * Select “Heavy” and click-on “OK”
    * Select “Best Practices” and click-on “OK”
    * Accept the default PF_RING slot size of 4096 and click-on “OK”
    * Accept the pre-selected interfaces to monitor and click-on “OK”
    * Set the “HOME_NET” variable to the sensor’s subnet and click-on “OK”
    * Click-on “Yes, store logs locally.”
    * Accept the default “LOG_SIZE_LIMIT” and click-on “OK”
    * Click-on “Yes, proceed with the changes!”
    * When prompted,
        * Type ‘yes’ to accept the authenticity of your “Master”
        * Type the password of the SSH account associated with this sensor
    * Click-on “OK” for the remaining pop-ups

### Ingesting Microsoft Windows events
**Option 1 (do it remotely from the “Master”)**
1. Login to the "Master"
2. Open a terminal (Click-on "Applications > System Tools > Xfce Terminal")
3. Use `salt` to add rule allowing Winlogbeat through the firewall of all "Heavy Nodes"
```bash
# step 3
sudo salt '*' cmd.run 'ufw allow 5044'
```

**Option 2 (do it locally at each “Heavy Node”)**
1. Login to the "Heavy Node"
2. Open a terminal (Click-on "Applications > System Tools > Xfce Terminal")
3. Add a rule allowing Winlogbeat through the firewall of the "Heavy Node" you're logged into
4. Repeat for each "Heavy Node"
```bash
# step 3
sudo so-allow
# type 'b' for Beats (Winlogbeat)
# enter the IP address of the Event Collector; ex: '10.10.10.99'
# press 'Enter' when prompted
```

### Ingesting Syslog events
**Option 1 (do it remotely from the “Master”)**
1. Login to the "Master"
2. Open a terminal (Click-on "Applications > System Tools > Xfce Terminal")
3. Use `salt` to add rule allowing Syslog through the firewall of all "Heavy Nodes"
```bash
# step 3
sudo salt '*' cmd.run 'ufw allow 514'
```

**Option 2 (do it locally at each “Heavy Node”)**
1. Login to the "Heavy Node"
2. Open a terminal (Click-on "Applications > System Tools > Xfce Terminal")
3. Add a rule allowing Syslog through the firewall of the "Heavy Node" you're logged into
4. Repeat for each "Heavy Node"
```bash
# step 3
sudo so-allow
# type 'l' for Syslog
# enter the IP address of the Event Collector; ex: '10.10.10.99'
# press 'Enter' when prompted
```

## Configuring Syslog Forwarding
```bash
sudo vim /etc/rsyslog.d/security.conf
```
```bash
*.* @10.10.10.10:514
```
```bash
service rsyslog restart
```

### Download links
* [SecurityOnion 14.04.5](https://github.com/Security-Onion-Solutions/security-onion/releases/download/v14.04.5.12_20180416/securityonion-14.04.5.12.iso)
* [Sysmon](https://download.sysinternals.com/files/Sysmon.zip)
* [Sysmon Configuration File](https://github.com/SwiftOnSecurity/sysmon-config/blob/master/sysmonconfig-export.xml)
* [Sysmon Installation Script](https://github.com/cyberphor/scallion)
* [Winlogbeat 6.2.4](https://artifacts.elastic.co/downloads/beats/winlogbeat/winlogbeat-6.2.4-windows-x86_64.zip)
