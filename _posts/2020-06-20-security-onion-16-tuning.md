---
layout: post
title: 'Security Onion 16: Tuning'
category: guides
permalink: 'guides/security-onion/16/tuning'
---

## Table of Contents
* [Whitelisting in Netsniff-NG](#whitelisting-in-netsniff-ng)
* [Whitelisting in Snort/Suricata](#whitelisting-in-snort-suricata)
* [Whitelisting in OSSEC](#whitelisting-in-ossec)
* [Fine-tuning Snort/Suricata](#fine-tuning-snort-suricata)
* [Fine-tuning OSSEC](#fine-tuning-ossec)
* [Update NIDS rules](#update-nids-rules)
* [Disable a NIDS rule](#disable-a-nids-rule)
* [Elasticsearch queries](#elasticsearch-queries)
* [Change the name of a sensor](#change-the-name-of-a-sensor)

## Whitelisting in Netsniff-NG
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
!(host 192.168.1.69) &&
```
```bash
# step 4
sudo salt '*' cmd.run 'rule.update'
```

## Whitelisting in OSSEC
1. Identify the IP address you want to whitelist
2. Use a text-editor to open `/var/ossec/rules/local_rules.xml`
3. Add the IP address using proper XML format (along with a comment for continiuity)
4. Peform a manual rule update

```bash
# step 2
sudo vim /var/ossec/rules/local_rules.xml
```
{% highlight xml %}
<!-- Added by Victor on 22 JUN 2020 -->
<rule id="100777" level="0">
  <if_sid>5706, 5710, 5712</if_sid>
    <srcip>192.168.1.23</srcip>
    <srcip>192.168.1.69</srcip>
    <description>Whitelist: Authorized analyst workstations.</description>
</rule>
{% endhighlight %}  
```bash
# step 4
sudo so-rule-update
```

## Fine-tuning Snort/Suricata
#### Whitelist an IP Address for a Specific Rule
1. Identify the IP address you want to whitelist
2. Identify the SID of the rule you want to fine-tine
3. Use a text-editor to open `/etc/nsm/rules/local.rules` on your Master Node
    * Create a variable for the IP address
    * Copy/paste the rule with the variable 
4. Use `salt` to perform a manual rule update across the relevant sensor
5. Use `salt` to manually restart Snort/Suricata across the relevant sensor

```bash
# step 3
sudo vim /etc/nsm/rules/local.rules

# step 4
ipvar WHITELIST_SID_3000001 [192.168.1.23, 192.168.1.69, 192.168.1.86]
alert icmp !WHITELIST_SID_3000001 any -> any any (msg:"ICMP traffic!"; sid:3000001;)

# step 4
sudo salt 'foxhound-nids1' cmd.run 'so-rule-update'

# step 5
sudo salt 'foxhound-nids1' cmd.run 'so-nids-restart'
```

## Fine-tuning OSSEC
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

{% highlight xml %}
<!-- Added by Victor on 22 JUN 2020 -->
<rule id="100666" level="0">
  <if_sid>31120</if_sid>
  <description>Ignore 'Web server 500' errors relating to Kibana.</description>
</rule>
{% endhighlight %}  

```bash
# step 4
sudo so-ossec-stop
sudo so-ossec-start
```


## Update NIDS rules
1. Login to your Master Node
2. Use a text-editor to set `LOCAL_NIDS_RULE_TUNING` to `yes` in `/etc/nsm/securityonion.conf`
3. Verify your IDS engine and rule-set
4. Use a text-editor to specify your desired rule-sets in `/etc/nsm/pulledpork/pulledpork.conf`
5. Download the latest rule updates
6. Copy the rule updates to `/tmp/` on your Master Node 
7. Use `salt` to perform a manual rule update across all of your sensors

```bash
# step 2
sudo vim /etc/nsm/securityonion.conf
  LOCAL_NIDS_TUNING=yes
```
```bash
# step 3
sudo snort -V
sudo suricata -V
sudo grep -i 'engine' /etc/nsm/securityonion.conf
sudo grep -i 'ruleset' /var/log/nsm/sosetup.log 
```
```bash
# step 4
sudo vim /etc/nsm/pulledpork/pulledpork.conf
  rule_url=https://rules.emergingthreats.net/|emerging.rules.tar.gz|open
  rule_url=https://snort.org/downloads/community/|community-rules.tar.gz|Commmunity
```
```bash
# step 5
wget https://rules.emergingthreats.net/open/snort-2.9.0/emerging.rules.tar.gz
wget https://www.snort.org/downloads/community/community-rules.tar.gz
```
```bash
# step 6
sudo scp community-rules.tar.gz victor@foxhound-siem:/tmp/
sudo scp emerging.rules.tar.gz victor@foxhound-siem:/tmp/
```
```bash
# step 7
sudo salt '*' cmd.run 'so-rule-update'
```

## Disable a NIDS rule
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
sudo salt '*' cmd.run 'rule.update'
```

## Elasticsearch queries
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

## Change the name of a sensor
### Part 1 of 2  
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
```bash
# part 1: step 4
# example old name = gecko-sensor1
# example new name = foxhound-sensor1
```
```bash
# part 1: step 5
sudo service salt-minion start
```

### Part 2 of 2
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

### References
* https://groups.google.com/forum/m/#!topic/security-onion/SyJSSYtZws0
* https://groups.google.com/forum/#!topic/security-onion/oXdaQHVQ8-c
