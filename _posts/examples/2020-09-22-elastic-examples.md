---
layout: post
title: 'Elastic Stack'
permalink: 'elastic-examples'
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
  - 'C:\Windows\System32\LogFiles\Firewall\*.log'

name: 'DomainController1'
document_type: windowsfirewall
logtype: windowsfirewall
 
output.logstash:
    hosts: ["192.168.3.9:5044"]
```

### Logstash Configuration File
```
filter {
  if [type] == "windowsfirewall" {
    grok {
      match => {
        "message" => "%{GREEDYDATA:Date} %{GREEDYDATA:Time} %{WORD:action} %{WORD:protocol} %{IP:source_ip} %{IP:destination_ip} %{INT:SrcPort} %{INT:DstPort} %{INT:Size} %{GREEDYDATA:Size}"      
      }
    }
  }
}
```

### References
* [Syspanda: Collecting and sending Windows Firewall Event logs to ELK](https://www.syspanda.com/index.php/2017/10/04/collecting-sending-windows-firewall-event-logs-elk/)
