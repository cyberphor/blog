---
layout: post
title: 'Nmap & the Nmap Scripting Engine'
permalink: 'nmap-the-nmap-scripting-engine'
category: posts
subcategory: 'vulnerability-management'
---

“Network Mapper” (a.k.a “Nmap”) is tool frequently used for enumeration purposes during penetration testing. It was originally developed and is currently maintained by Gordon Lyon, a Network Security expert. One of it’s most notable features is “the Nmap Scripting Engine,” or “NSE” for short.

NSE is written in the Lua scripting language and extends the core functionality of Nmap’s network scanning capabilities. For example, the main Nmap executable identifies live nodes on the network, generating output that includes IP addresses, open ports, current versions of running services, and suspected operating systems in use. NSE allows one to go further by providing deeper analysis and even the exploitation of target systems. Each module, or NSE script, follows a standard format consisting of “a handful of descriptive fields, a rule defining when the script should be executed, and an action function containing the actual script instructions” [1]. Each “Field” provides metadata about the script itself. The “Rule” defines the conditions that must be met for successful script execution. Finally, the “Action” contains various operations performed when the script is invoked. One use-case is detecting systems vulnerable to the Microsoft Windows worm, “Conficker.” An example of the syntax to use would look like the following:

```bash
root@system # nmap --script smb-vuln-conficker.nse -p445 192.168.8.4
```

Although, on the topic of Nmap and enumerating information about a network, it is important to keep in mind systems typically have both TCP and UDP ports open. One must remember and act upon this in order to capture the most context during a penetration test or vulnerability assessment.

Nmap can be downloaded for free [here](https://nmap.org/download.html).

## References
* [Nmap Network Scanning: Chapter 9 - Nmap Scripting Engine](https://nmap.org/book/nse-script-format.html)
