---
layout: post
title: 'Security Onion 16: Tuning'
permalink: 'security-onion-16-tuning'
category: notes
---

## Table of Contents
* [Fine-tuning OSSEC rules](#fine-tuning-ossec-rules)
* [Whitelist a known-good IP address](#whitelist-a-known-good-ip-address)
  * [Netsniff-NG](#netsniff-ng)
  * [OSSEC](#ossec)
* [Syntax for querying the Elasticsearch database](#syntax-for-querying-the-elasticsearch-database)
* [Change the name of a sensor (a.k.a Salt Minion ID)](#change-the-name-of-a-sensor-aka-salt-minion-id)
* [Update NIDS (Snort/Zeek) rules](#update-nids-snortzeek-rules)
* [Disable a NIDS (Snort/Suricata) rule](#disable-a-nids-snort-rule)
* [Clearing alerts](#clearing-alerts)
* [Creating a custom Kibana dashboard](#creating-a-custom-kibana-dashboard)

## Fine-tuning OSSEC rules
1. Find the rule ID you want to disable
2. Use a text-editor to open `/var/ossec/rules/local_rules.xml`
3. Reduce the rule's alert level to zero
4. Restart OSSEC

```bash
# step 1
sudo grep -Ri 'Web server 500' /var/ossec/rules/
```
```bash
# step 2
sudo vim /var/ossec/rules/local_rules.xml
```
```xml
<!-- Added by Victor on 22 JUN 2020 -->
<rule id="100666" level="0">
  <if_sid>31120</if_sid>
  <description>Ignore 'Web server 500' errors relating to Kibana.</description>
</rule>
```
```bash
# step 4
sudo so-ossec-stop
sudo so-ossec-start
```

## Whitelist a known-good IP address
### Netsniff-NG
1. Identify the IP address you want to whitelist
2. Use a text-editor to open `/etc/nsm/rules/bpf.conf`
3. Add the IP address using proper BPF format (along with a comment for continiuity)
4. Peform a manual rule update
```bash
# step 2
sudo vim /etc/nsm/rules/bpf.conf
```
```bash
# step 3
# IP address belongs to our vulnerability scanner
# Added by Victor on 22 JUN 2020
!(host 192.168.1.69)
```
```bash
# step 4
sudo so-rule-update
```

### OSSEC
1. Identify the IP address you want to whitelist
2. Use a text-editor to open `/var/ossec/rules/local_rules.xml`
3. Add the IP address using proper XML format (along with a comment for continiuity)
4. Peform a manual rule update
```bash
# step 2
sudo vim /var/ossec/rules/local_rules.xml
```
```html
<!-- Added by Victor on 25 JUN 2020 -->
<rule id="100777" level="0">
  <if_sid>5706, 5710, 5712</if_sid> <!-- Signature IDs for SSH login failures -->
    <srcip>192.168.1.23</srcip>
    <srcip>192.168.1.69</srcip>
    <description>Whitelist: Authorized analyst workstations.</description>
</rule>
```
```bash
# step 4
sudo so-rule-update
```

## Syntax for querying the Elasticsearch database
```bash
# search for a specific source ip
source_ip:192.168.1.69

# search for a specific source subnet
source_ips:192.168.1.*

# search for a specific ip address and log
source_ip:192.168.1.69 AND event_type:bro_dns

# search for an ip address and log type during a specific time period
# ex: find DNS logs between 0900 to 1700 (on 23 JUN 2020) with '192.168.1.69' as the query source
source_ip:192.168.1.69 AND event_type:bro_dns AND @timestamp:["2020-06-23T09:00" TO "2020-06-23T17:00"]
```

## Change the name of a sensor (a.k.a Salt Minion ID)
**Part 1 of 2**
1. Login to the sensor
2. Stop the `salt-minion` service on the sensor
3. Use a text-editor to open `/etc/salt/minion-id`
4. Change the minion ID 
5. Restart the `salt-minion` service on the sensor
```bash
# part 1: step 2
sudo service salt-minion stop
``` 
```bash
# part 1: step 3
sudo vim /etc/salt/minion-id
```
```
# part 1: step 4
# example old name = gecko-sensor1
# example new name = foxhound-sensor1
```
```bash
# part 1: step 5
sudo service salt-minion start
```
**Part 2 of 2**
1. Login to the master
2. List the currently accepted salt keys
3. Accept the new key from the previously modified sensor
4. Delete the old key from the previously modified sensor
5. Test your salt configuration
```bash
# part 2: step 2
sudo salt-key -L
```
```bash
# part 2: step 3
sudo salt-key -A
```
```bash
# part 2: step 4
sudo salt-key -d gecko-sensor1
```
```bash
# part 2: step 5
sudo salt '*' test.ping
```

## Update NIDS (Snort/Zeek) rules
1. Verify your engine and ruleset
2. Download the latest rules
3. Upload the latest rules to `/var/www/`
4. Modify `/etc/nsm/pulledpork.conf`
5. Perform a manual rule update
```bash
# step 1
# https://groups.google.com/forum/m/#!topic/security-onion/SyJSSYtZws0
sudo grep 'ENGINE' /etc/nsm/securityonion.conf
sudo grep -i 'ruleset' /var/log/nsm/sosetup.log 
sudo snort -V
sudo suricata -V
```
```bash
# step 2
# https://securityonion.readthedocs.io/en/latest/rules.html
```
```bash
# Emerging Threats Open ruleset: emerging.rules.tar.gz
# https://rules.emergingthreats.net/open/snort-2.9.0/emerging.rules.tar.gz
```
```bash
# Snort Community ruleset: community-rules.tar.gz
# https://www.snort.org/downloads/community/community-rules.tar.gz
```
```bash
# this can be done from Powershell
scp emerging.rules.tar.gz victor@foxhound-siem:~/
scp community-rules.tar.gz victor@foxhound-siem:~/
```
```bash
# step 3
sudo mkdir /var/www/rules/
sudo cp emerging.rules.tar.gz community-rules.tar.gz /var/www/rules/
```
```bash
# step 4
sudo vim /etc/nsm/pulledpork/pulledpork.conf
```
```bash
# rule_url=https://rules.emergingthreats.net/|emerging.rules.tar.gz|open
rule_url=https://localhost/rules/|emerging.rules.tar.gz|open
rule_url=https://localhost/rules/|community-rules.tar.gz|open
```
```bash
# step 5
sudo rule-update
```

## Disable a NIDS (Snort/Suricata) rule
1. Login to the Master
2. Identify the rule you want to disable
3. Use a text-editor to open `/etc/nsm/pulledpork/disabledsid.conf`
4. Add the rule using proper Snort/Suricata syntax (along with a comment for continiuity)
5. Peform a manual rule update
```bash
# step 2
sudo vim /etc/nsm/pulledpork/disabledsid.conf
```
```bash
# step 3
# Added by Victor on 29 JUN 2020
1:2008123
```
```bash
# step 4
sudo rule-update
sudo salt '*' cmd.run 'rule.update'
```

## Clearing alerts

## Creating a custom Kibana dashboard
