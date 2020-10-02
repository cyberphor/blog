---
layout: post
title: 'Nessus: Troubleshooting'
permalink: 'nessus-troubleshooting'
category: notes
subcategory: guides
---

### Table of Contents
* [Un-installing SecurityCenter](#uninstalling-securitycenter)
* [Un-installing Nessus](#uninstalling-nessus)

### Un-installing SecurityCenter 
```bash
sudo service SecurityCenter stop
sudo yum remove SecurityCenter
sudo rm -rf /opt/sc
```

### Un-installing Nessus 
```bash
sudo service nessusd stop
sudo yum remove Nessus
sudo rm -rf /opt/nessus
```
