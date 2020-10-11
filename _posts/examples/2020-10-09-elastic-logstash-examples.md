---
layout: post
title: 'Elastic Stack: Logstash'
category: examples
permalink: 'examples/elastic-stack/logstash'
---

### Table of Contents
* [Asset Inventory](#asset-inventory)
* [iptables](#iptables)
* [sudo](#sudo)
* [Windows Firewall](#windows-firewall)

### Asset Inventory
```
# WORK-IN-PROGRESS
# saved as 1999_asset_inventory.conf
filter {
  if ("syslog" in [tags]) {
    if ("source_ip" == "10.10.10.1") {
      add_tag => [ "asset" ]
      remove_tag => [ "_grokparsefailure" ]
      mutate {
        update => { "host" => "dc1.vanilla.sky.net" }
      }
    }
  }
}
```

### iptables
```
# saved as 6203_firewall_iptables.conf
filter {
  if ("syslog" in [tags]) and ("[iptables]" in [message]) {
    grok {
      pattern_definitions => {
        "IPTABLES_HEADER" => "\[%{WORD}\] %{WORD:action} %{WORD:reason}"
        "IPTABLES_BODY" => "%{GREEDYDATA} SRC=%{IP:source_ip} DST=%{IP:destination_ip}"      
        "IPTABLES_TRAILER" => "%{GREEDYDATA} PROTO=%{WORD:protocol} %{GREEDYDATA}"
        "IPTABLES" => "%{IPTABLES_HEADER} %{IPTABLES_BODY} %{IPTABLES_TRAILER}"
      }
      match => { "message" => "%{IPTABLES}" }
      add_tag => [ "iptables" ]
      remove_tag => [ "_grokparsefailure" ]
    }
    
    if ("iptables" in [tags]) {
      mutate {
        update => { "host" => "%{syslog-host}" }
        update => { "syslog-legacy_msghdr" => "firewall" }
        update => { "syslog-facility" => "iptables" }
        add_field => { "type" => "firewall" }
        remove_field => [ "[type][0]" ]
      }
    }
  }
}
```

### sudo
```
# saved as 6003_syslog_sudo.conf
filter {
  if ("syslog" in [tags]) and ("TTY" in [message]) {
    grok {
      pattern_definitions => {
        "SUDO_HEADER" => "%{WORD:user} : TTY=%{GREEDYDATA:terminal} ;"
        "SUDO_BODY" => "PWD=%{GREEDYDATA:directory} ; USER=%{GREEDYDATA:user} ;"
        "SUDO_TRAILER" => "COMMAND=%{GREEDYDATA:command} %{GREEDYDATA:arguments}"
        "SUDO" => "%{SUDO_HEADER} %{SUDO_BODY} %{SUDO_TRAILER}"
      }
      match => { "message" => "%{SUDO}" }
      add_tag => [ "sudo" ]
      remove_tag => [ "_grokparsefailure" ]
    }
    
    if ("syslog" in [tags]) and ("sudo" in [tags]) {
      mutate {
        update => { "host" => "%{syslog-host}" }
        update => { "syslog-legacy_msghdr" => "sudo" }
        update => { "syslog-facility" => "sudo" }
      }
    }
  }
}
```

### Windows Firewall
```
# saved as 6202_firewall_windows.conf
filter {
  if ("beat" in [tags]) and ("windows-firewall" in [tags]) {
    grok {
      pattern_definitions => {
        "WINDOWS_FIREWALL_HEADER" => "%{GREEDYDATA:date} %{GREEDYDATA:time} %{WORD:action} %{WORD:protocol} "
        "WINDOWS_FIREWALL_BODY" => "%{IP:source_ip} %{IP:destination_ip} %{INT:source_port} %{INT:destination_port} "
        "WINDOWS_FIREWALL_TRAILER" => "%{GREEDYDATA}" 
        "WINDOWS_FIREWALL" => "%{WINDOWS_FIREWALL_HEADER} %{WINDOWS_FIREWALL_BODY} %{WINDOWS_FIREWALL_TRAILER}"   
      }
      
      match => { "message" => "%{WINDOWS_FIREWALL}" }
      remove_tag => [ "_grokparsefailure" ]
    }
    
    if ("windows-firewall" in [tags]) {
      mutate {
        add_field => { "type" => "firewall" }
        remove_field => [ "[type][0]" ]
        #remove_field => [ "[beat][hostname]" ]
        #remove_field => [ "[beat][name]" ]
        #remove_field => [ "[beat][version]" ]
        #remove_field => [ "[offset]" ]
        #remove_field => [ "[prospector][type]" ]
      }
    }
  }
}

# SAMPLE LOG
# 2020-09-27 13:59:26 ALLOW TCP 192.168.3.12 192.168.3.10 47896 445 0 - 0 0 0 - - - RECEIVE

# FOR FUTURE USE
# YYYY-MM-DD HH:MM:SS
```

### References
* [Syspanda: Collecting and sending Windows Firewall Event logs to ELK](https://www.syspanda.com/index.php/2017/10/04/collecting-sending-windows-firewall-event-logs-elk/)
