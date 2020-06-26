---
layout: post
title: 'Understanding iptables Syntax'
permalink: 'understanding-iptables-syntax'
category: posts
subcategory: 'vulnerability-management'
---

## Table of Contents
* [Table section](#table-section)
* [Action section](#action-section)
* [Chain section](#chain-section)
* [Protocol section](#protocol-section)
* [Rule section](#rule-section)

In [1998](https://www.netfilter.org/about.html#history), Paul “Rusty” Russell, Marc Boucher, and other C programmers developed `ipchains`. `ipchains` was a Linux utility used to provide firewall functions. The program was actually a collection of modules designed to interface with the kernel. It’s most basic syntax was organized into five sections: `table`, `action`, `chain`, `protocol`, and `rule`. Today, the `ipchains` utility has since be renamed **iptables**. It is [now](https://www.netfilter.org/about.html#history) maintained by Pablo Neira Ayuso, Jozsef Kadlecsik, Eric Leblond, Florian Westphal, and Arturo Borrero González.

### Table section

Each `iptables` table represents a different packet processing method. Although, to be succinct, they process Protocol Data Units (PDUs - not just packets). Nonetheless, there are five different ways a PDU can be processed: filter, security, nat, mangle, and raw.

Of the group, `filter` is the most common module and used as the default table. The `security` table is used in conjunction with other modules such as those relating to authentication, authorization, and accounting (think of Security-Enhanced Linux, or SELinux). `nat` refers to redirecting PDUs based on source and destination IP addresses. `mangle` offers custom options for stripping or modifying PDU header information.

### Action section
Actions include appending, deleting, checking, and listing. For example, if an administrator wanted to modify the system’s firewall, they would use `-A` to append and `-D` to delete. If they wanted to check or list the current configuration, they would use `-C` and `-L` respectively.

### Chain section
The word “chain” can be confusing to some folks. The most simplest way to understand this section is focusing on the direction one is aiming to configure. For example, there are two main directions (chains): `INPUT` and `OUTPUT`.

There is also a `FORWARD` chain which is used to determine whether or not PDU get routed elsewhere.

### Protocol section
`iptables` can filter multiple procotols such as IP, ICMP, UDP, and TCP. It can also be configured to filter “all” as well as those identified in `/etc/protocols`.

### Rule section
Lastly, one can specify rules to either `ACCEPT`, `REJECT`, and `DROP` PDUs. As a final example, one may execute the command sentence below to block inbound ICMP traffic.

```bash
iptables -t filter -A INPUT -p icmp -j REJECT
	    ^       ^ ^        ^       ^
	    |       | |	       |       |
	    |	    | |	       |       +---rule
	    |	    | |        +---protocol
	    |	    | +---chain
	    |	    +---action
	    +---table
```
