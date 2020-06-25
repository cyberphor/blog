---
layout: post
title: 'Security Onion 16: Troubleshooting'
permalink: 'security-onion-16-troubleshooting'
category: notes
---

## Table of Contents
* [How to verify “Heavy Node” services are running](#how-to-verify-heavy-node-services-are-running) 
* [How to verify logs are being generated (written to disk)](#how-to-verify-logs-are-being-generated)
* [How to verify logs are being collected, parsed, and indexed](#how-to-verify-logs-are-being-collected-parsed-and-indexed)
* [How to verify network connectivity to the “Master”](#how-to-verify-network-connectivity-to-the-master)

## How to verify “Heavy Node” services are running
```bash
sudo so-status # everything should be green and say 'OK'
sudo so-pcap-status # full packet capture service
sudo so-nids-status # IDS alerting service
sudo so-barnyard-status # IDS alert spooling service
sudo so-bro-status # protocol dissector & logging service
sudo service syslog-ng status # IDS alert log collection service
sudo so-logstash-status # IDS alert log parsing service
sudo so-elasticsearch-status # IDS alert log indexing service
```

## How to verify logs are being generated (written to disk)
```bash
# find & list packet capture files written to disk today (by size)
find /nsm/sensor_data/*/dailylogs/ -ctime 0 -ls | awk '{print $7, $11}'

# find & list IDS alerts written to disk today (by size)
find /nsm/sensor_data/*/snort.uni* -ctime 0 -ls | awk '{print $7, $11}'

# if the size of the IDS alert log has not changed today, try this
sudo tcpreplay -i eth1 -M 10 /opt/samples/zeus-sample-1.pcap
find /nsm/sensor_data/*/snort.uni* -ctime 0 -ls | awk '{print $7, $11}'

# find & list protocol dissection logs written to disk today (by size)
find /nsm/bro/logs/ -ctime 0 -ls | awk '{print $7, $11}'
```

## How to verify logs are being collected, parsed, and indexed
```bash
# verify logs are being sent to Syslog-ng for collection
sudo tcpdump -i lo dst port 514 -X 

# verify Syslog-ng is sending collected logs to Logstash for parsing
sudo tcpdump -i lo dst port 6050 -X

# verify Logstash is sending parsed logs to Elasticsearch for indexing
sudo tcpdump -i any dst port 9200 -X

# verify Elasticsearch is indexing parsed logs from Logstash
sudo tcpreplay -i eth1 -M 10 /opt/samples/zeus-sample-1.pcap
curl localhost:9200/_search?q=source_ip:192.168.3.35 | jq '.' 
```

## How to verify network connectivity to the “Master”
```bash
# verify routing between the "Heavy Node" and "Master"
ip route | head -n1 # identify the default gateway of the "Heavy Node"
ping <your_default_gateway> # ping the default gateway
ping <master_ip_address> # ping the "Master" 

# verify SSH access between the "Heavy Node" and "Master"
nc -v <master_ip_address> 22 # probe port 22 on the "Master" 
ssh <SSH_acct_associated_w_HeavyNode>@<master_ip_address> # verify creds

# verify outbound connections to the "Master"
sudo netstat -n | grep 22 # check if SSH tunnel is up
sudo netstat -n | grep 4505 # check if inbound Salt connection is up
sudo netstat -n | grep 4506 # check if outbound Salt connection is up
sudo netstat -n | grep 7736 # check if outbound Squil connection is up
```
