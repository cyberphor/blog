---
layout: post
title: 'Snort'
category: examples
permalink: 'examples/snort'
---

### Table of Contents
* [ICMP](#icmp)

### ICMP
```bash
alert icmp any any -> $HOME_NET any (msg:"ICMP traffic!"; sid:3000001;)
```
