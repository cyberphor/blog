---
layout: post
title: 'Windows Group Policy'
category: guides
permalink: 'guide/windows-group-policy'
---

### Table of Contents
* [Remote Procedure Call (RPC) Service](#remote-procedure-call-rpc-service)
* [Logging the "Sexy Six" Windows Event IDs](#logging-the-sexy-six-windows-event-ids)
* [Windows Remote Management (WinRM)](#windows-remote-management-winrm)
* [Windows Event Forwarding (WEF)](#windows-event-forwarding-wef)
* [Windows Event Collection (WEC)](#windows-event-collection-wec)
* [Startup Scripts](#startup-scripts)

### Remote Procedure Call (RPC) Service
1. Computer Configuration > Windows Settings > Security Settings > System Services
    * Right-click "Remote Procedure Call (RPC)" and select "Properties"
    * Click-on "Define this policy setting" and select "Automatic"
    * Click-on "Apply" and then, "OK"

### Logging the "Sexy Six" Windows Event IDs
1. Computer Configuration > Windows Settings > Security Settings > Local Policies > Security Options 
    * Right-click "Audit: Force audit policy subcategory settings (Windows Vista or later) to override audit policy category settings"
    * Select "Properties"
    * Select "Define this policy setting:" and "Enabled"
    * Click-on "Apply" and then, "OK"
2. Computer Configuration > Windows Settings > Security Settings > Advanced Audit Policy Configuration > Audit Policies
    * Expand these categories and select "Configure the following audit events:" and "Success"
        * Detailed Tracking > Audit Process Creation
        * Object Access > Audit File Share
        * Object Access > Audit File System
        * Object Access > Audit Registry
        * Object Access > Audit Filtering Platform Connection
    * Expand these categories and select "Configure the following audit events:", "Success", and "Failure"
        * Logon/Logoff > Audit Logon

### Windows Remote Management (WinRM)
#### TLDR
1. Configure the WinRM service to start automatically
2. Configure the WinRM service to listen for HTTP requests on all available NICs
3. Configure Windows Firewall with Advanced Security to allow inbound connections to the WinRM service
4. Configure Windows Defender to allow remote administration 
5. Link the "WinRM" Group Policy Object to the domain

#### How to Configure a Group Policy Object for "WinRM"
1. Computer Configuration > Windows Settings > Security Settings > System Services
    * Right-click "Windows Remote Management" and select "Properties"
    * Click-on "Define this policy setting" and select "Automatic"
    * Click-on "Apply" and then, "OK"    
2. Computer Configuration > Policies > Administrative Templates > Windows Components > Windows Remote Management (WinRM) > WinRM Service
    * Right-click on “Allow remote server management through WinRM” and select “Edit”
    * Select “Enabled”
    * Type an asterisk (*) into the “IPv4 filter” field
    * Click-on “Apply”
    * Click-on “OK”
3. Computer Configuration > Policies > Windows Settings > Security Settings > Windows Firewall with Advanced Security > Windows Defender Firewall with Advanced Security > Windows Defender Firewall with Advanced Security
    * Right-click-on “Inbound Rules” and select “New Rule”
    * Select “Predefined” and then, “Windows Remote Management”
    * Click-on “Next”
    * Remove the check from the “Public” profile
    * Click-on “Next”
    * Select “Allow the connection”
    * Click-on “Finish”
4. Computer Configuration > Policies > Administrative Templates > Network > Network Connections > Windows Defender Firewall > Domain Profile
    * Right-click on “Windows Defender Firewall: Allow inbound remote administration exception” and select “Edit”
    * Select “Enabled”
    * Type an asterisk (“*”) into the IPv4 field
    * Click-on “Apply”
    * Click-on “OK”

### Windows Event Forwarding (WEF)
#### TLDR 
1. Configure [Windows Remote Management](#windows-remote-management-winrm)
2. Configure clients to log the [Sexy Six Windows Event IDs](#logging-the-sexy-six-windows-event-ids)
2. Configure clients to forward events to your Event Collectors
3. Authorize the Network Service (SID: `S-1-5-20`) access to logs you wanted collected (ex: Security Logs)
4. Link the "WEF" Group Policy Object to the domain

#### How to Configure a Group Policy Object for "WEF" 
1. Computer Configuration > Policies > Administrative Templates > Windows Components > Event Forwarding
    * Right-click “Configure target Subscription Manager” and select “Edit”
    * Select “Enabled”
    * Click-on “Show…”
    * Add an entry like below to the “Value” field for each “Event Collector”:
        * `Server=http://dc1.vanilla.sky.net:5985/wsman/SubscriptionManager/WEC,Refresh=60`
    * Click-on “Apply”
    * Click-on “OK”
2. Computer Configuration > Polices > Administrative Templates > Windows Components > Event Log Service > Security
    * Right-click "Configure log access" and select "Edit"
    * Select "Enabled"
    * Add an entry like below to the "Log Access" field:
        * `O:BAG:SYD:(A;;0xf0007;;;SY)(A;;0x7;;;BA)(A;;0x1;;;BO)(A;;0x1;;;SO)(A;;0x1;;;S-1-5-32-573)(A;;0x1;;;S-1-5-20)`

### Windows Event Collection (WEC)
#### TLDR 
1. Configure [Windows Event Forwarding (WEF)](#windows-event-forwarding-wef)
2. Configure the WEC service to start automatically
3. Configure Event Collectors to subscribe to the Sexy Six Windows Event IDs
4. Link the "WEC" Group Policy Object to your Event Collectors

#### How to Configure a Group Policy Object for "WEC"  
1. Computer Configuration > Windows Settings > Security Settings > System Services
    * Right-click "Windows Event Collector" and select "Properties"
    * Click-on "Define this policy setting" and select "Automatic"
    * Click-on "Apply" and then, "OK"
#### How to Configure an Event Collector to Subscribe to the Sexy Six Windows Event IDs
1. Click-on "Subscriptions"
2. If prompted with the message below, select "Yes"
    * `To work with subscriptions, the Windows Event Collector Service must be running and configured. Do you want to start the service and/or configure it to automatically start when the the computer is restarted?`
3. Click-on "Create Subscription..."
4. Specify the following and click-on "OK"
    * Subscription name: `Forwarded Events`
    * Subscription type: `Source computer initiated`
    * Source computers: `Domain Computers`
    * Events to Collect
        * By log - Event logs: `Application, Security, System`
        * Event IDs: `4688,4624,5140,4663,4657,5156`
    
### Startup Scripts
1. Open the "Group Policy Management" snap-in
2. Right-click "Group Policy Objects" and select "New"
3. Name your GPO when prompted
4. Right-click your newly created GPO and select "Edit..."
5. Under "Computer Configuration," click-on "Polices > Windows Settings > Scripts (Startup/Shutdown)"
6. Double-click "Startup" 
7. Click-on the "PowerShell Scripts" tab
8. Click-on "Show Files..."
9. Copy & paste your script and any files it requires to this window
10. Click-on "Add..." and browse to where you uploaded your script
11. Highlight your script and click-on "Open" 
12. Specify any script parameters and then, click-on "OK"
  * Don't forget to link your existing GPO to the relevant OU
  * Run `gpupdate /force; shutdown /r /t 000` when testing your startup script

### References
* [How To Set Up Windows Event Log Forwarding In Windows Server 2016](https://adamtheautomator.com/windows-event-log-forwarding/#Allowing_the_Network_Service_to_Read_Event_Logs)
* [Use Windows Event Forwarding to help with intrusion detection](https://docs.microsoft.com/en-us/windows/security/threat-protection/use-windows-event-forwarding-to-assist-in-intrusion-detection)
* [Ask a Malware Archaeologist](https://www.slideshare.net/Hackerhurricane/ask-aalware-archaeologist)
* [Implement Auditing Using Group Policy and AuditPol.exe](https://www.rootusers.com/implement-auditing-using-group-policy-and-auditpol-exe/)
