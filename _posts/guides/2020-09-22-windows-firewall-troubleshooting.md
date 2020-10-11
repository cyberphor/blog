---
layout: post
title: 'Windows Firewall: Troubleshooting'
category: guides
permalink: 'guides/windows-firewall/troubleshooting'
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
