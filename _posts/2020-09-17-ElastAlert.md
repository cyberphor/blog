---
layout: post
title: 'ElastAlert'
permalink: 'elastalert'
category: 'notes'
subcategory: 'tool-cheat-sheet'
---

## Table of Contents
* [TLDR (Too long; didn't read)](#tldr)
* [Use-Cases](#use-cases)
* [Installing ElastAlert](#installing-elastalert)
* [Writing Rules](#writing-rules)
* [Rule Types & Examples](#rule-types-examples)
    * [Whitelist](#whitelist)
* [Adding Rules](#adding-rules)
* [Running ElastAlert](#running-elastalert)
* [References](#references)

## TLDR
Using ElastAlert on Security Onion 16
1. Define your notification criteria (see [Use-Cases](#use-cases))
2. Create a rule file (see [Writing Rules](#writing-rules))
    * If necessary, create your support files (ex: whitelist, blacklist, etc.)
4. Copy everything to the `/etc/elastalert/rules/` directory on your Master Node
5. Test it by executing `sudo so-elastalert-test`
6. Restart ElastAlert using `sudo so-elastalert-restart`
7. Create a [Kibana](#references) Visualization on your Master Node to view the alerts

## Use-Cases
|Applicable Rule Type|Notification Criteria|
|---|---|
|[Flatine](#flatline)|Monitoring the heartbeat of a critical server|
|[Whitelist](#whitelist)|Detecting abnormal DNS queries<br>Detecting abnormal outbound network connections from a critical server|

## Installing ElastAlert 
```bash
# command goes here
```

## Writing Rules
### Option 1: Manually
1. Use a text-editor to create a new file
```bash
# naming convention tip: ruletype-rulename.yaml
sudo vim whitelist-authorized_IPs.yaml
```
2. Add your notification criteria using the proper syntax (see [Rule Types & Examples](#rule-types-examples) and/or [References](#references)). 

### Option 2: Automatically
Security Onion 16 has a script called `so-elastalert-create` that makes it easier to develop ElastAlert rules. 
```bash
sudo so-elastalert-create
```

## Rule Types & Examples
### Whitelist
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
```bash
sudo vim /etc/elastalert/rules/_authorized_ips.txt
```
```bash
192.168.56.1
192.168.56.2
192.168.56.3
```

## Adding Rules
### Security Onion 16
```bash
# copy your YAML file to the correct directory
sudo cp whitelist-authorized_IPs.yaml /etc/elastalert/rules/
```

## Running ElastAlert
```bash
# command goes here
```

## References
* [ElastAlert: Read the Docs](https://elastalert.readthedocs.io/en/latest/)
* [Security Onion 16: Read the Docs - ElastAlert](https://docs.securityonion.net/en/latest/elastalert.html)
* [Elasticsearch ElastAlert: Alerting at Scale](https://qbox.io/blog/elasticsearch-alerting-at-scale-using-elastalert)
