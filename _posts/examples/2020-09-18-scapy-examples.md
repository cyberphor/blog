---
layout: post
title: 'Scapy'
permalink: 'scapy-examples'
category: examples
---

## Table of Contents
* [DNS Request](#dns-request)

## DNS Request
```python
    ether = Ether()
    ether.src = '00:0a:0b:0c:0d:11'
    ether.dst = '00:0a:0b:0c:0d:22'
    ip = IP()
    ip.src = '127.0.0.1'
    ip.dst = '10.10.10.10'
    udp = UDP()
    udp.sport = 4321
    udp.dport = 53
    dns = DNS()
    dns.rd = 1
    dns.qd = DNSQR()
    dns.qd.qname = 'https://cyberphor.github.io/blog/'
    packet = ether/ip/udp/dns
    wrpcap('example.pcap', packet)
```
