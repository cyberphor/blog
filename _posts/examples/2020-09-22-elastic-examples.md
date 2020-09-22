---
layout: post
title: 'Elastic Stack'
permalink: 'elastic-stack-examples'
category: notes
subcategory: examples
---

### Table of Contents
- [Filebeat Configuration File](#filebeat-configuration-file)

### Filebeat Configuration File
```yaml
filebeat.prospectors:
- input_type: log
 paths:
 - C:\Windows\System32\LogFiles\Firewall\*.log
 document_type: windowsfirewall
 fields:
 logtype: windowsfirewall
 
output:
 logstash:
 hosts: ["192.168.3.9:5044"]
```

### References
* [Syspanda: Collecting and sending Windows Firewall Event logs to ELK](https://www.syspanda.com/index.php/2017/10/04/collecting-sending-windows-firewall-event-logs-elk/)
