---
layout: post
title: 'Nessus: Troubleshooting'
permalink: 'nessus-troubleshooting'
category: notes
subcategory: guides
---

### Table of Contents
* [Un-installing Nessus](#uninstalling-nessus)
* [Un-installing SecurityCenter](#uninstalling-securitycenter)

### Un-installing Nessus 
```bash
yum list | grep -i Nessus
yum remove Nessus*
```

### Un-installing SecurityCenter 
```bash
yum list | grep -i SecurityCenter
yum remove SecurityCenter*
for file in $(rpm -q --config SecurityCenter); do rm -f $file; done && rpm -e SecurityCenter
```

### References
- [ServerFault: Yum equivalent of “apt-get purge”](https://serverfault.com/questions/41502/yum-equivalent-of-apt-get-purge)
