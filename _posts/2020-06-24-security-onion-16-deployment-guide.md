---
layout: post
title:  'Security Onion 16: Deployment Guide'
permalink: 'security-onion-16-deployment-guide'
category: notes
---

## Table of Contents
* [Overview](#overview)
  * [Security Onion Requirements](#security-onion-requirements)
  * [Distributed Deployment](#distributed-deployment)
* [Installing Security Onion](#installing-security-onion)
* [Configuring a Master Node](#configuring-a-master-node)
  * [Configuring SSH accounts for Heavy Nodes](#configuring-ssh-accounts-for-heavy-nodes)
  * [Enabling remote access to analyst applications](#enabling-remote-access-to-analyst-applications)
  * [Creating analyst accounts](#creating-analyst-accounts)
* [Configuring a Heavy Node](#configuring-a-heavy-node)
  * [Ingesting Microsoft Windows events](#ingesting-microsoft-windows-events)
  * [Ingesting Syslog events](#ingesting-syslog-events)
* [Configuring WEF](#configuring-wef)
  * [Enabling WinRM via Group Policy](#enabling-winrm-via-group-policy)
  * [Enabling WEF via Group Policy](#enabling-wef-via-group-policy)
  * [Deploying Sysmon via Group Policy](#deploying-sysmon-via-group-policy)
  * [Deploying Winlogbeat via Group Policy](#deploying-winlogbeat-via-group-policy)

# Overview
## Security Onion Requirements
* CPU: 2 sockets with 2 processors each
* Memory: 8 GBs of RAM
* Disk space: 500 GBs (Solid State Drive is preferred)
* Network Interface Cards (NIC): 2 (one for remote management, one to sniff locally)
* Routing between the “Master” and “Heavy Nodes”
* Firewall ports on the “Master” 
    * 22 = SSH: for each “Sensor” to advertise their Elasticsearch database
    * 443 = Kibana web app: used to view data visualizations
    * 4505 = Salt: for publishing orchestration commands from the “Master”
    * 4506 = Salt: for ingesting orchestration command results from each “Node”
    * 7734 = Squil web app: used to view Snort/Suricata alerts
    * 7736 = Squil database: for ingesting Snort/Suricata alerts from each “Node”
* Firewall ports on “Heavy Nodes”
    * 22 = SSH: for the “Master” to access their Elasticsearch database
    * 514 = Syslog: for ingesting Syslog events (ex: from routers, web servers, etc.)
    * 4505 = Salt: for publishing orchestration commands from the “Master”
    * 5044 = Winlogbeat: for ingesting Microsoft Windows events

## Distributed Deployment
1. Coordinate & confirm routing, firewall, DNS, and browser support
2. Deploy "Analyst Workstations" 
3. Establish network-based monitoring
    * Deploy a “Master”
        * Create SSH accounts for each “Heavy Node”
        * Enable remote access to Analyst applications
        * Create additional Analyst accounts as desired
    * Deploy “Heavy Nodes”
        * Allow Microsoft Windows events to be ingested
        * Allow Syslog events to be ingested (as desired)
4. Establish host-based monitoring
    * Enable “Microsoft Windows Event Forwarding (WEF)”
        * Tells a Windows machine to deliver its logs to an Event Collector
    * Enable “Microsoft Windows Remote Management (WinRM)”
        * Delivers Microsoft Windows events 
    * Deploy “Event Collectors”
        * Collects Microsoft Windows events
    * Deploy “Sysmon from Microsoft Windows SysInternals”
        * Generates logs based on host activity
    * Deploy “Winlogbeat by Elastic”
        * Feeds Microsoft Windows events to “Heavy Nodes”
    * Enable "Syslog Forwarding"
        * Feeds events from Linux/Unix-based machines to “Heavy Nodes”

# Installing Security Onion
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

# Configuring a Master Node
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

## Configuring SSH accounts for Heavy Nodes
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

## Enabling remote access to analyst applications
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

## Creating analyst accounts 
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

# Configuring a Heavy Node
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

## Ingesting Microsoft Windows events
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

## Ingesting Syslog events
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

# Configuring WEF
## Enabling WinRM via Group Policy
1. Create a GPO so the WinRM service listens for HTTP requests on all available NICs
    * Computer Configuration > Policies > Administrative Templates > Windows Components > Windows Remote Management (WinRM) > WinRM Service
        * Right-click on “Allow remote server management through WinRM” and select “Edit”
        * Select “Enabled”
        * Type an asterisk (*) into the “IPv4 filter” field
        * Click-on “Apply”
        * Click-on “OK”
2. Configure the same GPO to auto-start the WinRM service
    * Computer Configuration > Preferences > Control Panel Settings > Services
        * Right-click on “Service” and select “New > Service”
        * Select “Automatic” for the “Startup” option
        * Select “WinRM” as the “Service name”
        * Select “Start service” as the “Service action”
        * Click-on “Apply”
        * Click-on “OK”
3. Configure the same GPO to allow inbound connections to the WinRM service
    * Computer Configuration > Policies > Windows Settings > Security Settings > Windows Firewall with Advanced Security > Windows Defender Firewall with Advanced Security > Windows Defender Firewall with Advanced Security
        * Right-click-on “Inbound Rules” and select “New Rule”
        * Select “Predefined” and then, “Windows Remote Management”
        * Click-on “Next”
        * Remove the check from the “Public” profile
        * Click-on “Next”
        * Select “Allow the connection”
        * Click-on “Finish”
    * Computer Configuration > Policies > Administrative Templates > Network > Network Connections > Windows Defender Firewall > Domain Profile
        * Right-click on “Windows Defender Firewall: Allow inbound remote administration exception” and select “Edit”
        * Select “Enabled”
        * Type an asterisk (“*”) into the IPv4 field
        * Click-on “Apply”
        * Click-on “OK”
4. Link the same GPO to the domain


## Enabling WEF via Group Policy
1. Download “Sysmon” (see "Download Links")
2. Download a “Sysmon” install script (see “Download Links”)
3. Download a “Sysmon” configuration file (see "Download Links")
    * NOTE: if using the install script listed under “Download Links,” make sure the “Sysmon” configuration file is renamed to “SysmonConfig.xml”
4. Create a GPO called “Deploy-Sysmon,” but do not configure any settings yet
5. Use “File Explorer” to navigate to the following path on your Domain Controller:
    * C:\Windows\SYSVOL\sysvol\<your_domain_name>\Policies\<guid_of_deploy_sysmon_gpo>\Machine\Scripts\Startup\
6. Copy the items listed below to the folder mentioned above:
    * Eula.txt
    * Sysmon.exe
    * Sysmon64.exe
    * Install-Sysmon.ps1
    * SysmonConfig.xml
7. Now, configure the “Deploy-Sysmon” GPO to require the install script to be executed at “Startup”
    * Computer Configuration > Policies > Windows Settings > Scripts
        * Right-click “Startup” and select “Properties”
        * Click-on the “PowerShell Scripts” tab
        * Click-on “Add…”
        * Click-on “Browse”
        * Select the install script previously copied
        * Click-on “OK”
        * Click-on “Apply”
        * Click-on “OK”
8. Link the “Deploy-Sysmon” GPO to the domain
9. Reboot target machines

## Deploying Sysmon via Group Policy
1. Create a GPO to enable “WinRM” and link it to the domain (see above)
2. Create a “Enable-WEF” GPO so clients send events to designated “Event Collectors”
    * Computer Configuration > Policies > Administrative Templates > Windows Components > Event Forwarding
        * Right-click “Configure target Subscription Manager” and select “Edit”
        * Select “Enabled”
        * Click-on “Show…”
        * Add an similar entry below to the “Value” field for each “Event Collector:”
            * Server=http://<event_collector_fqdn>:5985/wsman/SubscriptionManager/WEC,Refresh=60
        * Click-on “Apply”
        * Click-on “OK”
3. Link the “Enable-WEF” GPO to the domain
4. Create a Security Group called “Event Collectors”
    * Add servers designated for this role to this Security Group 
5. Create a GPO called “Deploy-EventCollectors,” but do not configure any settings yet
6. Use “File Explorer” to navigate to the following path on your Domain Controller:
    * C:\Windows\SYSVOL\sysvol\<your_domain_name>\Policies\<guid_of_deploy_event_collectors_gpo>\Machine\Scripts\Startup\
7. Copy the items listed below to the folder mentioned above:
    * Configure-EventCollector.ps1
    * EventSubscription-Sysmon.xml
8. Now, configure the “Deploy-EventCollectors” GPO to require the install script to be executed at “Startup”
    * Computer Configuration > Policies > Windows Settings > Scripts
        * Right-click “Startup” and select “Properties”
        * Click-on the “PowerShell Scripts” tab
        * Click-on “Add…”
        * Click-on “Browse”
        * Select the install script previously copied
        * Click-on “OK”
        * Click-on “Apply”
        * Click-on “OK”
9. Configure the “Deploy-EventCollectors” GPO so it is only applied to the “Event Collectors” Security Group
    * Click-on “Add” under the “Security Filtering” settings
    * Browse for and select the “Event Collectors” Security Group
    * Click-on “OK”
    * Highlight “Authenticated Users” and click-on “Remove”
10. Link the “Deploy-EventCollectors” GPO to the domain

## Deploying Winlogbeat via Group Policy
1. Download Winlogbeat (see "Download Links") 
2. Modify YAML file
3. Install Winlogbeat using PowerShell
    * NOTE: only install Winlogbeat on the “Event Collector” 
4. Configure the “Winlogbeat” service to auto-start
5. Manually start the “Winlogbeat” service if it’s not started already
