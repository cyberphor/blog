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
