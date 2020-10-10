---
layout: post
title: 'Elastic Stack: Logstash'
category: examples
permalink: 'examples/elastic-stack/logstash'
---

### Table of Contents
* [Windows Firewall](#windows-firewall)

### Windows Firewall
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
