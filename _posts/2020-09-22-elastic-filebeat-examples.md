---
layout: post
title: 'Elastic Stack: Filebeat'
category: examples
permalink: 'examples/elastic-stack/filebeat'
---

### Table of Contents
* [Windows Firewall](#windows-firewall)

### Windows Firewall
```yaml
filebeat.prospectors:
- input_type: log
  paths:
  - 'C:\Windows\System32\LogFiles\Firewall\*.log'

name: 'DomainController1'
document_type: windowsfirewall
logtype: windowsfirewall
 
output.logstash:
    hosts: ["192.168.3.9:5044"]
```
