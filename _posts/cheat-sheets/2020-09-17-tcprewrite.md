---
layout: post
title: 'tcprewrite'
permalink: 'tcprewrite'
category: 'notes'
subcategory: 'cheat-sheets'
---

```bash
sudo tcpdump -i eth0 -w dirty.pcap
```
```bash
tcpprep --port --pcap=dirty.pcap --cachefile=dirty.cache
tcprewrite --cachefile=dirty.cache --endpoints=12.34.56.78:10.10.10.10  --infile=dirty.pcap --outfile=clean.pcap
```
