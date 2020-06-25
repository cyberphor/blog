---
layout: post
title: 'Incident Response: Linux'
permalink: 'incident-response-linux'
category: notes
---

## Table of Contents
* [Review Running Processes](#review-running-processes)
* [Search for Abnormal Files](#search-for-abnormal-files)
* [Check Network Usage](#check-network-usage)
* [Review Scheduled Tasks](#review-scheduled-tasks)
* [Check for Bogus Accounts](#check-for-bogus-accounts)
* [Review Logs](#review-logs)
* [Check System Performance](#check-system-performance)

## Review Running Processes
```bash
sudo ps -aux
sudo lsof -p 123
chkconfig --list
```

## Search for Abnormal Files
```bash
sudo find / -uid 0 -perm -4000 -print
sudo find / -size 10000k -print
sudo find / -name " " -print
sudo find / -name ".. " -print
sudo find / -name ". " -print

lsof +:1

rpm -Va | sort
```

## Check Network Usage
```bash
ip link | grep PROMISC
netstat -nap
lsof -i
arp -a
```

## Review Scheduled Tasks
```bash
sudo crontab -u root -l
cat /etc/crontab
ls /etc/cron.*
```

## Check for Bogus Accounts
```bash
sudo sort -nk3 -t: /etc/passwd | less
sudo egrep ':0+:' /etc/passwd
sudo getent passwd | egrep ':0+:'
sudo find / -nouser -print
```

## Review Logs
```bash
# entered promiscuous mode
# logon failres
# rpc programs w/entries > 20 (strange characters)
# errors
# reboots and app restarts
```

## Check System Performance
```bash
sudo uptime # load average
sudo free
sudo df
```
