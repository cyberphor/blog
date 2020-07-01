---
layout: post
title: 'Security Onion 16: Tuning'
permalink: 'security-onion-16-tuning'
category: notes
---

## Table of Contents
* [Fine-tuning OSSEC](#fine-tuning-ossec)
* [Whitelisting in Netsniff-NG](#whitelisting-in-netsniff-ng)
* [Whitelisting in OSSEC](#whitelisting-in-ossec)
* [Elasticsearch queries](#elasticsearch-queries)
* [Change the name of a sensor](#change-the-name-of-a-sensor)
* [Update NIDS rules](#update-nids-rules)
* [Disable a NIDS rule](#disable-a-nids-rule)

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
sudo so-rule-update
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
```xml
<rule id="100777" level="0">
  <if_sid>5706, 5710, 5712</if_sid>
    <srcip>192.168.1.23</srcip>
    <srcip>192.168.1.69</srcip>
    <description>Whitelist: Authorized analyst workstations.</description>
</rule>
```
{% endhighlight %}
```bash
# step 4
sudo so-rule-update
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
```bash
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

## Update NIDS rules
1. [Everytime] Login to the "Master"
2. [The first time only] Create the directory `/var/www/rules/`
3. [The first time only] Use a text-editor to modify `/etc/nsm/pulledpork.conf`
4. [The first time only] Use `salt` to modify `/etc/hosts` across all of your nodes
5. [Everytime] Verify your engine and ruleset
6. [Everytime] Download the latest rules (ex: onto disc)
  * [All rulsets](https://securityonion.readthedocs.io/en/latest/rules.html)
  * [Emerging Threats Open ruleset](https://rules.emergingthreats.net/open/)
  * [Snort Community ruleset](https://www.snort.org/downloads/community/)
7. [Everytime] Upload the latest rules to the "Master"
8. [Everytime] Copy the latest rules to `/var/www/rules/` on the "Master"
9. [Everytime] Perform a manual rule update 
```bash
# step 2
sudo mkdir /var/www/rules/
```
```bash
# step 3
sudo vim /etc/nsm/pulledpork/pulledpork.conf
  rule_url=https://rules.emergingthreats.net/|emerging.rules.tar.gz|open # keep
  rule_url=https://localhost/rules/|emerging.rules.tar.gz|open # add
  rule_url=https://localhost/rules/|community-rules.tar.gz|open # add
```
```bash
# step 4
sudo salt '*' cmd.run "echo '127.0.0.1 rules.emergingthreats.net' | sudo tee -a /etc/hosts"
sudo salt '*' cmd.run "cat /etc/hosts"
```
```bash
# step 5
sudo grep -i 'ruleset' /var/log/nsm/sosetup.log 
sudo grep -i 'engine' /etc/nsm/securityonion.conf
sudo snort -V
sudo suricata -V
```
```bash
# step 7
scp emerging.rules.tar.gz victor@foxhound-siem:~/
scp community-rules.tar.gz victor@foxhound-siem:~/
```
```bash
# step 8
sudo cp emerging.rules.tar.gz /var/www/rules/ 
sudo cp community-rules.tar.gz /var/www/rules/
```
```bash
# step 9
sudo salt '*' cmd.run 'rule-update'
sudo salt '*' cmd.run 'wc -l /etc/nsm/rules/downloaded.rules'
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
sudo rule-update
sudo salt '*' cmd.run 'rule.update'
```

### References
* https://groups.google.com/forum/m/#!topic/security-onion/SyJSSYtZws0
