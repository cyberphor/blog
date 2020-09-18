---
layout: post
title: 'ElastAlert'
permalink: 'elastalert-examples'
category: notes
subcategory: examples
---

## Table of Contents
* [Whitelist for IP Addresses](#whitelist-for-ip-addresses)
    * [Baseline File](#baseline-file)
    * [Configuration File](#configuration-file)

## Whitelist for IP Addresses
### Baseline File
```bash
192.168.56.1
192.168.56.2
192.168.56.3
```
### Configuration File
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
