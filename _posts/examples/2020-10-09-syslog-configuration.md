---
layout: post
title: 'Syslog: Configuration File'
category: examples
permalink: 'examples/syslog/configuration-file'
---

### Table of Contents
* [Accounts](#accounts)
* [iptables](#iptables)
* [sudo](#sudo)
* [Tripwire](#tripwire)

### Accounts
```bash
# save this file under "/etc/rsyslog.d/"
:programname, isequal, "useradd" /var/log/useradd.log
:programname, isequal, "useradd" @10.10.10.10:514

:programname, isequal, "usermod" /var/log/usermod.log
:programname, isequal, "usermod" @10.10.10.10:514

:programname, isequal, "userdel" /var/log/userdel.log
:programname, isequal, "userdel" @10.10.10.10:514
```

### iptables
```bash
# save this file under "/etc/rsyslog.d/"
:msg, contains, "[iptables]"    /var/log/iptables.log
:msg, contains, "[iptables]"    @10.10.10.10:514
```

### sudo
```bash
# save this file under "/etc/rsyslog.d/"
:programname, isequal, "sudo"    /var/log/sudo.log
:programname, isequal, "sudo"    @10.10.10.10:514
```

### Tripwire
```bash
:programname, isequal, "tripwire"    /var/log/tripwire.log
:programname, isequal, "tripwire"    @10.10.10.10:514
```
