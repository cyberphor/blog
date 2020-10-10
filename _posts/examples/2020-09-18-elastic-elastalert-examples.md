---
layout: post
title: 'Elastic Stack: ElastAlert'
category: examples
permalink: 'examples/elastic-stack-elastalert'
---

### Table of Contents
* [Whitelist IP Addresses](#whitelist-ip-addresses)
* [Whitelist Ports](#whitelist-ports)
* [Whitelist DNS Queries](#whitelist-dns-queries)

### Whitelist IP Addresses
Baseline File
```bash
# this file is saved under /etc/elastalert/rules/_authorized_ips.txt
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
    - "!file /etc/elastalert/rules/_authorized_ips.txt"
alert:
    - debug
```

### Whitelist Ports
Baseline File
```bash
# this file is saved under /etc/elastalert/rules/_authorized_ports.txt
22
53
80
```

Configuration File
```yaml
# alert on any destination port not on the whitelist

es_host: elasticsearch
es_host: 9200
name: Abnormal outbound network connections detected!
index: "*:logstash-*"
type: whitelist
compare_key: destination_port
ignore_null: true
whitelist:
    - "!file /etc/elastalert/rules/_authorized_ports.txt"
alert:
    - debug
```

### Whitelist DNS Queries
Baseline File
```bash
# this file is saved under /etc/elastalert/rules/_normal_DNS_queries.txt
github.com
google.com
cyberphor.com
```

Configuration File
```yaml
# alert on any DNS query not on the whitelist

es_host: elasticsearch
es_host: 9200
name: Unknown IP detected!
index: "*:logstash-*"
type: whitelist
compare_key: query
ignore_null: true
whitelist:
    - "!file /etc/elastalert/rules/_normal_DNS_queries.txt"
filter:
    - wildcard:
        event_type: "bro_dns"
alert:
    - debug
```
