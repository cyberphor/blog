---
layout: post
title: 'ACAS: Troubleshooting'
permalink: 'acas-troubleshooting'
category: notes
subcategory: guides
---

## Table of Contents
* [Un-installing SecurityCenter](#uninstalling-securitycenter)
* [Un-installing Nessus](#uninstalling-nessus)

### Un-installing SecurityCenter 
```bash
yum list | grep -i SecurityCenter
yum remove SecurityCenter*
for file in $(rpm -q --config SecurityCenter); do rm -f $file; done && rpm -e SecurityCenter
```

### Un-installing Nessus 
```bash
yum list | grep -i Nessus
yum remove Nessus*
```

### References
- https://serverfault.com/questions/41502/yum-equivalent-of-apt-get-purge
