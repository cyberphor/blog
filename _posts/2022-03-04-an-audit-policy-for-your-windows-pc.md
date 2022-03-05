---
layout: post
title: 'An Audit Policy for Your Windows PC'
permalink: 'an-audit-policy-for-your-windows-pc'
---

I spend a lot of time researching and developing techniques to monitor computer networks. Yet, I never set aside something I could copy/paste onto any of my Windows-based Personal Computers (PC) or recommend to others. For this reason, I put together the list of commands below. Copy/paste them into PowerShell to enable some of the most commonly recommended audit categories. Once you're done, you can review security events on your PC using Event Viewer or something like locally-hosted Elastic instance (Elasticsearch, Kibana, and Winlogbeat). 
```pwsh
# Event IDs 4624 and 4625
auditpol /set /subcategory:"Logon" /success:Enable /failure:Enable

# Event ID 4657
auditpol /set /subcategory:"Registry" /success:Enable

# Event ID 4663
auditpol /set /subcategory:"File System" /success:Enable

# Event ID 4688
auditpol /set /subcategory:"Process Creation" /success:Enable

# Event ID 5140
auditpol /set /subcategory:"File Share" /success:Enable

# Event ID 5156
auditpol /set /subcategory:"Filtering Platform Connection" /success:Enable
```

### Reference  
[https://www.slideshare.net/Hackerhurricane/finding-attacks-with-these-6-events](https://www.slideshare.net/Hackerhurricane/finding-attacks-with-these-6-events)
