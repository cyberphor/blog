---
layout: post
title: 'Windows Group Policy'
category: guides
permalink: 'guide/windows-group-policy'
---

### Table of Contents
* [Windows Remote Management](#windows-remote-management)
* [Windows Event Forwarding](#windows-event-forwarding)
* [Startup Scripts](#startup-scripts)

### Windows Remote Management
**TLDR**  
1. Configure the WinRM service so it starts automatically
2. Configure the WinRM service to listen for HTTP requests on all available NICs
3. Configure Windows Firewall with Advanced Security to allow inbound connections to the WinRM service
4. Configure Windows Defender to allow remote administration 

**Procedures**
1. Computer Configuration > Preferences > Control Panel Settings > Services
    * Right-click on “Service” and select “New > Service”
    * Select “Automatic” for the “Startup” option
    * Select “WinRM” as the “Service name”
    * Select “Start service” as the “Service action”
    * Click-on “Apply”
    * Click-on “OK”
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

### Windows Event Forwarding
1. Computer Configuration > Policies > Administrative Templates > Windows Components > Event Forwarding
    * Right-click “Configure target Subscription Manager” and select “Edit”
    * Select “Enabled”
    * Click-on “Show…”
    * Add an similar entry below to the “Value” field for each “Event Collector:”
        * Server=http://dc1.vanilla.sky.net:5985/wsman/SubscriptionManager/WEC,Refresh=60
    * Click-on “Apply”
    * Click-on “OK”

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
