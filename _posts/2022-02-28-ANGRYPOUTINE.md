---
layout: post
title: 'ANGRYPOUTINE'
permalink: 'angrypoutine'
---

**Download PCAP**
[https://www.malware-traffic-analysis.net/2021/09/10/2021-09-10-traffic-analysis-exercise.pcap.zip](https://www.malware-traffic-analysis.net/2021/09/10/2021-09-10-traffic-analysis-exercise.pcap.zip)

**Executive Summary**  
On YYYY-MM-DD, at approximately HH:MM:SS UTC, an OPERATING_SYSTEM host used by FIRST_NAME LAST_NAME was infected with MALWARE_FAMILY malware.

**Victim Details**  
Username: TBD (frame #???)
Hostname: TBD (frame #???)
IP Address: TBD (frame #???)
MAC Address: TBD (frame #??)
Serial Number: n/a

**Indicators of Compromise**  
*Malicious Traffic*    
src.ip, src.port, dst.ip, dst.port, http.request.method, http.request uri
src.ip, src.port, dst.ip, dst.port, http.request.method, http.request uri
src.ip, src.port, dst.ip, dst.port, http.request.method, http.request uri

*File Hashes*  
Filename
SHA-256 Hash: TBD
File Size: TBD
File Location (URL, UNC, etc.): TBD
File Type: TBD
File Description: TBD

**Notes**  
Elaborate on the alerts that were raised, lessons learned, recommendations, etc. 
**Run the PCAP through the Suricata IDS.**  
```
mkdir suricata
cd suricata
suricata -r ../traffic.pcap
```

**What was the total number of alerts raised?**  
```
cat fast.log | wc -l
```
38

**How many unique alerts were raised?**  
```
cat fast.log | awk -F '\\[\\*\\*\\] ' '{print $2}' | sort | uniq
[1:2014520:7] ET INFO EXE - Served Attached HTTP 
[1:2018959:4] ET POLICY PE EXE or DLL Windows file download HTTP 
[1:2028765:2] ET JA3 Hash - [Abuse.ch] Possible Dridex 
[1:2033385:1] ET POLICY IP Check Domain (myexternalip .com in TLS SNI) 
[1:2260002:1] SURICATA Applayer Detect protocol only one direction
```
5

**How many times was each unique alert raised?**  
```
cat suricata/fast.log | awk -F '\\[\\*\\*\\]' '{print $2}' | sort | uniq -c | sort -r
     34  [1:2028765:2] ET JA3 Hash - [Abuse.ch] Possible Dridex 
      1  [1:2260002:1] SURICATA Applayer Detect protocol only one direction 
      1  [1:2033385:1] ET POLICY IP Check Domain (myexternalip .com in TLS SNI) 
      1  [1:2018959:4] ET POLICY PE EXE or DLL Windows file download HTTP 
      1  [1:2014520:7] ET INFO EXE - Served Attached HTTP
```

**Based on the summary of alerts, what do you think happened?**  
Someone downloaded and executed Dridex malware after browsing to a malicious website.

**What are the first three unique alerts raised?**  
```
cat fast.log | head -n3
09/10/2021-19:17:32.338290  [**] [1:2028765:2] ET JA3 Hash - [Abuse.ch] Possible Dridex [**] [Classification: Unknown Traffic] [Priority: 3] {TCP} 10.9.10.102:58132 -> 167.172.37.9:443
09/10/2021-19:17:29.191744  [**] [1:2018959:4] ET POLICY PE EXE or DLL Windows file download HTTP [**] [Classification: Potential Corporate Privacy Violation] [Priority: 1] {TCP} 194.62.42.206:80 -> 10.9.10.102:58131
09/10/2021-19:17:29.191744  [**] [1:2014520:7] ET INFO EXE - Served Attached HTTP [**] [Classification: Misc activity] [Priority: 3] {TCP} 194.62.42.206:80 -> 10.9.10.102:58131
```

**What is the rule criteria for the first alert raised?**  
```
sudo cat /var/lib/suricata/rules/suricata.rules | grep 2028765
alert tls $HOME_NET any -> $EXTERNAL_NET any (msg:"ET JA3 Hash - [Abuse.ch] Possible Dridex"; ja3_hash; content:"51c64c77e60f3980eea90869b68c58a8"; reference:url,sslbl.abuse.ch/ja3-fingerprints/; classtype:unknown; sid:2028765; rev:2; metadata:created_at 2019_10_14, former_category JA3, updated_at 2019_10_29;)
```
The first rule looks for the JA3 hash “51c64c77e60f3980eea90869b68c58a8.”

**Do any packets match the criteria of the alert raised?**  
```
tshark -r traffic.pcap -Y "ja3.hash == 51c64c77e60f3980eea90869b68c58a8" -T fields -e frame.number -e ip.src -e ip.dst -e ja3.hash
1487	10.9.10.102	167.172.37.9	51c64c77e60f3980eea90869b68c58a8
1845	10.9.10.102	94.158.245.52	51c64c77e60f3980eea90869b68c58a8
1863	10.9.10.102	94.158.245.52	51c64c77e60f3980eea90869b68c58a8
# …snipped…
```
Yes. A total of 34 frames match the rule’s criteria. 

**How did the victim know to contact the attacker?**  

**What is the victim’s username and hostname?**  

**What files were downloaded via HTTP?**  

**What were the types of files downloaded?**  

**Were the files executed?**  


