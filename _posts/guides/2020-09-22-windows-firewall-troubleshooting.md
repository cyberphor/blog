---
layout: post
title: 'Windows Firewall: Troubleshooting'
permalink: 'windows-firewall-troubleshooting'
category: notes
subcategory: guides
---

### Disable All Profiles
```pwsh
# example use-case: setting up a lab, troubleshooting, etc.
Set-NetFirewallProfile -All -Enabled false
```

### Log Filepath (Microsoft Windows Server 2012 R2 Standard)
```
%systemroot%\system32\LogFiles\Firewall\pfirewall.log
```
