---
layout: post
title: 'How to Configure a Host-based Firewall'
permalink: 'how-to-configure-a-host-based-firewall'
category: posts
subcategory: 'vulnerability-management' 
---

Here’s a run-down of iptables, a Linux utility for configuring a host-based firewall.

## Fundamentals

**Syntax**  
```bash
iptables <table> <action> <chain> <protocol> <rule>
```

**Tables**  
```bash
iptables -t filter	# default; it is not required to explicitly specify this table
iptables -t nat 	# helpful in managing connection re-directs (based on src/dst IP)
iptables -t mangle	# used for stripping and modifying outbound traffic
iptables -t raw
iptables -t security
```

**Actions** (applicable to all tables)   
```bash
iptables -t filter -A 	# append
iptables -t filter -C	# check; compares user provided rule against what is configured
iptables -t filter -D 	# delete
iptables -t filter -L	# list; use this to review configured rules
```

**Chains** (for the “filter” table)  
```bash
iptables -t filter -A INPUT	# traffic destined for the localhost
iptables -t filter -A FORWARD	# traffic allowed through the localhost
iptables -t filter -A OUTPUT	# traffic outbound from localhost
```

**Protocols**
```bash
iptables -t filter -A INPUT -p tcp
iptables -t filter -A INPUT -p udp
iptables -t filter -A INPUT -p icmp
iptables -t filter -A INPUT -p ip 
```

**Rules**
```bash
iptables -t filter -A INPUT -p icmp -j DROP
iptables -t filter -A INPUT -p icmp -j REJECT
iptables -t filter -A INPUT -p icmp -j ACCEPT
```
