---
layout: post
title: 'Elastic Stack: ElastAlert'
category: examples
permalink: 'examples/elastic-stack-elastalert'
---

### Table of Contents
* [Whitelist for IP Addresses](#whitelist-for-ip-addresses)

### Whitelist for IP Addresses
Baseline File
```bash
192.168.56.1
192.168.56.2
192.168.56.3
```
Configuration File
```yaml
# alert on any IP address not on the whitelist

es_host: elasticsearch
es_host: 9200
name: Unknown IP address detected!
index: "*:logstash-*"
type: whitelist
compare_key: source_ip
ignore_null: true
whitelist:
    - "!file /etc/elastalert/rules/ip_address_baseline.txt"
alert:
    - debug
```
