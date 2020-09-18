---
layout: post
title: 'Snort'
permalink: 'snort-examples'
category: notes
subcategory: examples
---

## Table of Contents
* [ICMP](#icmp)

## ICMP
```bash
alert icmp any any -> $HOME_NET any (msg:"ICMP traffic!"; sid:3000001;)
```
