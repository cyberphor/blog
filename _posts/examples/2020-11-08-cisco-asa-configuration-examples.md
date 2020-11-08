---
layout: post
title: 'Cisco Adaptive Security Appliance (ASA) Firewall: Configuration'
category: examples
permalink: 'examples/cisco-asa'
---

### Port Mirroring
```bash
# switchport monitor source_port [tx | rx | both]

firewall(config)# interface ethernet 0/2
firewall(config-if)# switchport monitor ethernet 0/0
firewall(config-if)# switchport monitor ethernet 0/1
```

### References
* [Cisco Community: I want to know ASA support port mirror?](https://community.cisco.com/t5/network-security/i-want-to-know-asa-support-port-mirror/td-p/2061417)
