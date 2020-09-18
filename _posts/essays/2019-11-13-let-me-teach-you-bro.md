---
layout: post
title: 'Let Me Teach You: Bro'
permalink: 'let-me-teach-you-bro'
category: posts
subcategory: network-security-monitoring
---

Bro is an event-driven scripting engine and an intrusion detection system. It sniffs packets, decodes them from hexadecimal, and parses their protocol fields. If it notices anything specific or weird, it generates logs for the analyst to review. Bro’s namesake is a reference to the surveillance activities of “Big Brother,” a fictional character from the book 1984. In 2018, it was renamed to “Zeek” in honor of the hunting dog from the Far Side comics. In this blog post, I will demonstrate how to use the Bro, or Zeek, scripting language to help automate traffic analysis and threat hunting.

## Table of Contents
* Lab Setup
* How to Craft a Packet with Scapy
* How to Write a Zeek Signature
* How to Write a Zeek Script
* How to Parse Zeek Logs
* How to Sniff Packets with Zeek
* How to Correlate Network Events with Zeek
* References

## Lab Setup
First, download and deploy the Security Onion platform as a Virtual Machine (VM) to follow along. It ships with almost everything you will need to get started. For example, it already has Zeek, Scapy, tcpreplay, and Vim installed (I’ll explain what each of these tools do later). When invoked manually, Zeek will dump various logs to your current directory. Therefore, open a terminal, make a “working” or “temporary” directory, and change your location on the filesystem to it. This will help keep things clean and organized.

```
mkdir workbench
cd workbench
```

## How to Craft a Packet with Scapy
Scapy is a Python library used for manually crafting and manipulating packets. In this instance, a “defender” may wish to use it to emulate a known threat and test their intrusion detection capabilities. To fire-up a Python interpreter and have the library imported automatically, type `scapy` at the console.
```
scapy
```

In the example below, I created a Packet Capture, or PCAP, file containing a DNS query for the domain name, “www.malicious.life.” Although this website is for a cybersecurity podcast, it’s TLD (Top Level Domain) extension of “.life” is often associated with malware distribution points. To make the PCAP more realistic, I also specified “recursion is desired” (or set the Recursion bit to 1) in order to force the Google DNS server at `8.8.8.8` to recursively look for answer if it doesn’t have one itself.

```
ip = IP()
ip.src = "192.168.1.69"
ip.dst = "8.8.8.8"
udp = UDP()
udp.sport = 4444
udp.dport = 53
dns = DNS()
dns.rd = 1
dns.qd = DNSQR()
dns.qd.qname = "www.malicious.life"
packet = ip/udp/dns
packet.show()
wrpcap("dns-traffic.pcap", packet)
```

## How to Write a Zeek Signature
We now need to write a signature file so Zeek triggers on blacklisted TLDs like “.life.” First, open a text-editor. I prefer to use Vim.
```
vim dns-intel.sig
```

Next, identify the protocol, destination, and payload string we’re interested in. In the example below, I’m choosing to first label this signature “dns-intel” since blacklisted TLDs are often shared as “threat intelligence.” Ultimately, you can label it whatever you want, but understand, it will be key in the next step. I also used a Regex, or Regular Expression, pattern to identify multiple indicators of concern. In other words, I want Zeek to be on the look out for these extensions inside of any UDP traffic destined for port 53.
```
signature dns-intel {
	ip-proto == udp
	dst-port == 53
	payload /.*ooo|.*gdn|.*bar|.*work|.*life/
	event "[Suspicious DNS query] "
}
```

## How to Write a Zeek Script
As mentioned before, the real power behind Zeek is it’s protocol analyzers. They take stimulus and response functions found in most “network protocols” and present them to the script developer as individual “events.” For instance, a DNS query and a DNS response are both unique events you can leverage in a script. Another example is when a signature is matched. For this particular exercise, we’re going to have Zeek alert us (print something to the console) if it detects anything matching what we specified in our signature file.
```
vim dns-alert.bro
```

In the scripting context of Zeek, “network events” are objects with properties. We must ensure it understands every argument provided as input in order for each event to be processed correctly. For instance, the “signature_match” event includes three arguments: `state`, `msg`, and `data`. `state` is of the `signature_state` data type while `msg` and `data` are string data types. If we confused the two data types or supplied a bogus one, Zeek will fail at handling the event as desired.

Lastly, to access or manipulate the properties of an object (network event), we must use the $ symbol. In my script below, I compare the `state$sig_id` property of the `signature_match` event to the string `dns-intel`. Any event matching this criteria will cause Zeek to trigger and then, execute my code. To keep things easy to digest, my code shown here prints a dynamically formatted output using the `state$conn$dns$query` property (a.k.a the “domain name” in question).
```
event signature_match (state: signature_state, msg: string, data: string) {
	if (state$sig_id == "dns-intel") {
		print fmt ("[Suspicious DNS query] %s", state$conn$dns$query)
	}
}
```

## How to Parse Zeek Logs
Let’s daisy-chain all of the concepts covered thus far. The syntax below asks Zeek (identified as `bro`) to read our handmade PCAP and execute our script using the `dns-intel.sig` signature file as support.
```
bro -r dns-traffic.pcap -s dns-intel.sig dns-alert.bro
```
If you typed everything correctly, you should get output similar to what is shown below.
```
[ALERT] Suspicious DNS query: www.malicious.life
```
Now, list the contents of your current working directory. You should see a handful of `.log` files.
```
ls *.log
```
These logs are handy for network forensics and threat hunting. Although, beware, you must use a special Zeek tool called `bro-cut` to effectively extract and correlate interesting data points. As example, look at the `dns.log` using only the `cat` utility.
```
cat dns.log
```
Your output should be something similar to what is shown below. As you’ll see, it looks important, but it’s hard to read.
```
#separator \x09
#set_separator	,
#empty_field	(empty)
#unset_field	-
#path	dns
#open	2019-11-19-18-01-41
#fields	ts	uid	id.orig_h	id.orig_p	id.resp_h	id.resp_p	proto	trans_id	rtt	query	qclass	qclass_name	qtype	qtype_name	rcode	rcode_name	AA	TC	RD	RA	Z	answers	TTLs	rejected
#types	time	string	addr	port	addr	port	enum	count	interval	string	count	string	count	string	count	stringbool	bool	bool	bool	count	vector[string]	vector[interval]	bool
1573934403.323682	Cs9G761UH0Nh67wo0g	0.0.0.0	4444	8.8.8.8	53	udp	0	-	www.malicious.life	1	C_INTERNET	1	A	-	-	F	F	T	F	0	-	-	F
#close	2019-11-19-18-01-41
```
Execute the command sentence below to only look at the `#fields` row.
```
cat dns.log | head -n7 | tail -n1
```
The result will include the unique fields, or column headers, that exist in your log of interest.
```
#fields	ts	uid	id.orig_h	id.orig_p	id.resp_h	id.resp_p	proto	trans_id	rtt	query	qclass	qclass_name	qtype	qtype_name	rcode	rcode_name	AA	TC	RD	RA	Z	answers	TTLs	rejected
```
To cut-out and only focus on certain columns, you must use the bro-cut command. Let’s try it with the `ts`, `id.orig_h`, `id.orig_p`, and `query` columns. Include `-u` with `ts` to view timestamps in UTC.
```
cat dns.log | bro-cut ts id.orig_h id.orig_p query
```
Great. We know how to create a packet, a signature, and a script. We also see how `bro-cut` can help us parse a Zeek logs. Now, let’s try to use Zeek for sniffing packets off the network.
```
2019-11-16T20:00:03+0000	0.0.0.0	4444	www.malicious.life
```
