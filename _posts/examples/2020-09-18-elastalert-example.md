---
layout: post
title: 'ElastAlert'
permalink: 'elastalert-example'
category: notes
subcategory: examples
---

## Table of Contents
* [Whitelist for IP Addresses](#whitelist-for-ip-addresses)
* [Whitelist for Ports](#whitelist-for-ports)

## Whitelist for IP Addresses
Baseline file for IP addresses. 
```bash
sudo vim /etc/elastalert/rules/_authorized_ips.txt
```
```bash
192.168.56.1
192.168.56.2
192.168.56.3
```
Configuration file for an IP address whitelist.
```bash
sudo vim whitelist-authorized_IPs.yaml
```
```yaml
# alert on any IP address not on the whitelist

es_host: elasticsearch
es_host: 9200
name: Unknown IP detected!
index: "*:logstash-*"
type: whitelist
compare_key: source_ip
ignore_null: true
whitelist:
    - "!file /etc/elastalert/rules/_authorized_ips.txt"
alert:
    - debug
```
