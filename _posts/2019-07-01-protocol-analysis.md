---
layout: post
title: 'Protocol Analysis'
permalink: 'protocol-analysis'
category: notes
---

## Table of Contents
* [Address Resolution Protocol (ARP)](#address-resolution-protocol)
* [Internet Control Message Protocol (ICMP)](#icmp)
* [Internet Protocol version 6 (IPv6)](#ipv6)
* [Transmission Control Protocol (TCP): Explicit Congestion Notification (ECN)](#tcp-ecn)
* [User Datagram Protocol](#udp)
* [Remote Procedure Call (RPC)](#rpc)
* [Domain Name System (DNS)](#dns)

## ARP
Detect ARP spoofing (duplicate ARP replies from different IP addresses)
```bash
# the 'newest' IP address is probably evil, overwriting the previous reply
tshark -nr traffic -Y 'arp.opcode == 2'
```
Gratuitous ARP frames
```bash
tshark -nr traffic.pcap -Y 'arp.isgratuitous == 1'
tshark -nr traffic.pcap -Y 'arp.src.proto_ipv4 == arp.dst.proto_ipv4'
```

## ICMP
Error Message: Type 11, Code 0 (Time To Live Exceeded in Transit)
```bash
tcpdump -nr traffic.pcap 'icmp[icmptype] = icmp-timxceed'
```

## IPv6
Find fragmented IPv6 packets
```bash
tcpdump -nr traffic.pcap 'ip6[6] == 44'
tshark -nr traffic.pcap -Y 'ipv6.nxt == 44'
```

## TCP: ECN
Packets with nodes identifying themselves as ECN-aware
```bash
# IP ECN flags off; TCP ECN and SYN flags on
tcpdump -nr traffic.pcap 'ip[1]&0x03=0 and tcp[13]&0xc2=0xc2'
```

Packets with ECN-aware nodes and no congestion
```bash
# IP ECN flags alternating; TCP ECN flags off
tcpdump -nr traffic.pcap 'ip[1]&0x03>0 and tcp[13]&0xc0=0'
```

Packets with ECN-aware nodes and congestion
```bash
# First notification: Both IP ECN flags on; Both TCP ECN flags off
tcpdump -nr traffic.pcap 'ip[1]&0x03=0x03 and tcp[13]&0xc0=0x00'

# Receiver settings: Both IP ECN flags on; Only TCP ECE flag on
tcpdump -nr traffic.pcap 'ip[1]&0x03=0x03 and tcp[13]&0xc0=0x40'

# Responder settings: Both IP ECN flags on; Both TCP ECN flags on
tcpdump -nr traffic.pcap 'ip[1]&0x03=0x03 and tcp[13]&0xc0=0xc0'
```

## UDP
Filter suspected Traceroute-generated traffic
```bash
# print all UDP packets destined for ports 33434 through 33534, 
# and ICMP packets w/Type 11, Code 0 (Timex)
tcpdump -nr traffic.pcap '(udp dst portrange 33434-33534) or (icmp[0]=11 and icmp[1]=0)'
```

Follow UDP Stream using Tshark from the Command Line
```bash
tshark -nr traffic.pcap -z follow,udp,hex,0     # 0 = first stream
```

## RPC
Find high-number ports of MS/RPC (DCE/RPC) Endpoint Mapping Response
```bash
tshark -nr traffic.pcap -Y 'dcerpc and epm' -w epm_traffic.pcap
tshark -nr traffic.pcap -Y 'dcerpc and epm' -T fields -e frame.number -e ip.src -e ip.dst -e epm.proto.tcp_port

# example output (columns: frame number, src ip, dst, ip, dst RPC port) 
4429	192.168.61.20	192.168.50.25	135 4430	192.168.50.25	192.168.61.20	49155 <--- dst RPC port 
```

## DNS
DNS responses with multiple answers 
```bash
tshark -nr traffic.pcap -Y 'dns.count.answers > 1'
tcpdump -nr traffic.pcap 'src port 53 and udp[12:2]=0 and udp[16:2]>0'
```
